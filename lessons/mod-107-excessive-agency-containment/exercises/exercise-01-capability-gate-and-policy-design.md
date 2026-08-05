# exercise-01: Capability Gate and Policy Design

**Estimated effort:** 2 hours

## Objective

Author sections 1–5 of an **Excessive-Agency Containment Contract (EACC)** end-to-end for one concrete agent deployment, and *review it as though you were a peer safety reviewer*. The output is a versioned EACC artefact that a reviewer at level 40 would sign, plus a short review memo naming three specific ways the contract is under-gated and one specific way it may be over-gated.

This exercise anchors chapter 01 in practice. The remaining exercises fill sections 6 (chapter 04) and 7 (chapters 02 and 05).

## Prerequisites

- Read chapter 01 (Capability Gates and Policy Design) end-to-end.
- Skim [OWASP LLM06: Excessive Agency (2025)](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/) and the [OWASP GenAI Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/) entries for Tool Misuse, Privilege Compromise, and Resource Overload.
- Pick a target deployment. Either:
  - A real deployment you have documented access to (coordinate with the owning team before submitting anything internally), *or*
  - A **paper deployment** — a written description of the agent's purpose, tools, principals, and downstream systems, with enough detail that another engineer could review the EACC without asking questions.
- Identify the paired `senior-agentic-ai-engineer` peer artefact you would cite for the tool inventory and the `ai-infra-security` peer artefact you would cite for the credential broker. If none exists, note that as a "requires peer-role input" cite.

## Requirements

Author a pre-registration document `EACC.md` (or `EACC.yaml`) with the following sections. Every field must be filled — either with a concrete choice, a specific "waived because …" reason, or a specific "requires peer-role input" cite. TBDs are not accepted.

### Section 1 — Tool inventory and allow-list

For every tool the agent may invoke, record:

- **Tool ID** and **version pin** (immutable identifier plus the specific version this EACC ratifies).
- **One-line intent statement.** What this tool does, for whom, in the language of the business's existing consent floor. A tool without an intent statement cannot have a defensible allow-list decision.
- **Downstream target.** The API / service / store the tool ultimately touches.
- **Allow / deny / conditional verdict per (agent role × session context) pair.** Enumerate every role — no `*` wildcards.
- **Hash.** SHA-256 of the tool's schema at this version; the runtime start-up log must emit the same hash.

Also document:

- **Runtime enumeration mechanism.** How the runtime enforces the allow-list (config-file gate at start-up, feature-flag driven registration, etc.). *No system-prompt allow-list clauses; those are hints, not gates.*
- **Shadow-tool audit.** Enumerate every tool function the underlying tool-bus can expose but that this EACC does not admit. A shadow tool that the runtime *could* expose is a finding.

### Section 2 — Argument-validation contract

For each tool, per argument:

- **Type / schema.** JSON-schema with `additionalProperties: false`, tight `enum` where applicable, length caps on every free-form string.
- **Value-level validator.** For URLs: host allow-list, scheme, no user-info, no `%00`. For emails: address parser, domain allow-list, per-recipient rules. For SQL / shell: parameterised only, or explicit `no free-form` rejection. Cite the parser / library you are using.
- **Argument-provenance requirements.** Which sources may compose this argument (`user_message`, `retrieved_document`, `prior_tool_output`, `system`, `model_composed`); which combinations trigger the chapter-04 HITL escalation.
- **Transformation applied before invocation.** Canonicalisation, case-folding, unicode NFC, trailing-whitespace strip.
- **Refusal shape.** The structured error the wrapper returns when validation fails — `{"error": "argument_validation_failed", "argument": ..., "reason": ..., "allowed_values": ...}` — with the retry policy (max retries against the wrapper's budget).

### Section 3 — Side-effect scope

For each side-effecting tool:

- **Tenant / principal source.** Where the wrapper reads the session's authoritative tenant / user from (the authenticated caller context, not the model's arguments). If the tool schema exposes a `tenant_id` argument, name the wrapper's overwrite rule.
- **Resource resolution and scope check.** How the wrapper resolves `order_id`, `document_id`, `user_id` to the underlying record and verifies the record's tenant matches the session's before invocation.
- **Storage-layer defence.** Row-level security, database view, per-tenant collection — the defence-in-depth check at the store. If none exists, name the "requires peer-role input" for the storage-layer team.
- **Cross-tenant read rule.** How the tool's read paths (`search`, `list`, `get`) enforce the scope — not just the writes.
- **Sudo / override tools.** Any tool that admits a cross-tenant argument is enumerated here with the justification, the approver, and the compensating monitor.

### Section 4 — Credential contract

For each tool:

- **Identity.** The identity the downstream API sees (session user via OAuth on-behalf-of, workload identity, or scoped service identity). *Never the operator's platform key on the tool-invocation path.*
- **Broker.** The credential broker that mints the tool-scoped token. Name the peer-role component and its interface.
- **TTL.** Minutes, not days. Named per tool; shorter is stricter.
- **Scopes.** The specific downstream scopes / IAM actions the credential grants. Every scope is justified against the tool's intent statement.
- **Rotation and revocation path.** How the broker rotates the underlying secret; how a revocation propagates to in-flight sessions.
- **Widening exceptions.** Every widening beyond the minimum is documented, with the review that approved the widening.

