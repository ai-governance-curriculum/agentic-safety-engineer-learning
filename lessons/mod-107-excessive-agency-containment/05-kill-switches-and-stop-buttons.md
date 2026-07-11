# 05 — Kill Switches and Stop Buttons

## Motivation

The gate, the sandbox, the wrapper, and the HITL escalation are the containment layers you engineer *before* the fact. They are your best guess at what needs to be caught. A kill switch is what you engineer for the *after*: the moment the operator, the safety team, the on-call engineer, or the regulator concludes that *the deployment cannot be permitted to take further actions*, and the response has to be *fast*, *scoped*, *auditable*, and *reversible enough that it can be used*.

The framing this discipline inherits from the frontier-safety literature is direct. Anthropic's Responsible Scaling Policy and OpenAI's Preparedness Framework both name **rollback triggers** — pre-committed thresholds at which a deployment's capability engages an additional mitigation or is withdrawn. Google DeepMind's Frontier Safety Framework carries the equivalent under Critical Capability Levels. At the deployment-runtime layer, the mitigation of last resort *those* frameworks reach for is exactly the primitive this chapter engineers: a stop button that halts the agent's tool-taking authority, in place, and does so faster than the model can talk anyone out of it.

The interesting engineering question is not *"can you press a button?"* — that is trivial. The interesting question is: *what happens after you press it?* The button that everyone is afraid to press because the false-positive cost is catastrophic is not a stop button; it is a decoration. The button that fires whenever any monitor blinks is a nuisance and gets muted. The button that fires cleanly but leaves the agent's half-completed side effects in an unknown state produces a worse incident than the one it stopped. The button that only one team can press stops one team's problem, not the organisation's. And the button whose firing has no defined follow-up leaves the incident-response team improvising in the worst possible moment.

This chapter engineers the kill switch as a *system* — the button, the scope, the escalation fan-in, the false-positive cost model, the org-wide orchestration, and the incident-response contract that follows a fire. It ties into chapter 06's boundary with `ai-infra-security` (the platform layer the button runs on), the mod-108 monitors that vote a fire, and the mod-112 disclosure workflow the fire triggers.

## Primary sources

