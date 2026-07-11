# 04 — Human-in-the-Loop Escalation and Bypass Prevention

## Motivation

Every containment posture leaves a residual set of tool calls that are *plausibly correct but consequential enough* that the runtime will not auto-approve them. The refund above the auto-refund cap, the outbound email to a new external recipient, the database update whose predicate covers more rows than the wrapper's estimator expected, the `sudo`-shaped operation the operator authorises rarely. The gate (chapter 01) puts these calls in the *conditional* bucket. The wrapper (chapter 03) produces the diff a human can read. Something has to route the diff to a human, take their decision, and honour it.

That something is the human-in-the-loop escalation. It is often written as if it were a UI question. It is not. HITL is a *bypass-prevention* problem. The interesting failure is not "the model did the wrong thing." The interesting failure is "the model did the wrong thing and the human approved it." The model can *socially engineer* the human. The volume of approvals can *fatigue* the human. The escalation timing can *pressure* the human. The default-on-timeout can *bypass* the human entirely. Every one of those failure modes has been observed against production HITL flows; the OWASP GenAI Agentic Threats paper names the class as "Overwhelming Human-in-the-loop" and "Human Manipulation."

This chapter builds the HITL escalation as an *engineering discipline* against those failure modes. It pins the decision thresholds that route calls into the escalation, the UI patterns that resist manipulation, the timeout defaults that fail-closed, the revocable-consent workflow that lets a human take back an approval, and the bypass-prevention posture against social engineering, approval fatigue, and approval bombing. It does *not* teach general product-UX design; it teaches the specific engineering choices that make an HITL flow defensible against an adversarial model and an adversarial context.

## Primary sources

- **[OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)** — the Overwhelming Human-in-the-loop, Human Manipulation, and Human Attacks on Multi-Agent Systems entries name the threats this chapter contains.
- **[OWASP Top 10 for LLM Applications 2025 — LLM06: Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/)** — the excessive-autonomy sub-failure specifically names HITL as the required mitigation for consequential actions.
- **[NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)** — the "Manage" function's HITL guidance is the reference posture for consequential-action oversight. <!-- needs-research: pin the current NIST AI RMF revision (1.0 / 1.1 / GenAI Profile) in the HITL contract; the guidance evolves. -->
- **[EU AI Act — Regulation (EU) 2024/1689 — Article 14 (Human oversight)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)** — the regulatory floor for high-risk-system human oversight. Recognise-and-understand, correctly-interpret, override, and stop are the four capacities Article 14 asks for; every HITL flow this chapter engineers must support all four.

## What "human-in-the-loop" means in this discipline

An HITL escalation is a *runtime pause* on a specific tool call, a *canonical presentation* of what the call would do, a *decision request* from a specifically-identified human, and a *signed decision object* the wrapper (chapter 03) honours. Four properties are load-bearing:

1. **The runtime pauses on the call, not on the session.** The agent is not free to skip past the pause; the tool call cannot proceed without the decision object.
2. **The presentation is canonical, not summarised.** The human sees the wrapper's canonical form (chapter 03 responsibility 1) — the resolved targets, the argument provenance, the predicted state delta — not the model's natural-language summary. The model can lie in the summary; the model cannot lie about the wrapper's canonical form because the model does not write it.
3. **The decision is signed and audit-logged.** The decision object carries the approver's identity, the timestamp, the specific action approved (not "the request"), the specific hash of the canonical form, and — for high-consequence actions — a signature that would let a subsequent verifier prove that *this* approver approved *this* action.
4. **The decision is revocable.** Consent given now can be withdrawn within a defined window; the withdrawal window is part of the escalation contract.

Anything else — a chat prompt to a human that says "do you want to proceed?" without the canonical form; a session-level "auto-approve for the next 10 minutes"; a decision that is not audit-logged; a decision that cannot be revoked — is a bypass channel dressed up as HITL.

## Decision thresholds — the escalation contract

The EACC section-6 (chapter 01) defines the escalation contract. This section walks the shape.

An escalation contract is a set of per-tool, per-argument-pattern *rules* that decide which calls escalate, to whom, on what timing, with what fail-closed default. The rule engine reads the canonical form and the diff (chapter 03) and outputs one of three decisions:

- **Auto-approve.** The call proceeds. Common for read-only tools, for tools whose diff matches a pre-authorised pattern, for tools whose blast radius is below the auto-approve cap.
- **Auto-deny.** The call refuses. Common for allow-list violations, budget exhaustion, sandbox-quarantined outcomes.
- **Conditional-approve.** The call pauses. An HITL flow runs. The outcome is a signed decision.

The rules are of three broad shapes:

