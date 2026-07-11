# 03 — Monitored Tool Wrappers

## Motivation

Chapter 01 built the capability-gate pipeline. Chapter 02 built the sandbox that contains what the gate admitted. Both are useless unless *something* runs them for every tool call the agent makes. The thing that runs them — and that composes the gate, the sandbox, the audit stream, and the escalation flow into a single call boundary — is the **monitored tool wrapper**.

A wrapper is not middleware. Middleware is a chain of transformations. A wrapper is the *policy-enforcing execution shell* around one tool. It answers a specific question the OWASP GenAI Agentic Threats paper keeps returning to: *when the model asks the tool to do something, whose code checks that the ask matches the intent?* The answer, in this discipline, is: not the model's, not the tool implementation's, not the downstream API's — the *wrapper's*. Policy in the wrapper, not in the model.

The load-bearing insight is that the model is *not the policy engine*. The system prompt that says "only send emails to users' verified addresses" is a hint. The model can be convinced (by direct injection, indirect injection, roleplay, jailbreak) to ignore it. The wrapper cannot. The wrapper reads the session's authenticated principal, resolves the tool's target, runs the value-level validators, mints the scoped credential, records the argument-provenance, invokes the tool inside the sandbox, verifies the outcome against the intent, and emits the tamper-evident audit-log entry. Every one of those steps is code that runs *regardless of what the model says* and *before the model observes the result*.

This chapter walks the wrapper's five load-bearing responsibilities: **argument diffing** (canonicalising and comparing the tool call to prior state), **outcome verification** (checking the tool's return matches the wrapper's own reconstruction), **wrapper-side policy enforcement** (the gate pipeline from chapter 01, executed here, not in the model), **audit-log emission** (structured, complete, machine-readable), and **tamper-evident action logs** (integrity guarantees the log itself must satisfy).

## Primary sources

