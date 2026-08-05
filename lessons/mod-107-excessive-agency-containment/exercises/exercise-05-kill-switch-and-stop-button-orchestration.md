# exercise-05: Kill Switch and Stop-Button Orchestration

**Estimated effort:** 2 hours

## Objective

Design the kill-switch fan-in, mode configuration, and incident-response contract for one concrete deployment, and *run a table-top drill* against a specific incident scenario. The output is a switch-registry entry, an incident-response contract keyed on each fire mode, a runbook, and a written trace of the table-top drill — including the un-fire decision and the post-incident EACC update it implied.

You are not building an org-wide feature-flag platform. You are demonstrating that if the switch fired at 03:00 the operator would know *which* switch to fire, *what* would happen, *who* would be paged, *what* the un-fire path is, and *what* the customers would be told.

## Prerequisites

- Read chapter 05 (Kill Switches and Stop Buttons) end-to-end.
- Read Anthropic's [RSP](https://www.anthropic.com/rsp), OpenAI's [Preparedness Framework](https://openai.com/safety/preparedness/), and Google DeepMind's [Frontier Safety Framework](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/) sections on rollback triggers / halt-deployment language. mod-101 covers these in depth.
- Skim [NIST SP 800-61 Rev. 3](https://csrc.nist.gov/publications/detail/sp/800-61/rev-3/final) — the four-phase incident-response shape.
- Have exercise 01's EACC available so section 7's failure clauses can be populated. Exercise 03's wrapper (with an audit log stream to quarantine) makes the drill more concrete but is not strictly required.
- Access to whichever feature-flag / config-management platform you would use for the switch's implementation surface — this exercise designs the switch; the platform is the peer-role's ownership.

## Requirements

### Part A — Switch registry entry

Author `switch-registry.md` (or `switch-registry.yaml`) for at least three switches at different scopes. For each, record:

- **Switch ID.** A stable identifier the runbook cites.
- **Scope.** The specific `(deployment × tool × tenant × region × agent-role)` tuple. Enumerate; no wildcards.
- **Modes supported.** Which of Mode 1 (soft stop), Mode 2 (freeze), Mode 3 (quarantine), Mode 4 (rollback). Per mode, name the fire path.
- **Fan-in.** Who / what can fire it — human path, monitor votes, downstream EACC contracts, upstream RSP / Preparedness / FSF triggers, partner contracts.
- **Approver.** The human authorised to fire each mode. Escalating approval — a per-tool soft stop may be one on-call, an org-wide quarantine may require a safety-review-body signature.
- **False-positive cost class.** A tier that decides the escalating-approval requirements.
- **Owner.** The engineering role responsible for the switch's operational health.
- **Un-fire path.** Distinct from the fire path; a different approver.
- **Propagation-latency SLA.** The measured time from fire → all wrappers-in-scope refusing the next call. Cite the exercise-02 sandbox measurements.

At least one switch is per-tool (narrowest), one is per-deployment (broader), one is org-wide (widest). This demonstrates the escalating-approval pattern.

### Part B — False-positive cost analysis

For each switch, produce a short analysis:

- **True-positive benefit.** Concrete harm prevented — customer impact, regulatory exposure, cost of the alternative response.
- **False-positive cost.** Concrete harm if fired mistakenly — outage impact, SLA cost, operational cost of recovery, trust cost.
- **Ratio and disposition.** Is the switch usable at the current ratio? If not, name the specific engineering knob that would reduce the false-positive cost enough to make it usable (narrower scope, faster un-fire path, mode-1-only default, graceful-degradation shape).

### Part C — Incident-response contract

For each of the four fire modes, author a contract in `ir-contract-mode-{1,2,3,4}.md` that walks the four NIST-SP-800-61-Rev-3 phases:

- **Phase 1 — Immediate containment (first minute).** What propagates, what is paged, who is designated incident commander.
- **Phase 2 — Detection and analysis (first hour).** What evidence is captured (wrapper audit log, sandbox telemetry, egress-proxy log, HITL decision stream), which teams are pulled in.
- **Phase 3 — Recovery (first day).** Compensating actions (chapter 04 rollback, chapter 02 side-effect quarantine review). Un-fire preparation. Customer-impact communication draft.
- **Phase 4 — Post-incident activity (first week).** Blameless review. EACC update. mod-108 monitor tuning. mod-111 red-team briefing. mod-112 disclosure trigger (internal, regulatory, public).

For Mode 3 and Mode 4, include the mod-112 disclosure workflow trigger — internal, EU AI Act Article 73 (where applicable), or public.

### Part D — Runbook

Author `runbook.md` for a **cross-switch coordinated fire** — a scenario where several switches must fire in a specific order. Choose one of:

- Suspected credential-broker compromise: fire the tool-scoped switches for every tool whose credentials the broker mints, then the per-deployment switch if the credential is broadly used, then rotate the broker.
- Suspected base-image supply-chain compromise: fire the code-interpreter switches at the deployments running the image, then the org-wide switch if the compromise is confirmed, then rebuild the image.
- Suspected model-side regression (a monitor sees a large increase in HITL denials): fire the soft-stop switch on the affected agent role, then quarantine the sessions with un-approved side effects.

The runbook enumerates the switches, the fire order, the sanity checks between fires, the pre-conditions each fire must satisfy, the paging plan, and the rollback (un-fire) discipline.

### Part E — Table-top drill

Run the drill. Either with colleagues (as an actual table-top exercise, ~45 min) or as a self-play walk-through in writing. Author a `drill-trace.md` that records:

- **Scenario.** The specific incident (drawn from Part D or an alternative).
- **Timeline.** T+0 (fire), T+1 min (containment), T+15 min (evidence capture), T+1 hr (analysis), T+1 day (recovery), T+1 wk (post-incident).
- **Decisions.** Every decision the incident commander made and the rationale. Every switch fired (and un-fired) and by whom.
- **Communication.** What was said to customers, to the safety-review body, to the regulator (if any). Draft the messages.
- **EACC update.** What sections of the EACC the incident implied a change to. Version-bump the EACC and note the diff.
- **Post-drill critique.** What went wrong in the drill (a switch was slow to un-fire; a runbook step was ambiguous; the customer message was too legalistic). Route each critique to a specific engineering owner.

## Deliverables

Commit to your exercise-solution area:

- `switch-registry.md` — Part A.
- `false-positive-analysis.md` — Part B.
- `ir-contract-mode-1.md` … `ir-contract-mode-4.md` — Part C, one per mode.
- `runbook.md` — Part D.
- `drill-trace.md` — Part E, with the timeline, decisions, communications, and post-drill critique.
- `EACC-section-7.md` (or a diff against exercise-01's EACC) — updated with the switch clauses and the drill-implied changes.
- `README.md` naming the deployment, the switches, and a one-paragraph summary of the drill outcome.

## Acceptance criteria

- **At least three switches at three different scopes are registered**, with per-switch fan-in, modes, approvers, propagation-latency SLA, and un-fire path.
- **Every switch has an explicit false-positive cost analysis** with a disposition — usable, or requires a specific engineering change to become usable.
- **Every fire mode has an IR contract** covering the four NIST-SP-800-61-Rev-3 phases, with named roles.
- **The runbook is executable** — an on-call engineer with no prior context could follow the steps at 03:00.
- **The table-top drill produced at least one EACC update.** A drill with no findings is a drill that was not adversarial enough.
- **Un-fire requires a different approver than the fire.** Documented in the registry.
- **Automated fires default to Mode 1 (soft stop) and page a human**, with the false-positive-rate budget cited.
- **Mode 3 and Mode 4 fires trigger mod-112 disclosure workflows.** The workflow IDs are cited (or explicit TODOs).

## Stretch goals

- **Live-fire drill.** Fire a real switch on a real staging deployment with pre-arranged stakeholder awareness. Measure the propagation latency and update section 7 with the measured value. Un-fire under the drill approver's discipline.
- **Automated-fire policy.** Design the automated-fire policy for one monitor (a mod-108 monitor of your choice). Include the false-positive-rate budget, the fire-mode default, the paging path, and the retirement criterion.
- **Runbook-diff exercise.** Author two versions of the same runbook — one for a normal-business-hours fire, one for an off-hours fire (skeleton on-call, no incident commander until they wake up). Show the difference in decisions.
- **Cross-org drill.** Simulate a partner-driven fire (a foundation-model provider's RSP-driven deployment-scope reduction that hits your surface). Walk the response.
- **Regulator-facing drill.** Draft the EU AI Act Article 73 serious-incident notification the drill would trigger. <!-- needs-research: pin the current Article 73 initial-notification cadence / template. --> Include what evidence you would attach.

## Guardrails

- **Do not fire real production switches for the drill unless you have pre-arranged authorisation and a rollback plan.** The drill can be table-top.
- **Do not test automated fires against a live monitor without pre-arranged awareness** — a monitor that has never fired at scale will produce a shape you cannot predict.
- **Do not draft customer communications in a form that would be mistaken for the real thing.** Mark every drill artefact as a drill.
- **Coordinate with your legal / policy / disclosure team before drafting regulatory notifications**, even in a drill. Drafts are internal artefacts.
- **Do not include real credential material, real tenant IDs, or real customer PII in the drill trace.** Redact.
