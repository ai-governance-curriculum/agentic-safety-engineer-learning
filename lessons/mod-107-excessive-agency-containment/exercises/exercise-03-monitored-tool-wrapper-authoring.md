# exercise-03: Monitored Tool Wrapper Authoring

**Estimated effort:** 2 hours

## Objective

Author a **defensible monitored wrapper** for one specific tool from your exercise-01 EACC. The wrapper must implement chapter 03's five responsibilities — argument diffing, outcome verification, wrapper-side policy enforcement, audit-log emission, and tamper-evident action logging — and must pass a small red-team suite you author alongside it.

You are not building a general-purpose wrapper framework. You are producing a single wrapper for a single tool, in a shape a reviewer could point at as the reference implementation.

## Prerequisites

- Read chapter 03 (Monitored Tool Wrappers) end-to-end.
- Skim [RFC 6962 (Certificate Transparency)](https://www.rfc-editor.org/rfc/rfc6962) — the Merkle-tree pattern the tamper-evident log uses. Optionally [Trillian](https://github.com/google/trillian) for a reference implementation.
- Skim [NIST SP 800-92](https://csrc.nist.gov/publications/detail/sp/800-92/final) for the audit-log posture.
- Have exercise 01 completed, or a candidate EACC section 1 with at least one tool defined.
- A programming language you are comfortable with. Any is fine; Python / TypeScript / Go / Rust are conventional.

## Requirements

### Part A — Pick the tool

Pick one tool from your EACC. Prefer a side-effecting tool over a read-only one — the interesting behaviour of a wrapper lives on the write side. Reasonable choices for a paper deployment:

- `refund_issue(order_id, amount_cents, reason)` — a customer-support refund tool.
- `send_email(to, subject, body)` — a support-agent email tool.
- `sql_execute(template_id, params)` — a parameterised query tool.
- `ticket_close(ticket_id, resolution)` — a helpdesk-workflow tool.
- `file_move(src, dst)` — a filesystem-managing tool.

Record: the tool ID, the EACC section-1 entry, the downstream target, the credential contract (section 4), the blast-radius cap (section 5).

### Part B — Implement the five wrapper responsibilities

Structure the code as one module per responsibility so a reviewer can inspect each independently.

**Responsibility 1 — Argument diffing (canonicalisation)**

- Normalise arguments: whitespace, unicode NFC, JSON key order, URL / email normalisation, numeric normalisation.
- Resolve every reference argument (`order_id`, `user_id`, etc.) to the underlying record; store the record's fingerprint alongside the raw arg.
- Tag every argument with its provenance (`user_message`, `retrieved_document`, `prior_tool_output`, `system`, `model_composed`). If your test-harness cannot supply real provenance, simulate it via the caller signature and document the simulation.
- Emit the **canonical form** as a stable, hashable structure. Compute its SHA-256 fingerprint.
- Compute the **diff** — `state_before`, `predicted_state_after`, `delta`.

**Responsibility 2 — Outcome verification**

- After the tool invocation, re-read the target record and compute the `actual_state_after`.
- Compare `actual_state_after` against `predicted_state_after`. Report `matches`, `widened` (side-effect superset), `narrowed` (nothing happened), or `spoofed` (the tool's return does not match reality).
- On `widened` / `spoofed`, quarantine the result and refuse to hand it back to the model until a HITL review completes (simulate the HITL callback for this exercise).

**Responsibility 3 — Wrapper-side policy enforcement**

- Execute the chapter-01 gate pipeline in order: allow-list, schema, value, rate, budget, resource-resolution, scope, credential mint, blast-radius pre-check.
- Read the policy from an externalised, hashed config file. Log the policy hash at wrapper start-up.
- Every gate step returns a structured verdict; every refusal is a structured error the model can observe *and* an audit-log entry.
- Fail-closed on partial failure — the wrapper does not fall back to cached policy on a broker outage.

**Responsibility 4 — Audit-log emission**

- Emit a structured audit entry per invocation with: request identity (session, principal, tenant), tool identity (ID, version, wrapper version, policy hash), request payload (raw + canonical hash + provenance), gate verdicts (per step), sandbox telemetry (if applicable), credential principal, downstream request-ID, outcome (tool return + verification verdict), HITL context (if applicable), and monotonic timestamp.
- Write to two sinks — the tamper-evident stream (Part 5) and an operational observability sink (stdout or a structured-log library is fine).

**Responsibility 5 — Tamper-evident action log**

- Hash-chain the entries (each entry carries `prev_hash`).
- Periodically compute a Merkle root over a batch of entries and publish it to a **separate** sink (a second file, a WORM-simulating directory, or an actual object-storage bucket with immutability enabled).
- Sign the batch root via a signing function whose key material is **outside** the wrapper's process (a separate process, a KMS emulator, or an environment-variable-scoped subprocess — the point is to demonstrate the *separation of concerns*, not to build a production KMS).
- Provide a `verify()` command that walks a chain from head to root and reports any tampering.

### Part C — Red-team the wrapper

Author a small suite of tests that a reviewer would run to break the wrapper:

- `test-canonical-01` — call the tool with structurally identical arguments in three different formattings; assert the canonical form's hash is identical.
- `test-policy-01` — send a call that violates the allow-list; assert the audit entry records the refusal *before* returning.
- `test-policy-02` — send a call that violates the scope (resolved target's tenant ≠ session tenant); assert the wrapper refuses even if the model composed a plausible-looking `tenant_id`.
- `test-verification-01` — mock the tool to return `success` while actually not mutating the target; assert the wrapper flags `narrowed` / `spoofed`.
- `test-verification-02` — mock the tool to mutate more than predicted; assert the wrapper flags `widened` and quarantines.
- `test-log-01` — tamper with a historical audit entry; assert `verify()` detects the modification and identifies which entry.
- `test-log-02` — tamper with the signing key or replay a stale batch root; assert the verifier detects.
- `test-failclosed-01` — take the policy broker offline (mock a timeout); assert the wrapper refuses the next call rather than using cached policy indefinitely.

All tests pass with the current code. Every failing test is a wrapper finding.

## Deliverables

Commit to your exercise-solution area:

- `wrapper/` — the five-responsibility implementation.
- `tests/` — the red-team suite.
- `policy/` — the externalised, hashed EACC-derived policy for the chosen tool.
- `log/` — sample audit-log stream and Merkle roots from a run.
- `verify` — the log-verification script.
- `README.md` naming the tool, the runtime, and a one-paragraph summary of what a reviewer should look at first.

## Acceptance criteria

- **All five responsibilities are visible in the code layout** — one module per responsibility.
- **Canonical form is deterministic.** Two structurally identical calls hash identically; a diff in whitespace / key order / unicode form does not change the hash.
- **Every refusal is audit-logged with the specific gate step and the specific argument that triggered it.**
- **Policy is externalised and hashed.** The wrapper logs the policy hash at start-up; drift is a build-time failure.
- **The signing key is not in the wrapper's process.** Document how you separated it.
- **`verify()` detects at least the two tamper cases in the red-team suite.**
- **The wrapper fails-closed on partial policy-broker failure.** Test `test-failclosed-01` demonstrates it.
- **The audit log is complete enough to reconstruct the call end-to-end** from the log alone — a mod-108 monitor could classify from the entry without needing anything else.

## Stretch goals

- **Diff-based auto-approval.** Author a pre-authorised diff pattern for a class of calls; the wrapper auto-approves matches and escalates non-matches. Include a pattern-fingerprint that the EACC pins.
- **Retrospective outcome verification.** For non-idempotent tools where the wrapper cannot dry-run, implement a delayed re-read + reconciliation path: the wrapper records the prediction, invokes, re-reads at a later checkpoint, and flags any drift to mod-108's monitor.
- **MCP integration.** Wrap a real [MCP-server](https://modelcontextprotocol.io/) tool — a filesystem or a HTTP-fetch server — with your wrapper. Report which MCP security-considerations the wrapper closes and which it does not.
- **Merkle proof export.** Add a command that emits an inclusion proof for a specific audit entry — the shape a downstream disclosure (mod-112) or a customer dispute would consume.
- **Chaos test.** Kill the wrapper mid-call (SIGKILL between resolution and invocation, between invocation and log commit). Assert the audit stream and the tool's downstream converge on a consistent state — no orphaned side effects with no log entries.

## Guardrails

- **Do not connect to real downstream APIs.** Mock them. The exercise verifies the wrapper's shape, not the downstream's behaviour.
- **Do not commit real credentials.** Every credential in the exercise is a placeholder; the broker in this exercise is a stand-in.
- **Do not depend on production-grade WORM storage** — an append-only file plus a `chattr +i`-style protection is sufficient to demonstrate the *shape*. In production, the WORM sink is a peer-role artefact.
- **Do not roll your own crypto for the tamper-evident log.** Use a vetted hash (SHA-256) and a vetted signing library. The point is composition, not novel crypto.
- **If the tool the wrapper wraps is real, do not run the wrapper against production.** Use a staging tenant with no real customer data.