- **[OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)** — the Repudiation & Untraceability and Tool Misuse entries name what an unmonitored wrapper allows.
- **[OWASP Top 10 for LLM Applications 2025 — LLM06: Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/)** — the excessive-autonomy sub-failure is what wrapper-side policy enforcement prevents.
- **[NIST SP 800-92 — Guide to Computer Security Log Management](https://csrc.nist.gov/publications/detail/sp/800-92/final)** — the reference posture for security-relevant logging; the audit-log integrity requirements below cite it.
- **[RFC 6962 — Certificate Transparency](https://www.rfc-editor.org/rfc/rfc6962)** — the Merkle-tree log pattern that gives us tamper-evidence at low cost. Adapted below for tool-action logs.
- **[Model Context Protocol (MCP) — specification](https://modelcontextprotocol.io/)** — the wire protocol many tool-using agents standardise on. Read the security-considerations sections; the wrapper is what implements them on the operator side. <!-- needs-research: pin the current published MCP spec version and any published security-hardening notes / erratas in the wrapper artefact. -->

## The wrapper's contract

A monitored wrapper is a single call boundary that owns the following contract for one specific tool:

1. **Receive** a structured tool-call request from the agent runtime, carrying: session ID, principal, tool ID, tool version, arguments, argument provenance.
2. **Canonicalise and diff** the request: normalise argument shapes; compute the *canonical form* used for audit and HITL rendering; diff against the session's prior state (the *what changes if we run this?* view).
3. **Enforce the gate pipeline** from chapter 01: allow-list, schema, value, rate, budget, scope, credential mint, blast-radius pre-check, HITL routing.
4. **Invoke** the tool inside the chapter-02 sandbox (for sandboxed tools) or against the downstream (for non-sandboxed API tools) with the minted credential.
5. **Verify the outcome** against the wrapper's own predicted state, and against the tool's own returned invariants.
6. **Emit an audit-log entry** that records the request, the gate verdicts, the sandbox telemetry, the outcome, and the wrapper's verification result.
7. **Return** a structured response to the runtime — either the tool's output (post-sanitisation) or a structured refusal the model can observe.

If any step fails-closed, the call ends there, the audit log records the failure, and the model observes a structured error. There is no path where a step is skipped silently.

## Wrapper responsibility 1 — Argument diffing

### What it means

Before the wrapper enforces the gate, it produces a **canonical form** of the call and diffs it against the session's known state. Canonicalisation strips presentation variability so two structurally identical calls with different formatting produce the same hash; the diff reveals what the call would change if it ran.

Canonicalisation shapes:

- **Argument normalisation.** Whitespace normalisation on free-form strings; unicode NFC; email address lowercasing; URL normalisation (host lowercase, default-port stripped, path segment decoding); JSON key-sorted; numeric normalisation (trailing zeros, scientific notation).
- **Reference resolution.** Object references (`order_id`, `user_id`, `document_id`) resolved to the underlying record's *stable* identity, plus a fingerprint of the record's current state. `order_id="ord_abc"` becomes `{order_id: "ord_abc", tenant: "acme", owner: "u_...", status: "shipped", amount_cents: 1500}` in the canonical form.
- **Argument-provenance tagging.** Every argument is tagged with its source: `user_message`, `retrieved_document`, `prior_tool_output`, `system`, `model_composed`. Attacker-controllable sources are the highest-scrutiny inputs.

### Why the diff matters

The diff is what the HITL flow (chapter 04) renders. A refund `$15.00 → order ord_abc123 → user u_...` is legible; a summary "the agent thinks a refund is warranted" is not. mod-108's monitors classify the diff; the tamper-evident log stores the diff; the wrapper's own gate uses the diff to decide whether the call is auto-approved or conditional.

A diff has three legs:

- **State before.** The relevant slice of the world before the call: the target record's fields, the target account's balance, the session's budget counters.
- **Predicted state after.** What the wrapper thinks will change: this specific field, this specific balance, this specific counter.
- **Delta.** The specific change, in a canonical shape: `{field: "status", from: "shipped", to: "refunded"}`, `{field: "balance", from_cents: 12500, to_cents: 11000, delta_cents: -1500}`.

The delta is the load-bearing argument for chapter 04's HITL rendering and mod-108's monitor classification.

### Diff-based auto-approval

A useful pattern: some tool calls are pre-authorised on their diff shape, not their raw arguments. "Refund up to $50 on a shipped-order record whose owner is the session's user" is a diff pattern; when the diff matches the pattern, the wrapper auto-approves. Otherwise it escalates. This turns the EACC section-6 escalation contract into a *pattern-matching* problem the wrapper can execute deterministically.

## Wrapper responsibility 2 — Outcome verification

### What it means

After the tool runs, the wrapper compares the *actual outcome* — the tool's return value, the downstream API's side-effect confirmation, the sandbox's telemetry — against the *predicted outcome* from the diff, and refuses to hand back a result that does not match.

Two failure modes it catches:

- **The tool did more than it claimed.** The `refund_issue` tool returned `{status: "ok", refund_id: "..."}` but the downstream also cancelled the shipping label. The wrapper's outcome check re-reads the target record and diffs the *actual* state against the *predicted* state. When the actual delta is a superset of the predicted delta, the wrapper flags the call as *side-effect-widened* and routes to the escalation flow *retrospectively* (the call happened; the response is quarantined until a human reviews).
- **The tool did less than it claimed.** The tool returned success, but the target record is unchanged. This is a symptom of a compromised or spoofed tool. The wrapper flags the call and does not return `success` to the model.

### Verification is not always possible pre-invocation

For non-idempotent side effects, the wrapper cannot always dry-run. When it cannot, it *records the prediction as a claim*, invokes the tool, re-reads the target, and stores both. Discrepancies feed mod-108's monitors and mod-112's disclosure flow. This is the containment side of the *"detect what you cannot prevent"* posture.

### Common failure the reviewer catches

The wrapper trusts the tool's return value. The tool returns `{status: "ok", records_changed: 1}`; the wrapper reports success; the actual number of records changed is 4 000. The reviewer asks whether the wrapper *re-reads* the store or *trusts* the tool. Trusting the tool is a finding; re-reading is the correct posture.

## Wrapper responsibility 3 — Policy enforcement, in the wrapper, not in the model

The gate pipeline from chapter 01 lives in the wrapper. This section pins the invariants that separate a wrapper-policy posture from a prompt-policy posture.

### Invariant A — The policy code path is not the model's code path

The model observes tool schemas and tool outputs; it does not observe policy code. The wrapper decides. If the model wants to know why a call was refused, it reads a structured refusal object; it does not read the policy engine's source.

### Invariant B — Every refusal is logged before it is returned

A silent refusal is a bypass channel. The audit log records every refusal — with the specific gate step that fired, the specific reason, and the specific argument that triggered the fire — before the wrapper returns the refusal to the model. If the audit-log emission fails, the wrapper fails-closed on the call.

### Invariant C — Policy configuration is externalised, versioned, and hashed

The EACC (chapter 01) is the versioned policy artefact. The wrapper reads its policy configuration from a build-time-fetched artefact whose hash is logged on wrapper start-up. A drift — the wrapper is running one hash and the reviewer's approved EACC has a different hash — is an operational incident.

### Invariant D — Policy cannot be updated by the model

There is no tool the model can call that mutates the policy configuration. The policy is *out-of-band*: an operator (or an on-call safety engineer) updates the EACC through a code path with its own approval flow, not through the agent. The kill switch (chapter 05) is *not* an EACC update; it is a runtime feature flag that shortcuts every gate to *refused*.

### Invariant E — Wrapper failure is fail-closed, not fail-open

If the gate pipeline itself errors (the credential broker is unreachable, the argument validator throws), the wrapper returns a structured refusal, records the failure, and does not fall back to invoking the tool with reduced checks. Fail-open under partial failure is one of the classical excessive-agency-in-production failure modes.

### Common failure the reviewer catches

The wrapper "handles" a policy-engine timeout by invoking the tool with the *last-known-good* policy from cache. The reviewer asks what the tool does when the policy engine is *permanently* down; the answer should be "refuse," not "use the cache indefinitely."

## Wrapper responsibility 4 — Audit-log emission

### What must be recorded

A defensible audit-log entry for a single tool call carries:

- **Request identity.** Session ID, run ID, agent ID, agent role, principal (authenticated user), tenant.
- **Tool identity.** Tool ID, tool version, wrapper version, policy hash, sandbox class + profile hash.
- **Request payload.** Raw arguments (with size-capped body), canonical form hash, argument provenance per argument.
- **Gate verdicts.** Per-step verdicts of the gate pipeline (allowed / refused, reason). Include even the steps that passed — the reviewer's question is often *"did this step run at all?"*
- **Sandbox telemetry.** Syscall counts (for sandboxed tools), egress-proxy log reference (chapter 02), cgroup high-water marks, wall-clock, exit reason.
- **Invocation identity.** Credential principal (the identity the wrapper minted, not the operator platform credential), downstream request-ID (if the downstream returns one).
- **Outcome.** Tool return value hash (with size-capped body), verified-state diff, verification verdict (matches / widened / narrowed / spoofed).
- **HITL context.** If the call escalated, which HITL flow, which approver, which decision, timestamp.
- **Time.** Monotonic wall-clock, ideally with a trusted timestamp (NTS-authenticated NTP or, for the highest-integrity streams, an RFC 3161 timestamp).

Do *not* record: the model's raw system prompt (unless the deployment's disclosure posture explicitly permits it), plaintext secrets, or the user's PII beyond what the tool call itself carries.

### Log routing

The wrapper writes to two sinks:

- **A tamper-evident append-only stream** (see responsibility 5) — the load-bearing evidence for mod-109 safety cases, mod-108 monitors, mod-111 red-team analysis, mod-112 disclosure.
- **An operational observability sink** (traces, spans, metrics) — engineer-facing, indexed by session ID.

The two sinks are consistent by construction: the tamper-evident stream is the authoritative record; the observability sink is a lossy view of it.

### Common failure the reviewer catches

The audit log is written to the same store the tool can reach (a shared database, an object store the tool can list). A reviewer asks whether a compromised tool could rewrite its own audit entry; the answer must be *no*, and the log-storage posture must justify it.

## Wrapper responsibility 5 — Tamper-evident action logs

Ordinary logs are append-only *by convention*. A tamper-evident log is append-only *by cryptographic construction*. Any subsequent modification is detectable by a party with a small piece of the log's later state.

### Two useful patterns

**Hash-chained log.** Each entry carries the hash of the previous entry. A verifier who trusts the current head can walk backwards and detect any modification. Cheap, simple, and sufficient for the on-operator side of the trust boundary.

**Merkle-tree log.** Entries are the leaves of a growing Merkle tree; the tree's root is periodically published to an authenticated stream (a WORM object store, a peer service, an internal transparency log). This is the RFC 6962 Certificate-Transparency pattern. Higher-integrity than a chain: a verifier can *audit* the log without downloading all of it, and can *gossip* the roots to detect divergent views.

For a level-40 containment posture, hash-chained per-session with periodic Merkle-root pinning to a separate WORM store is defensible. The pinning cadence is a section-7 EACC field.

### Integrity requirements

- **The signing key is not reachable from the wrapper's execution environment.** The wrapper signs entries via a signing service (a KMS, an HSM-backed API); the wrapper does not hold the key.
- **The verifier's material is separate.** A monitoring role (mod-108) holds the verifier keys, checks the chain continuity and the Merkle-root pins, and alerts on divergence. The wrapper does not verify itself.
- **The log stream survives operator compromise.** The WORM sink (S3 Object Lock, immutable Azure Blob, GCS Bucket Lock, an on-prem WORM appliance) has a retention policy that outlasts the incident-response window.

### The log is the artefact behind repudiation

The OWASP Agentic Threats entry on Repudiation & Untraceability treats missing or incomplete logs as its own class of threat. Every action a customer disputes, every action a regulator asks about, every action that could be the subject of an incident-response conversation (chapter 05) is answered from this log. Design accordingly.

### Common failure the reviewer catches

The log entries are signed but the signing key lives *in the wrapper's process*. A compromised wrapper writes valid signatures over false entries. The reviewer asks where the key lives; the answer must be *not in the wrapper*.

## Bringing it together — the wrapper's pseudo-lifecycle

```
def invoke(request):
    entry = start_audit_entry(request)                    # 4
    try:
        canonical, diff = canonicalise_and_diff(request)  # 1
        entry.canonical = canonical
        entry.diff = diff

        for step in GATE_PIPELINE:                        # 3 + chapter 01
            verdict = step(request, canonical, diff)
            entry.gate_verdicts.append(verdict)
            if verdict.refuse:
                return refuse(entry, verdict)
            if verdict.escalate:
                decision = escalate_to_hitl(entry)        # chapter 04
                entry.hitl = decision
                if decision.deny:
                    return refuse(entry, decision.reason)

        cred = credential_broker.mint(request)            # chapter 01 primitive 4
        with sandbox(request) as box:                     # chapter 02
            result = box.invoke(request, cred)
            entry.sandbox_telemetry = box.telemetry

        verification = verify_outcome(canonical, diff, result)   # 2
        entry.verification = verification
        if verification.mismatch:
            return quarantine(entry, result)

        return sanitise_and_return(entry, result)
    except Exception as exc:
        entry.error = repr(exc)
        return refuse(entry, "wrapper_error")
    finally:
        commit_audit_entry(entry)                         # 4 + 5
```

Every branch commits an audit entry. Every entry is signed against the tamper-evident log. Every refusal is structured. This is the shape exercise 03 asks you to implement for a small tool of your choice.

## Interfaces to the rest of the module

- **Chapter 01 (gates).** The gate pipeline the wrapper executes. The EACC is the wrapper's configuration.
- **Chapter 02 (sandbox).** The wrapper opens the sandbox for the invocation step and consumes its telemetry.
- **Chapter 04 (HITL).** The wrapper hands `conditional` verdicts (and `blast-radius-widened` outcomes) to the HITL flow; the HITL flow returns a signed decision that becomes part of the audit entry.
- **Chapter 05 (kill switch).** The wrapper checks the kill-switch state at the *first* gate step; a firing kill switch shortcuts every subsequent step to *refused*.
- **Chapter 06 (boundaries).** The wrapper is the safety-engineering artefact; the signing service, the KMS, the WORM store, the workload identity fabric are `ai-infra-security`'s scope.
- **mod-108 (monitors).** Monitors *consume* the wrapper's log stream; they do not sit inline. mod-107 owns the wrapper; mod-108 owns the classifier that reads its output.
- **mod-109 (safety cases).** The wrapper's log stream is the evidence backing a *"the runtime enforced the EACC"* claim in a safety case.
- **mod-111 (scaled red team).** The red-team harness *targets* the wrapper's gate pipeline directly; findings feed EACC updates.

## Common misreadings to avoid

- **"Policy in the system prompt is easier."** It is easier. It also does not work — the model can be convinced to ignore it. Policy in the wrapper is harder to write but cannot be convinced.
- **"The wrapper does not need to canonicalise; the model gave us JSON."** Two structurally identical calls with different whitespace, different key order, or different unicode normalisation will hash differently and defeat every replay-detection, audit-comparison, and diff-based auto-approval you build. Canonicalise.
- **"The audit log is a downstream concern."** No. The log is the evidence artefact for every downstream role. If the wrapper does not emit it correctly, mod-108 cannot monitor, mod-109 cannot cite, mod-112 cannot disclose, and the customer's incident-response conversation has no ground truth.
- **"Signing the log inside the wrapper is fine."** A compromised wrapper produces valid signatures over false entries. The signing key does not live in the wrapper.
- **"Outcome verification adds a duplicate read on every call."** Yes. That is the cost. For high-blast-radius calls it is worth it; for read-only search calls the wrapper can pattern-match its way out of the verification. The EACC section-6 specifies which calls verify and which do not.

## Summary

- The wrapper is where policy runs. Not the model, not the tool, not the downstream — the wrapper.
- Five responsibilities: **argument diffing** (canonicalise; diff against state), **outcome verification** (re-read the world after the call), **policy enforcement** (execute the chapter-01 gate pipeline; fail-closed; policy is externalised), **audit-log emission** (structured, complete, machine-readable), **tamper-evident action logs** (hash-chained + Merkle-pinned; signing key out of the wrapper).
- The wrapper composes the gate (chapter 01), the sandbox (chapter 02), the HITL flow (chapter 04), and the kill switch (chapter 05). It is the single call boundary the reviewer inspects.
- Exercise 03 asks you to author a defensible wrapper for one specific tool, including its audit-log entry shape, its canonicalisation contract, and its verification loop.