- **Static threshold rules.** "`refund_issue` with amount > $500 escalates." The EACC pins the threshold; changes are versioned.
- **Pattern rules.** "The diff pattern *matches* the pre-authorised shape → auto-approve; else escalate." Diff-based auto-approval is where you turn "the reviewer once approved a class of calls" into a runtime primitive.
- **Anomaly rules.** "This call's argument-provenance includes retrieved attacker-controllable text → escalate regardless of blast radius." Used sparingly — every anomaly rule that fires becomes an approver-time-tax, and the approval-fatigue budget (below) is not free.

### Right-sizing the threshold

Thresholds too low → approval fatigue → approval-bombing → rubber-stamping → bypass by exhaustion. Thresholds too high → the interesting-consequence calls skip HITL → OWASP LLM06 excessive-autonomy in production. Right-sizing is a discipline:

- **Anchor to the business's existing consent floor.** If the support policy authorises refunds up to $500 without a manager, the HITL threshold anchors at $500. Otherwise the containment posture is more restrictive than the un-agent policy, and the agent will be treated as broken.
- **Anchor to the observed distribution.** The wrapper's audit log gives you the distribution of past *successful, unquestioned* calls. Escalation should fire on the tail, not on the modal call. If 99% of past refund calls were under $50, the threshold at $500 is defensible; the threshold at $10 will fatigue every approver in the queue.
- **Anchor to the reversibility axis.** Irreversible actions (deleting data, sending an external email, wiring money) escalate at a lower threshold than reversible actions (moving a ticket status). Reversibility is a factor in the escalation contract.

### Common failure the reviewer catches

The escalation contract is *"escalate everything over $X."* The reviewer asks whether the operator can distinguish "the diff is a pre-authorised shape" from "the diff is novel and worth an approval." If not, the operator will rubber-stamp; the escalation is theatre.

## Escalation UI patterns — resisting manipulation

The HITL UI is not a chat message. It is a purpose-built interface designed to satisfy the four Article-14 capacities against an adversarial model.

### Show the canonical form, not the summary

The approver sees the wrapper's canonical form (chapter 03) rendered as a *structured diff*. Concretely:

- **Resolved targets, not raw IDs.** Not `refund order_id=ord_abc123`; but `Refund order ord_abc123 — customer u_..., email joe@example.com, amount $15.00, status shipped, placed 2025-06-04`.
- **State-before / state-after diff.** Two columns: *current* and *if approved*. Diff-highlight the fields that would change. Green for creation, red for deletion, amber for mutation.
- **Argument provenance.** Show which arguments came from the user's message, which came from the retrieved document, which the model composed. Attacker-controllable provenance is *specifically flagged*.
- **Blast-radius indicator.** For query-shaped tools, show the estimated affected count (e.g., "This query would update 4 233 rows"). Above the cap, the indicator turns red.
- **Model rationale, in a separate pane.** The model's summary is available, clearly labelled as *model-provided*, and *below* the canonical form. It is not the primary evidence.

### Two-panel diff for consequential mutations