- **[Anthropic — Responsible Scaling Policy](https://www.anthropic.com/rsp)** — read the sections on capability thresholds, deployment-scope reductions, and rollback triggers. The RSP's shape is where the "if crossed, engage this mitigation" contract this chapter engineers comes from.
- **[OpenAI — Preparedness Framework](https://openai.com/safety/preparedness/)** — the safeguards-and-safety-response section names the mitigations the operator's stop button implements at runtime.
- **[Google DeepMind — Frontier Safety Framework](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/)** — Critical Capability Level responses; the FSF's language on halting deployments is the framing for org-wide stop-button orchestration.
- **[NIST SP 800-61 Rev. 3 — Computer Security Incident Handling Guide](https://csrc.nist.gov/publications/detail/sp/800-61/rev-3/final)** — the reference posture for the post-fire incident-response contract this chapter engineers. Preparation → Detection & Analysis → Containment, Eradication & Recovery → Post-Incident Activity is the shape the follow-up workflow honours.
- **[OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)** — the Resource Overload, Misaligned Goals, and Human-in-the-loop Bypass entries name the fire scenarios this button is the terminal control for.

## What a "kill switch" is — and is not

A kill switch, in this module, is a runtime control that, when fired, transitions the deployment (or a specifically-scoped part of it) into a **contained state** — one in which the agent's tool-taking authority is revoked, in-flight side effects are stopped or quarantined, and the state is held until an explicit incident-response gate resumes normal operation.

Four properties are load-bearing:

1. **Scoped.** The switch has a defined scope — one tool, one agent role, one tenant, one deployment, one region, one product line, or *global* — and the firer chooses the scope from a bounded set. There is no *"fire everything and figure out the scope later"* button, because the false-positive cost of that button is catastrophic and it will not be used.
2. **Fast enough to matter.** The switch propagates to every wrapper in scope before the *next* tool call the switched-off wrappers admit. The propagation-latency budget is a section-7 EACC field; on a well-designed platform it is seconds, not minutes.
3. **Auditable.** The firing is a first-class audit-log event, signed, timestamped, with the firer's identity, the fire reason, the scope, and the switch state before and after. The chapter-03 tamper-evident log carries it.
4. **Reversible, but only through a gate.** Un-firing is a distinct action with its own approval flow (a peer role, an on-call rotation, an incident commander), not the same person who fired the switch. Otherwise firing becomes a mere pause the model can wait out.

A kill switch is *not*:

- The wrapper's `refuse` return. That is per-call. The switch is per-scope.
- The EACC re-authoring. That is offline. The switch is runtime.
- The HITL flow's `deny`. That is one decision. The switch stops the flow entirely.
- The load balancer that removes an unhealthy pod. That is an availability control. The switch is a *safety* control.

## Fire modes — what the switch actually does

A well-designed switch supports multiple fire modes so the firer can match the response to the incident's shape. The four canonical modes:

**Mode 1 — Refuse-only (soft stop).** The wrapper enters *refuse everything* state. In-flight tool calls complete; new tool calls refuse with a structured error the model observes. Non-tool-taking work (chat responses to the user, summarisation, retrieval-only) may continue. Right posture for a suspected policy drift where the containment layers below (chapter 04's HITL) are still trusted but the operator wants to prevent any new agentic action until a review completes.

**Mode 2 — Freeze (hard stop).** The wrapper enters *refuse everything and pause the session*. In-flight tool calls are aborted where possible; sub-agents are torn down; the model is not invoked again in the session until an explicit resume. Right posture for a suspected active exploitation where continuing to invoke the model risks it composing further exfil narrative.

**Mode 3 — Quarantine (contain and preserve).** Same as freeze, plus: capture full session state (conversation, tool history, sandbox state, wrapper audit entries) to the mod-112 disclosure workflow's evidence store. The session becomes an *incident artefact*. Right posture for the escalation to a regulator-visible disclosure or the internal safety-review body.

**Mode 4 — Rollback (revert).** Same as quarantine, plus: for reversible side effects taken within a defined window (the chapter-04 withdrawal window), trigger the compensating rollback path. Right posture for a *"we now believe every action this agent took in the last N minutes was wrong"* verdict — a rare but decisive escalation.

The EACC section-7 lists which modes are supported per scope, who is authorised to invoke each mode, and what the fire's downstream (chapter 04 HITL rollback path, mod-112 disclosure workflow) looks like per mode.

## False-positive cost accounting

The button that everyone is afraid to press is not a control. Every kill switch is analysed against two costs before it is authorised:

- **True-positive benefit.** The harm the fire prevents. Concrete: the customer impact, the regulatory exposure, the safety exposure, the cost of the alternative response.
- **False-positive cost.** The harm the fire causes if the fire was mistaken. Concrete: the customer impact of the outage, the SLA cost, the operational cost of the recovery, the trust cost.

The ratio matters. A switch whose true-positive benefit ratio to false-positive cost is 100× is a switch that fires liberally; a switch whose ratio is 2× fires under strict criteria; a switch whose ratio is 0.5× is a switch you must engineer to *reduce the false-positive cost* before it will be usable.

Concrete engineering knobs for reducing false-positive cost:

- **Narrower scopes.** A single tool's fire has 1/10th the cost of a whole-deployment fire, ceteris paribus. Publishing granular scopes lets firers use the smallest applicable one.
- **Fast un-fire path.** If un-firing takes an hour, firing is a decision to accept an hour of outage. If un-firing takes a minute (with a second-approver gate), firing is a decision to accept a minute of outage.
- **Explicit soft-stop mode.** Mode 1 (refuse-only) is much cheaper to un-fire than mode 3 or 4. Firers who suspect but do not know default to the soft stop.
- **Graceful degradation.** The agent's non-tool functionality continuing under a soft stop is a big false-positive cost saver. The user gets a "I can't take that action right now" message instead of a wholesale outage.
- **Documented false-positive playbook.** Every mode has a documented playbook for the customer-impact case. mod-112's disclosure workflow honours it.

The EACC section-7 codifies the false-positive cost analysis per scope. mod-108 monitors the false-positive rate.

### The escalating-approval pattern

A useful compromise for high-cost scopes: firing at a wider scope requires escalating approval. A single on-call may fire a per-tool soft stop. A per-deployment freeze requires the incident commander. An org-wide quarantine requires a safety-review-body sign-off. This *reduces the false-positive cost of the smaller fires* while *keeping the largest fires possible under sufficient authority*.

## Escalation fan-in — who and what can fire the switch

A kill switch fires when *some* fan-in condition is met. The condition is a disjunction:

- **A human presses a button.** The primary. Every switch has a human path.
- **A monitor votes a fire.** mod-108's monitors — canary probes, anomaly detectors, budget-exhaustion detectors, egress-anomaly detectors, HITL-fatigue detectors — can vote a fire. The vote is not the fire; the fire is either an automatic threshold on the vote (for switches whose false-positive cost accepts automation) or an escalation to a human (for the rest).
- **A downstream contract fires.** The EACC's rollback triggers (section 6 escalation contract; section 7 failure contract). If a specific pre-registered threshold is crossed — a certain rate of gate refusals, a certain rate of HITL denials, a certain sandbox-escape indicator — the switch fires.
- **An upstream contract fires.** For frontier deployments, the RSP / Preparedness / FSF rollback triggers land here. If the safety-review body issues a scope reduction, the switch fires the appropriate scope.
- **A partner contract fires.** For multi-tenant deployments, a tenant may have their own kill switch that fires their scope. mod-112 codifies the tenant-side switch as a disclosure obligation.

The fan-in shape is a graph the EACC section-7 draws. Each node is a fire condition; each edge is who / what escalates to what. The switch's *own* firing rate is monitored; unusual firing patterns feed mod-108's monitors as their own signal.

### Automated-fire discipline

Automated fires are a specific engineering discipline. The rules:

- **Fire mode matches automation confidence.** Automated fires default to Mode 1 (soft stop). Mode 2+ requires a human confirmation from the on-call within a bounded window.
- **Every automated fire notifies a human.** The fire is not silent; the on-call is paged; the incident-response contract engages.
- **False-positive rate is measured and bounded.** mod-108 measures the automated-fire's true-positive vs. false-positive rate against ground-truth reviews. Above a bounded false-positive rate, the automation is tuned or retired.
- **Automated un-fire is generally not permitted.** A monitor that voted the fire cannot vote the un-fire; un-firing is a human path with its own approval. Otherwise a compromised monitor can turn the switch off.

## Org-wide orchestration

For an operator running multiple deployments, the switch is an *organisation-level* concept, not a per-deployment one.

### The switch registry

Every switch in the org is registered in a central registry with:

- **Scope.** The specific (deployment × tool × tenant × region × agent-role) tuple.
- **Modes supported.** Which of the four modes.
- **Fan-in.** Who can fire it, what monitors can vote a fire, what upstream / downstream contracts fire it.
- **Owner.** The engineering role responsible for the switch's operational health.
- **False-positive cost class.** A tier that decides the escalating-approval requirements.
- **Fire history.** Every fire, mode, firer, un-fire, downstream.

The registry is what an incident commander reads at 03:00 to know *which switch* to reach for.

### Coordinated fires

For classes of incident that cross deployments — a suspected supply-chain compromise of the base image, a suspected credential leak in the broker, a suspected shared-classifier failure — the incident commander needs a *coordinated fire*: several switches, in a specific order, with a specific scope.

The pattern is a **runbook** that names the switches, their fire order, the sanity checks between fires, and the pre-conditions each fire must satisfy. Runbooks live in the disclosure workflow's runbook store (chapter 06 boundary) and are exercised in mod-112's tabletop drills.

### Cross-org obligations

Where the operator's deployment is part of a frontier lab's ecosystem (an API tenant of a foundation-model provider), the cross-org kill-switch contract matters. If the foundation-model provider fires their equivalent (e.g., an RSP-driven deployment-scope reduction), the operator's switches for the affected surface fire in response. mod-101 codifies the frontier-safety obligations; mod-107 engineers the runtime response.

## Incident-response contract — after a fire

A fire that stops the immediate harm but leaves the incident un-investigated is worse than useless; it teaches the org that fires do not lead anywhere. Every fire triggers a defined incident-response contract, keyed on the fire mode.

The contract has four load-bearing phases (aligned to NIST SP 800-61 Rev. 3):

### Phase 1 — Immediate containment (first minute)

- The switch is fired; the wrappers propagate the switch state.
- The audit log records the fire with the firer's identity, mode, scope, reason.
- The affected sessions are quarantined (Mode 2+).
- The on-call is paged; the incident commander is designated.

### Phase 2 — Detection and analysis (first hour)

- The incident commander opens an incident ticket in the operator's IR system.
- The wrapper's audit log, the sandbox's telemetry, the egress-proxy log, the HITL decision stream are pulled into an evidence archive.
- mod-108's monitor state is snapshotted.
- The affected users / tenants are enumerated.
- The suspected root-cause hypothesis is documented; the true root cause is investigated in parallel.

### Phase 3 — Recovery (first day)

- The compensating actions (chapter 04 rollback, chapter 02 side-effect quarantine review) are performed.
- The customer-impact communication is drafted with legal / policy counsel and, for high-stakes deployments, the disclosure workflow team (mod-112).
- The un-fire decision is prepared: what needs to be true about the deployment for it to be safe to resume? What changes to the EACC does the incident imply?
- The un-fire is executed under the escalating-approval discipline.

### Phase 4 — Post-incident activity (first week)

- A post-incident review (blameless, engineering-oriented) is written.
- The EACC is updated with the specific configuration change the incident implies.
- mod-108's monitors are tuned against the observed indicators.
- mod-111's red-team is briefed to reproduce the failure.
- The mod-112 disclosure workflow is triggered where applicable — internal, regulatory (EU AI Act Article 73 for serious incidents), or public.
- The runbook is updated with the lessons.

The contract is *pre-committed* in the sense that phases 1–4 are known, roles are named, and the drill has been run before the fire. A fire that is met by *"what do we do now?"* is a fire the org will not press.

<!-- needs-research: verify the current EU AI Act Article 73 serious-incident reporting timeline (initial notification / follow-up / final report cadence) once the Article 73 implementing regulation is published in final form, and reflect the correct cadence in the incident-response contract. -->

## Testing the switch — you fire it or it does not work

A switch that has never been fired in production is a switch that will not work when needed. The testing discipline:

- **Table-top drills.** Quarterly (at minimum) walkthroughs of the fire scenarios with the on-call, the incident commander, the safety-review body, and the disclosure workflow team. Run mod-112's tabletop framework.
- **Live-fire drills.** Fire real switches at real scopes with pre-arranged windows and stakeholder awareness. Every switch is live-fired at least annually; higher-stakes switches more often.
- **Propagation-latency probes.** Automated probes that measure how quickly a fired switch propagates to every wrapper. The section-7 EACC field is a *tested* number, not a nominal one.
- **Un-fire drills.** The un-fire path is drilled equally. Un-firing without the drill is a bug waiting to happen.
- **False-positive rate audits.** The automated-fire pipeline's true-positive vs. false-positive rate is audited quarterly against ground-truth reviews.

Exercise 05 walks the switch design and the drill for one concrete deployment.

## Common misreadings to avoid

- **"The kill switch is a big red button."** It is a set of scoped, mode-parameterised controls with a fan-in graph and an incident-response contract. The big-red-button-only posture produces a switch nobody presses.
- **"We do not need a switch because the wrappers are already refusing bad calls."** The switch is for the case *the wrappers are compromised*, or a new bad-call class the wrappers do not classify, or a novel exploit whose signal takes time to encode. The wrappers are the containment; the switch is the *last-resort* containment.
- **"Automated fires are always bad."** They are bad when they fire without a false-positive budget and a human notification. They are correct when the false-positive rate is bounded, the mode is soft, and the human is paged. mod-108 designs the automation.
- **"Un-firing does not need a separate approval."** It does. Otherwise the model can wait out the fire until the on-call, tired of tickets, un-fires without a review.
- **"We can drill this later."** No. A switch that has not been drilled is not a control. The first-fire experience is the moment the operator learns the switch does not work as they thought.
- **"Post-fire, we return to the previous configuration."** Sometimes. Often the incident implies a new EACC. Un-firing to the *old* configuration is a decision to accept a repeat.

## Summary

- The kill switch is the *last-resort* containment control. It transitions the deployment to a contained state faster than the model can talk anyone out of it.
- Four fire modes: soft stop (refuse-only), freeze (session pause), quarantine (preserve evidence), rollback (compensating actions). The EACC section-7 codifies which are supported per scope.
- Every switch has an explicit false-positive cost analysis. Escalating approval widens the scope; narrow scopes and soft modes lower the false-positive cost enough that the switch is *usable*.
- Fan-in is a graph: human presses, monitor votes, downstream contracts, upstream contracts (RSP / Preparedness / FSF rollback triggers land here). Automated fires default to soft, page a human, and are bounded by a false-positive-rate budget.
- Org-wide orchestration lives in a switch registry; incident-crossing fires follow a runbook exercised in tabletop drills.
- Every fire triggers an incident-response contract (NIST SP 800-61 shape): containment → analysis → recovery → post-incident. mod-112's disclosure workflow engages where applicable.
- Test the switch: propagation-latency probes, un-fire drills, table-top and live-fire drills, false-positive rate audits.
- Exercise 05 walks the switch design and the drill for one concrete deployment.
