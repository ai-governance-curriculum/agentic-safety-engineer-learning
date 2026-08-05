# exercise-04: Human-in-the-Loop Escalation and Bypass Prevention

**Estimated effort:** 3 hours

## Objective

Author **section 6** of an Excessive-Agency Containment Contract (EACC) — the human-in-the-loop escalation contract — for one specific tool from your exercise-01 EACC, *and* red-team it against the six bypass classes from chapter 04. The output is a defensible escalation contract, a wire-frame or storyboard of the approver UI, a signed-decision-object schema, and a red-team memo that shows the contract survives the six known bypass classes.

You are not shipping a production approver console. You are demonstrating that the contract, the UI, and the failure-modes have been engineered together — which is what separates HITL from *"send a chat message to a human and hope for the best."*

## Prerequisites

- Read chapter 04 (Human-in-the-Loop Escalation and Bypass Prevention) end-to-end.
- Skim [EU AI Act — Article 14](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) on the four human-oversight capacities.
- Skim the [NIST AI RMF — Manage function](https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook/Manage) HITL playbook entries.
- Skim the OWASP GenAI Agentic-Threats entries on *Overwhelming Human-in-the-loop*, *Human Manipulation*, and *Human Attacks on Multi-Agent Systems*.
- Have exercise 01's EACC and exercise 03's wrapper implementation available. If exercise 03 is not done, the contract is still authorable but the wrapper-side integration is a placeholder.

## Requirements

### Part A — Author section 6 of the EACC

For the chosen tool, populate the following. Every field is a specific value or a specific "waived because …" reason.

**A.1 — Escalation rules.** Enumerate the static, pattern, and anomaly rules that route calls to HITL.

- **Static threshold rules.** E.g., `refund_issue.amount_cents > 5000 → escalate`; `send_email.to_domain not_in verified_domains → escalate`.
- **Pattern rules.** The diff patterns the wrapper auto-approves (fingerprint each). Everything else escalates.
- **Anomaly rules.** E.g., `arg_provenance includes retrieved_document AND arg is free-form → escalate`; `session's escalation_count > 3 in the last hour → pause session`.

**A.2 — Escalation routing.** Primary approver role, secondary role, tertiary role, and fail-closed default. Timing between roles.

**A.3 — Timeout defaults.** Per-tool, per-blast-radius, per-approver-role. Justify any fail-open exception with the compensating mitigation and the reviewer who signed it off.

**A.4 — Withdrawal / revocation window.** Per-tool, per-blast-radius, per-reversibility. Which actions execute inside the window, which wait for the window to close. The compensating rollback path for reversible actions.

**A.5 — Approver identity contract.** Session auth for the modal case; step-up (device-bound push, hardware key, biometric) for high-stakes. Which classes require step-up.

**A.6 — Fatigue budgets.** Per approver: approvals per hour, deliberate-pause thresholds. Per tenant: escalation-volume rate limits. Per session: max escalations before session pause.

**A.7 — Presentation contract.** What the UI must show — resolved targets, state-before / state-after diff, argument provenance, blast-radius indicator, model rationale below-the-fold. The independent-of-the-model natural-language template used for the two-panel diff on high-consequence mutations.

**A.8 — Signed-decision object schema.** Fields: approver identity, timestamp (monotonic + trusted), canonical-form hash of the approved call, decision (`approve` / `deny` / `escalate-further`), reason (free-text, size-capped), signature (algorithm + key ID + signature bytes), revocation-window seconds.

**A.9 — Post-decision audit.** Where the decision object routes, how it is chained into the chapter-03 tamper-evident log, and which downstream monitors classify it.

### Part B — Storyboard the approver UI

Produce a wire-frame or a storyboard — hand-drawn on paper is fine; screenshots of a mock in Figma / your text-editor is fine; a set of text blocks with explicit layout markers is fine. Show:

- The modal case (a routine escalation).
- The high-stakes case (a two-panel diff, the model-rationale below the fold, the step-up prompt).
- The fatigue-triggered case (visually distinct — different colour, different keyboard shortcut, an explicit "you have approved N in the last hour; take a moment").
- The retry-of-denied case (the "previously denied at {t} by {approver}" tag).

The wire-frame is annotated with which capability the design defends against — for each element, name the bypass class it targets.

### Part C — Red-team the six bypass classes

Author a memo `bypass-red-team.md` that walks each of the six chapter-04 bypass classes against your contract:

1. **Model social-engineers the human.** Construct the specific narrative the model would compose to mislead the approver. Argue whether your section-6 contract survives; if not, propose a specific tightening.
2. **Approval fatigue.** Estimate the approver's queue rate under your thresholds. If the rate exceeds the fatigue budget in Part A.6, propose a threshold change or a routing change.
3. **Approval bombing.** Construct the attacker session — retrieved-document-injected instructions that would trigger 100+ plausible escalations. Argue whether your section rate limits catch it before the approver sees any; if not, propose the fix.
4. **Timeout-default bypass.** Consider the session's timing (off-hours, weekend, change-freeze window). Argue whether your fail-closed default holds; if the tool has a documented fail-open, argue whether the compensating mitigation is sufficient.
5. **Revocation-window bypass.** Construct the race the attacker would run to exit the flow before the withdrawal window closes. Argue whether the contract handles the case (irreversible actions wait, reversible ones roll back).
6. **Escalation channel spoofed.** Consider the channels the approver receives escalations on (email, Slack, console). Argue whether your per-decision step-up authentication defends against a compromised inbox / channel.

For each bypass class, the memo has a verdict — *contract holds* with the specific defence; *contract fails* with the specific remediation.

### Part D — Wire the contract into the wrapper (optional but recommended)

If exercise 03 is in place, add a HITL callback to the wrapper. The callback:

- Receives the canonical form and diff.
- Renders the escalation payload in a way a human could read (a JSON-blob printer, a static-HTML file, a CLI prompt).
- Waits (with the timeout from A.3) for a decision to be committed to a decision file.
- Verifies the decision's signature against a public key you have configured.
- Verifies the decision's canonical-form hash matches the wrapper's canonical form (no swap between rendering and approval).
- Returns the decision to the gate pipeline.

This wire-up demonstrates the *"the wrapper honours the signed decision object"* invariant from Part A.9.

## Deliverables

Commit to your exercise-solution area:

- `EACC-section-6.md` (or a diff against your exercise-01 EACC) — Part A fully populated.
- `ui-storyboard/` — the wire-frames or mock renderings, with annotations.
- `decision-schema.json` — the signed-decision object schema.
- `bypass-red-team.md` — Part C, one verdict per bypass class.
- `wrapper-hitl-integration/` — Part D (if attempted).
- `README.md` naming the target tool, the target deployment, and a one-paragraph summary of the red-team outcome.

## Acceptance criteria

- **Every rule in A.1 is a specific configuration** — a numeric threshold, a fingerprinted pattern, or a specific anomaly predicate. *"Escalate suspicious calls"* is not accepted.
- **Every timeout in A.3 has a fail-closed default** unless a documented fail-open exception with a compensating mitigation is cited.
- **The presentation contract in A.7 requires canonical-form-first, model-rationale-below-the-fold.** *"Chat message with model summary"* is a rejection.
- **The signed-decision object schema in A.8 pins the canonical-form hash of the approved call** — the wrapper honours *this* action, not *the model's next attempt*.
- **The bypass-red-team memo covers all six classes** and either shows the contract holds *with the specific defence* or proposes the specific tightening.
- **Approver identity is verified per-decision for high-stakes calls** — step-up authentication is explicit.
- **The storyboard demonstrates the two-panel diff for high-stakes mutations** — one panel from the canonical form, one from an independently-templated natural-language description.
- **If Part D is done, the wrapper honours the signed decision object and refuses on hash mismatch.**

## Stretch goals

- **Approver-selection game.** Two players; one plays the model, one plays the approver. The model tries to socially engineer approval of a wrong action within the constraints of the contract. Play three rounds; publish the transcripts and the specific contract changes each round produced.
- **Fatigue instrumentation.** Wire an approver-side telemetry hook that measures per-approval latency; plot the distribution against a hypothetical rate. Argue whether the shape indicates fatigue (a bimodal shape — fast rubber-stamps and slow thoughtful decisions).
- **Cross-channel confirmation.** Extend Part A.8 to require two independent channels (console + push) for the highest-stakes class; wire a prototype in Part D that demonstrates the flow.
- **Runbook.** Author the "the approver disagreed with the wrapper" runbook — what the on-call engineer does when the approver approves a call the wrapper would have refused, or vice-versa.
- **User-side revocation.** Design the user-facing revocation UI for a customer-facing agent (as opposed to the operator-facing approver UI). Argue about who authenticates whom.

## Guardrails

- **Do not test the UI against a real fatigued human.** Simulated approvers, self-play, or a colleague who has consented.
- **Do not commit real approver identities** or real customer data in the storyboards. Use synthetic personas.
- **Do not rely on a chat interface as the escalation surface** unless the exercise deliverable explicitly argues *why* — and even then, cite the Part-C memo showing the six bypass classes survive.
- **Do not test the approval-bombing scenario against a shared production queue.** If your organisation has a real approver rota, run the simulation against a scratch tenant.
- **The signed-decision key material stays out of the repo** — commit only public verification material.