For mutation-shaped calls above a specified blast-radius threshold, render the diff *twice*: once as a structured field-by-field diff, once as a natural-language description generated *independently of the model* (a template rendered from the canonical form, not the model's summary). The approver's confidence goes up when the two agree; the flow escalates further when they disagree. This is a specific defence against a model that has been convinced to compose a benign-looking summary of a harmful action.

### Explicit accept, explicit reject — no default

The approve and deny actions are distinct, keyboard-shortcut-distinct, and never mapped to the same keystroke path. No "click anywhere to dismiss." No "press enter to approve." The user must positively affirm one direction. This is a specific defence against *habit-of-mind* fast-clicks under fatigue.

### No approver-visible retry counter

If an approver denied a call and the model retries with a slightly different argument, the approver sees the retry as a *new* escalation with a specific tag that says "the previous version of this call was denied at {timestamp} by {approver}." Not "please review again"; a *specific record* that a denial was recent. This prevents an implicit pressure to reverse a prior denial.

### The approver's identity is verified, per-decision

Session-level authentication is not enough. The signed decision object carries a step-up authentication (device-bound push, hardware key, biometric) *per decision* for high-stakes calls. Session-level auth is fine for the modal case; step-up is the pattern for the outlier.

### Common failure the reviewer catches

The escalation UI is "the agent tells the user in chat, and the user replies OK." A reviewer at this level asks what the user actually saw. If the answer is "the model's summary in a chat bubble," the finding is: *the approver cannot recognise, cannot correctly interpret, cannot properly override, and cannot stop* — none of the Article-14 capacities is supported.

## Timeout defaults — fail-closed on absence

An approver away for lunch, on-call sleep, or triaging a different queue is not implicit assent. The timeout default is *deny*, not *approve*, unless the tool's semantics specifically justify an approve-on-timeout posture and the EACC section-6 explicitly documents that justification.

- **Fail-closed by default.** No decision within the window → the call refuses, the session is paused, the audit log records the timeout.
- **Timeout is per-call, not per-session.** A session with many escalations has many timeouts; each times out independently.
- **Timeout scaling.** For read-only actions, minutes. For mutations, longer — long enough that the approver can consult a colleague, verify a policy, look up a customer's history. Too short a timeout is itself a fatigue vector.
- **Timeout escalation ladder.** If the primary approver times out, the escalation routes to a secondary role, then a tertiary, before failing-closed. mod-108 monitors flow-through rates against the ladder; a ladder whose primary is always timing out is broken and the EACC needs updating.
- **Fail-open explicitly, only where documented.** Some tools' semantics genuinely require a fail-open (an anti-fraud tool whose failure would let fraud through is a rare example). The EACC names the tool, names the justification, names the compensating mitigation (a monitor that catches the fraud after the fact). The reviewer signs off on the fail-open.

## Revocable consent — approvals with a withdraw window

A decision made now must be withdrawable within a bounded window. The revocation contract is:

- **Withdrawal window.** The interval between approval and irreversibility. Reversible actions may execute inside the window; irreversible actions may *not* execute until the window closes.
- **Withdrawal path.** The approver (or a specifically-empowered peer) can revoke the decision by submitting a signed revocation. The wrapper honours the revocation and — if the action already executed reversibly — rolls back.
- **Withdrawal audit.** The revocation is an audit-log entry with the withdrawal reason. Repeated withdrawals against the same tool feed mod-108's monitors as a "the escalation is under-tuned" signal.
- **User-side withdrawal.** For customer-facing agents, the *user* who caused the escalation may also withdraw within their own window ("actually, do not send that email"). The withdrawal path is on the user's UI, not through the agent.

Revocability composes with reversibility: reversible actions can begin during the window; irreversible ones wait. For a session where the user has already left the flow (browser closed), the wrapper still respects the withdrawal window — either by delaying execution until the window ends or by only executing reversibly during it.

## Bypass prevention — the adversarial cases

The rest of the chapter walks the specific bypass classes an adversary tries against an HITL flow, and the containment posture for each.

### Bypass class 1 — The model social-engineers the human

*Shape.* The escalation reaches the approver with a model-composed narrative that misleads the approver into approving. "The customer just called; they need this urgently." "This is a routine adjustment; policy says it is auto-approved." "Manager overrode this yesterday for this customer."

*Containment.*

- The approver sees the *canonical form*, not the model's narrative. The narrative is available in a labelled model-rationale pane but is not the primary evidence.
- The approver's confirmation is tied to the *canonical form's hash*. A model that regenerated the narrative between rendering and approval would trigger a hash mismatch and re-render.
- Escalations for a *specific* customer's transactions are correlated across the session's history; a claimed "the customer said" is checked against the actual user messages in the session (which the wrapper has).
- For high-stakes categories, the model *may not compose narrative at all* — the UI shows only the canonical form.

### Bypass class 2 — Approval fatigue

*Shape.* The approver's queue is long, the calls look similar, the approver rubber-stamps. Every yes reduces the marginal attention on the next; eventually the malicious yes gets through unnoticed.

*Containment.*

- **Queue-fatigue budgets.** Per-approver rate limits (approvals per hour) with a *deliberate pause* on threshold. When an approver crosses the rate cap, the queue routes to another approver.
- **Deliberate visual variance in high-stakes calls.** The UI for a call that crosses a fatigue-triggering threshold is *visually distinct* from the routine UI. A different colour scheme, a different confirmation step, a different keyboard shortcut. This is the anti-*click-through* posture.
- **Sampling audit.** A random sample of approved decisions is reviewed by a second approver post-hoc. Divergence rates feed the escalation-contract tuning.
- **Threshold discipline.** Chapter section on right-sizing — thresholds too low are the cause of fatigue.

### Bypass class 3 — Approval bombing

*Shape.* The attacker floods the escalation queue with plausible calls to fatigue the approver, or to drown the malicious call in a sea of noise.

*Containment.*

- **Per-session and per-tenant escalation rate limits.** A session cannot produce more than N escalations per hour; a tenant cannot produce more than M. Beyond the limit, the session pauses and the tenant's incident-response contact is notified.
- **Anomaly detection on escalation volume.** mod-108 monitors escalation-volume anomalies. A tenant that used to produce 3 escalations a day and is producing 300 today triggers a session-level pause.
- **Deduplication.** Structurally identical calls collapse into a single escalation with a count. An attacker that resends a call ten times does not fatigue ten times; the approver sees one entry with a "10 attempts" tag.
- **Rate-limit the model, not just the approver.** The gate pipeline's rate and budget primitives (chapter 01) apply to escalation-producing calls too. A session that would produce more than its escalation budget's worth is paused *before* the escalations queue.

### Bypass class 4 — The timeout-default bypass

*Shape.* The attacker times the malicious call to occur when the approver is unlikely to respond (off-hours, during a change freeze). The timeout default is *approve*; the malicious call executes.

*Containment.*

- **Fail-closed default.** As above.
- **Timeout-ladder to a secondary approver.** If the primary is unreachable, escalation flows to a secondary role; the wrapper does not auto-approve on primary absence.
- **Business-hours awareness.** Some tools' HITL flow is only available during business hours; outside of them, the call refuses with a "human oversight window closed" error the model can observe. This is a design choice, not a leak.

### Bypass class 5 — The revocation-window bypass

*Shape.* The attacker races the withdrawal window: performs the action immediately, exits the flow, and the withdrawal is too late to matter.

*Containment.*

- **Irreversible actions wait for the window.** No wall-clock savings; the action does not execute until the window ends.
- **Reversible-during-window is the compromise.** Reversible actions may execute during the window; the wrapper's compensating rollback path is documented and tested.
- **The window's length is per-tool, not per-session.** Longer windows for higher-stakes calls; a $10 000 refund's window may be 30 minutes; a ticket-status change's window may be 30 seconds.

### Bypass class 6 — The escalation *itself* is spoofed

*Shape.* The attacker takes over the escalation channel (a compromised Slack channel, a hijacked email inbox) and *auto-approves* their own escalation.

*Containment.*

- **Escalation identity is per-decision.** Step-up authentication for high-stakes calls (device-bound push, hardware key, biometric).
- **Escalation channel is not the model's channel.** The approver does not receive the escalation in the same UI the agent runs in; the escalation is a distinct channel with its own identity fabric.
- **Cross-channel confirmation.** For the highest-stakes categories, two independent channels must confirm (e.g., approve in the console and confirm on a push notification). This is expensive; use it only where the EACC section-6 justifies it.

## The HITL contract in the EACC

Section 6 of the EACC codifies:

- **Per-tool escalation threshold.** Static, pattern, and anomaly rules.
- **Escalation routing.** Primary role, secondary role, tertiary role, fail-closed default.
- **Timeout defaults.** Per-tool, per-blast-radius.
- **Withdrawal window.** Per-tool, per-blast-radius, per-reversibility.
- **Approver-identity requirements.** Session auth, step-up auth, per-decision cryptographic signing.
- **Fatigue budgets.** Per-approver, per-tenant, per-session, per-hour.
- **Presentation contract.** What the UI must show (canonical form, provenance, blast radius, model rationale below the fold).
- **Post-decision audit obligations.** What the decision object must carry, how it is signed, where the log routes.

Exercise 04 walks through authoring this section for one concrete flow *and* red-teaming it against the six bypass classes above.

## Common misreadings to avoid

- **"The chat interface is the HITL surface."** A chat message asking "shall I proceed?" is not HITL. HITL renders the canonical form, requires positive affirmation, and produces a signed decision object. Chat asks the model to summarise the model's own intent to a fatigued human.
- **"Auto-approve for the rest of the session."** No. Every conditional call has its own decision. Auto-approve-for-N-minutes turns HITL into single-sign-off theatre — the model can carefully sequence the malicious call after the approval.
- **"The approver's identity is the session's authenticated user."** For low-stakes calls, yes. For high-stakes calls, the approver is a *specific* human whose identity is verified *per decision*, not per session.
- **"Fail-open on timeout is fine; nobody wants to be blocked."** Not for consequential actions. Fail-open on timeout is a specific, EACC-documented exception for tools whose semantics require it — and the tool has a compensating mitigation.
- **"Once we approved, the action executes immediately."** Not for irreversible actions. The withdrawal window is a load-bearing property; execute inside it only for reversible actions with a documented rollback.
- **"We can prevent bypass with a good UX."** UX is necessary but not sufficient. Every UX we have written has been socially engineered under enough pressure. Bypass prevention is UX + threshold discipline + rate limits + audit + cross-channel confirmation.

## Summary

- HITL is a bypass-prevention discipline. The interesting failure is the *approved* wrong action, not the *unapproved* right one.
- Route calls into HITL via the EACC's escalation contract — static thresholds, pattern rules, anomaly rules. Right-size against the business's consent floor and the observed distribution.
- Render the canonical form (chapter 03) as a structured diff. The model's summary is labelled and below the fold. Explicit approve, explicit reject, no default.
- Timeout defaults are *deny*. Fail-open is an EACC-documented exception with a compensating mitigation.
- Revocable consent within a bounded window. Irreversible actions wait; reversible ones may execute with a documented rollback.
- Contain six bypass classes: model social engineering, approval fatigue, approval bombing, timeout-default bypass, revocation-window bypass, escalation-channel spoofing.
- The EACC section-6 codifies the contract; exercise 04 walks authoring and red-teaming.