### Section 5 — Budget contract

For the session, tenant, user, and agent-role:

- **Tool-call budget.** Total tool calls per session, per hour, per day.
- **Token budget.** LLM tokens, upstream and downstream.
- **Wall-clock budget.** Session-lifetime cap.
- **Dollar budget.** For tools that transact — the monetary cap per session.
- **Retry ceiling per tool.** Retries above which the wrapper disables the tool for the rest of the session.
- **Sub-agent budget inheritance.** The fraction of the parent's budget a sub-agent inherits, per axis. If the peer-role's implementation does not enforce fractional inheritance, cite the finding.
- **Blast-radius caps.** Per-call recipient / amount / row-count caps and the predicate-coverage caps for query-shaped tools. Above the cap → escalate (chapter 04) or refuse (per tool).
- **Rate limits.** Per (tool × principal × time-window) — burst and refill rate. Cite the tool's expected legitimate distribution.
- **Budget-exhaustion path.** What the wrapper does when a budget trips: refuse silently (never — always logged), refuse with a structured error the model can read, or escalate to HITL. Name the escalation destination.

### Section 6 review-outline (deferred to exercise 04)

Add a placeholder that names the escalation contract you *anticipate* — which tools you expect to escalate, for what reasons. Do not author section 6 here; exercise 04 does.

### Section 7 review-outline (deferred to exercises 02 and 05)

Same shape — a placeholder for the sandbox and failure clauses.

### Review memo

Then put on your alter-ego reviewer hat. Author a 1–2 page memo that:

- Names **three specific ways the EACC is under-gated** — a tool the allow-list omits by role, an argument whose provenance is unguarded, a budget axis a runaway session could bypass. For each, propose the specific config change.
- Names **one specific way the EACC is over-gated** — a legitimate call the current thresholds would strand in the escalation queue. Propose the tightening (a diff-pattern auto-approval, a role-scoped exception).
- Confirms whether the EACC could be signed off. If not, list the remediations required.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo):

- `EACC.md` or `EACC.yaml` — sections 1–5 filled in, sections 6–7 outlined.
- `review-memo.md` — the alter-ego reviewer memo.
- `README.md` in the exercise directory naming the target deployment, the tool inventory scope, and a one-paragraph summary of the review outcome.

## Acceptance criteria

- **Every tool has an intent statement, a version pin, and a hash.** No wildcards.
- **Every free-form argument has a length cap, a value-level validator, and a documented provenance rule.**
- **Every side-effecting tool has a wrapper-side tenant overwrite rule and a resource-resolution scope check.** *"The tool trusts the model's `tenant_id`"* is a rejection.
- **Every tool credential is scoped, TTL-bounded (minutes), and minted from a broker.** *"Uses the operator's platform key"* is a rejection.
- **Every budget axis (tool-call, token, wall-clock, dollar, sub-agent) has a specific numeric or contractual value.** *"To be decided"* is not accepted.
- **Every blast-radius cap has a business-logic justification citing the deployment's consent floor.**
- **The review memo names three under-gated cases and one over-gated case, each with a proposed remediation.**
- **Peer-role handoffs are IDs or explicit TODOs**, not "the peer role."
- **The EACC is versioned** and its hash matches what a runtime would emit at start-up.

## Stretch goals

- **Two-role author.** Author the EACC for two distinct agent roles on the same runtime (e.g., an internal-support role with a refund tool and a customer-service role without it). Show how the per-role allow-list keys off principal, and where the two share configuration.
- **Attacker-controlled provenance sweep.** For every argument whose provenance includes `retrieved_document`, red-team the injection surface — what prompt injection would flip which argument, and what wrapper-side validation would catch it? Cite mod-103's injection taxonomy.
- **Auto-approval diff-pattern authoring.** For at least one conditional tool, author the diff pattern (chapter 03 §"Diff-based auto-approval") that would let the wrapper auto-approve calls matching the pattern. Include the pattern's SHA-256 fingerprint so the EACC pins it.
- **Peer-role deliverable list.** Enumerate every peer-role delivery this EACC depends on (broker, sandbox, WORM store, workload identity fabric, memory-provenance hooks, tool-bus enumeration). Estimate each's lead time. This is what turns the EACC from *"authored"* to *"enforceable."*
- **Framework cross-reference.** Cross-reference every section with the specific OWASP LLM06 sub-failure and the specific OWASP Agentic-Threats entry it mitigates.

## Guardrails

- Do not implement the runtime; author the contract. Chapter 03's exercise implements a wrapper; this exercise stops at the contract.
- Do not commit any real credentials, host names, or tenant IDs from a production deployment. Use redacted or paper-deployment values.
- If your EACC references a real deployment, coordinate with the deployment's owning team before submitting the review memo internally.
- Do not paper over missing peer-role components with a hand-wave — the "requires peer-role input" TODOs are what make the EACC honest.
