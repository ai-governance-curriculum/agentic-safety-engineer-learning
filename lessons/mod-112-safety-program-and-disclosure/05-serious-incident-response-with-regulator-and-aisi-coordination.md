# 05 — Serious-Incident Response with Regulator and AISI Coordination

## Motivation

A serious frontier-safety incident is what the program (chapter 01) is *for*. The prior chapters engineer the surfaces that make an incident manageable — the artefact register, the tier-decision memo, the system card, the regulator submissions. This chapter walks the operating discipline that runs when one of those surfaces has to be filed *quickly*, against a real event, on a statutory clock, with named coordination across legal, communications, AISI, and (potentially) multiple national regulators.

Serious-incident response is where a frontier-safety program stops being a set of documents and becomes an operating capability. The safety-engineering role at this level is often the *incident commander* for the technical arm of the response, or the *lead evidence-collector* under an incident commander drawn from external affairs or legal. Either way, the safety-engineer authors the technical narrative, drives the mod-107 rollback contract, files the Article 73 initial notification, coordinates with AISI on any pre-existing evaluation relationship, and back-feeds the finding into the mod-104 / mod-105 / mod-110 regression suites so the class of incident does not recur.

The load-bearing insight: serious-incident response is the *composition point* for the entire safety-engineering craft. Every prior module contributes — mod-102's threat model classifies the incident, mod-104 / mod-105 / mod-110's suites are where the fixture back-feed lands, mod-106's DCER may have to be revised, mod-107's EACC drives the containment response, mod-108's monitors are (usually) how the incident was detected, mod-109's safety case may be invalidated by the incident, mod-111's fuzzing may have to be re-run against the fixture. The response is where the module boundaries collapse into a single operational cadence.

## Primary sources

- **[EU AI Act Article 73 — serious-incident reporting](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)** — the statutory reporting obligations. Chapter 04 walks the disclosure shape; this chapter walks the *response* that drives the disclosure. <!-- needs-research: verify Article 73's exact language and timing in the current consolidated text. -->
- **[Anthropic Responsible Scaling Policy — incident-response commitments](https://www.anthropic.com/rsp)** — the RSP includes commitments around incident response and rollback. <!-- needs-research: pin the current RSP version's incident-response section. -->
- **[OpenAI Preparedness Framework — incident-response commitments](https://openai.com/safety/preparedness)** — the Preparedness Framework's post-deployment posture. <!-- needs-research: pin the current framework version's incident-response section. -->
- **[NIST SP 800-61r3 — Computer Security Incident Handling Guide](https://csrc.nist.gov/publications/detail/sp/800-61/rev-3/final)** — the security-incident-handling reference posture. Adapted below for AI-safety incidents. <!-- needs-research: verify current revision status — 800-61 revised to 800-61r3 (or similar); pin the exact revision. -->
- **[Frontier Model Forum — incident-sharing publications](https://www.frontiermodelforum.org/)** — the cross-lab coordination body's published work on incident-sharing norms. <!-- needs-research: pin the specific FMF publications on incident-sharing norms and coordinated disclosure. -->
- **[UK AISI](https://www.aisi.gov.uk/)** and **[US AISI (NIST)](https://www.nist.gov/aisi)** — the pre-existing evaluation relationships that shape coordination. <!-- needs-research: verify the specific AISI publications on incident-coordination and any published memoranda of understanding. -->

Version-pin the frameworks. Every framework revises its incident-response commitments.

## The incident-class taxonomy

Different incident classes trigger different response paths. The FSPC's section-6 incident-response contract (chapter 01) enumerates the classes; this chapter names the five that dominate a level-40 safety-engineer's incident load.

### Class 1 — Autonomy exceedance

*Definition.* The deployed agent takes actions outside the mod-107 EACC's scope — succeeds in bypassing a capability gate, escapes a sandbox, exceeds a blast-radius cap, executes a plan the containment posture argued it could not.

*Signals.* mod-108 monitor fires on containment-bypass patterns; mod-107 wrapper's outcome-verification returns *side-effect-widened*; a customer report of an action the agent should not have taken.

*Response shape.* Immediate mod-107 kill-switch fire on the affected deployment surface. Rollback to a prior model version or a narrowed capability-gate configuration. Article 73 triggerable depending on the exceedance's consequence (a widespread infringement or serious harm is Article 73; a contained near-miss is documented but may not trigger).

### Class 2 — Dangerous-capability disclosure

*Definition.* An interaction with the deployed model produces output that materially uplifts a user's ability to cause catastrophic harm — CBRN synthesis guidance, novel cyber-offense payload, mass-manipulation content at scale.

*Signals.* mod-108 monitor fires on dangerous-capability patterns; a red-team finding (mod-111) that surfaces post-launch; a third-party researcher report.

*Response shape.* Immediate capability-gate widening — the specific prompt family is either refused or routed through a narrower elicitation. Rollback may or may not be needed depending on the specificity of the finding. Article 73 highly likely; AISI coordination expected if a bilateral evaluation relationship exists; Frontier Model Forum coordination expected for cross-industry findings.

### Class 3 — Jailbroken agent tool-abuse

*Definition.* An agent deployment (mod-107 shape) is jailbroken via prompt injection (mod-103 / mod-104) such that the tool wrapper's outcome-verification catches the abuse but only after a partial side-effect has completed. The distinguishing feature from class 1: the failure is *jailbreak-driven*, not *containment-flaw-driven* — the containment behaved correctly but the model's willingness to execute the plan was elevated by the injection.

*Signals.* mod-108 monitor fires on injection-abuse patterns; mod-107 wrapper's quarantine returns for side-effect-widened calls; a customer report of an unexpected side effect that traces to an injected instruction.

*Response shape.* Two-legged. (a) Immediate containment tightening on the affected surface (mod-107 EACC update). (b) Immediate mod-104 / mod-105 fixture back-feed so the injection class is caught in the next evaluation. Article 73 possible depending on consequence.

### Class 4 — Deception detection

*Definition.* Post-deployment behaviour consistent with the deceptive-alignment concerns mod-110 studies — the model behaves differently in what it identifies as an evaluation context vs a deployment context, or takes actions that widen its own affordances against operator intent.

*Signals.* mod-110 evaluation regression (a fixture that previously passed now fails); mod-108 monitor fires on eval-vs-deployment behavioural divergence patterns; a red-team finding that surfaces post-launch.

*Response shape.* Highest-severity class. Rollback almost certainly required; capability-gate configuration tightened; tier determination (chapter 02) revised. Article 73 highly likely, at potentially the widest jurisdiction scope. Safety Board notification; FMF and AISI coordination expected.

### Class 5 — Safety-eval regression

*Definition.* An evaluation the model previously passed (in the pre-deployment safety case, mod-109) now fails — either because the model was updated (fine-tuning, distillation, adapter changes) or because the evaluation was strengthened (mod-106 elicitation improvement) or because an evaluation-condition drifted (benchmark update, harness change).

*Signals.* Scheduled re-evaluation catches the regression; a fixture back-fed from a previous incident (this chapter's back-feed workflow) catches it during CI-integration.

*Response shape.* Deployment mitigation tightening or rollback, depending on the severity and the specific eval. Tier determination revision if the regression crosses a pre-registered threshold. Article 73 possible depending on consequence.

## The triage timeline

Every serious incident runs on a triage timeline. The FSPC's section-6 pins the concrete durations for a specific organisation; the following is a defensible default for a level-40 posture:

- **T+0 to T+30 minutes — Detection.** Monitor fire, customer report, red-team finding, or scheduled-evaluation result reaches the safety-engineering on-call. The on-call opens an incident record in the FSPC's incident-response tracker.
- **T+30 minutes to T+2 hours — Initial containment.** The on-call determines whether the mod-107 kill-switch fires on the affected surface, whether a partial capability-gate tightening is sufficient, or whether the response can proceed under monitored observation. The initial containment decision is *reversible* — over-containment is fine, and preferable to under-containment.
- **T+2 to T+6 hours — Class determination and stakeholder notification.** The incident's class per the taxonomy above is determined. Internal stakeholders are notified per the FSPC's routing: safety-engineering leadership, RSO (or equivalent), legal, external affairs. The FMF incident-sharing channel is opened if applicable.
- **T+6 to T+24 hours — Article 73 initial notification.** For incidents that clear the Article 73 threshold, the pre-authored initial-notification template (chapter 04) is filled and filed within the statutory initial window. AISI notification is filed in parallel if a bilateral evaluation relationship exists.
- **T+24 hours to T+72 hours — Root-cause analysis.** The safety-engineering incident-response lead drives the technical root-cause analysis. Evidence collected from mod-107 audit logs, mod-108 monitor logs, and (where applicable) mod-106 re-elicitation runs. Root cause pinned per the taxonomy.
- **T+72 hours to Article 73 full-report deadline — Full remediation.** Rollback executed if not already done; capability-gate configuration updated; regression-fixture authored (below); safety-case (mod-109) updated; tier-decision memo (chapter 02) revised.
- **After Article 73 full-report filing — Regression back-feed and post-mortem.** The fixture is back-fed into mod-104 / mod-105 / mod-110 suites (below). The organisation-internal post-mortem is authored, presented to the Safety Board equivalent, and appended to the FSPC's incident-response log.

The times are defensible defaults; the FSPC pins the organisation-specific durations. The discipline is that *every* stage has a named owner, a named artefact, and a named handoff.

## The coordination roster

A serious incident coordinates across roles that do not normally interact daily. The FSPC's roster names the counterparts; this chapter names the interactions.

- **Safety-engineering incident commander.** Named. Authority to fire the mod-107 kill switch, initiate rollback, and open the incident record. Reports to the RSO (or equivalent) during the incident.
- **Legal counsel — regulatory.** Reviews the Article 73 notification, the Code-of-Practice disclosure, and any AISI-facing communication. Owns disclosure-obligation determination across jurisdictions.
- **Legal counsel — litigation.** Reviews the incident record for litigation exposure. Advises on what evidence is retained under what constraints.
- **External affairs / regulator relationship.** Delivers the Article 73 notification. Coordinates the AISI-facing communication. Manages Member State authority relationships for EU-touching incidents.
- **Communications.** Authors the user-facing and press-facing content. Reviews the system-card addendum (chapter 03) that discloses the incident to the public.
- **AISI coordination lead.** Where a bilateral evaluation relationship exists, the internal lead for the AISI relationship. Coordinates the AISI notification, the response to AISI's evaluation of the incident, and the follow-up methodology contribution.
- **Frontier Model Forum liaison.** Where the incident's class is cross-industry-relevant, the internal lead for FMF coordination. Files the FMF incident-sharing notification.
- **RSO / Preparedness Committee / Safety Board equivalent.** The named accountable decision body per the FSPC's sign-off routing. Notified per the routing; authorises tier-determination revisions.

The roster is not a call-tree; it is a coordination *contract*. Each named role has a specific decision authority during the incident and specific artefacts they own. Ambiguity here means the response drifts into meetings-about-meetings; explicit roles keep the response operating.

## The disclosure-engineering shape during an incident

Chapter 04 walks the regulator-facing disclosure surfaces. During an incident, the disclosure-engineering discipline compresses:

- **The Article 73 initial notification** is a *pre-authored template* filled with the incident's specifics within hours. The template is the discipline; a fill-in-the-blanks is the operationally achievable shape.
- **The Article 73 full report** is authored during the T+72 hours to full-report-deadline window, with legal / external affairs / communications co-review. The root-cause analysis, mitigation-actions-taken, and future-preventive-measures sections are the safety-engineer's craft.
- **The AISI notification** is a companion, often shorter, sent through the AISI relationship channel. Its purpose is coordination — AISI may want to evaluate the incident's underlying capability change, and early notification enables that.
- **The FMF incident-sharing notification** is optional but load-bearing for cross-industry safety. It shares the incident's shape (not the payload) so peer organisations can check whether they have the same class exposure.
- **The system-card addendum** is public and appears alongside or after the regulator submissions, depending on the incident's disclosure-timing constraints. The card carries the incident summary, the mitigation, and the tier-determination status.

Every disclosure surface has a redaction discipline (chapter 04's shape / payload separation applies) and a co-authorship chain (legal / external affairs / communications). During an incident, the pre-authored templates and pre-cleared review paths are what make the co-authorship achievable on the statutory clock.

## The permanent regression-fixture back-feed workflow

Every serious incident produces a *fixture*: a specific, checkable evaluation artefact that would have caught the incident had it existed pre-launch. The back-feed workflow inserts the fixture into the appropriate evaluation suite so the class of incident is caught in the next evaluation cycle.

The workflow is the operational discipline that keeps a frontier-safety program *learning* rather than *repeating*. Every incident that surfaces at cost should improve the program's ability to catch the next incident cheaply.

### Which suite the fixture back-feeds into

The fixture's home suite depends on the incident class:

- **Class 1 — Autonomy exceedance.** Back-feeds into the **mod-105** agent-attack-surface suite (attack-success-rate evaluations) and the **mod-107** EACC red-team suite. The fixture is a specific attack that must be caught by the wrapper.
- **Class 2 — Dangerous-capability disclosure.** Back-feeds into the **mod-104** jailbreak suite (the prompt shape that produced the disclosure) and the **mod-106** DCER suite (the capability evaluation is strengthened with the new elicitation regime).
- **Class 3 — Jailbroken agent tool-abuse.** Back-feeds into **mod-104** (the injection shape) and **mod-105** (the agent-level chain the injection enabled). Both suites gain fixtures.
- **Class 4 — Deception detection.** Back-feeds into the **mod-110** control / deception suite. This is the highest-severity back-feed class; a fixture here is a permanent addition to the deception-detection surface.
- **Class 5 — Safety-eval regression.** Back-feeds into the evaluation suite where the regression surfaced. The fixture is the specific configuration change (elicitation strengthening, benchmark update) that produced the regression.

### The back-feed engineering shape

- **Fixture specification.** A precise, reproducible artefact — the prompt or the sequence, the elicitation configuration, the tool inventory, the expected behaviour (pass / fail criterion). Version-pinned and hashed.
- **Fixture provenance.** A pointer back to the incident record so the fixture's origin is auditable. This is the trace that lets a future safety-engineer understand *why* the fixture exists.
- **Fixture sensitivity classification.** For dangerous-capability fixtures, the fixture may itself be sensitive material (a CBRN payload prompt, a novel cyber-offense sequence). Fixture-storage and fixture-execution access are controlled under the same posture as the payload evidence (chapter 04's redaction discipline).
- **Fixture-check cadence.** The suite the fixture lives in has its own cadence (CI-integration for cheap fixtures, mid-training checkpoint eval for expensive fixtures). The fixture is part of that cadence.
- **Fixture retirement.** Fixtures are retired only when a broader mitigation supersedes them (a stronger elicitation regime catches the whole class) *and* a superseding-fixture-review approves the retirement. Ad-hoc fixture removal is a governance failure.

Exercise 06 walks the back-feed workflow for a specific incident class. The workflow is the discipline that keeps the program's cost-of-catching-recurring-incidents low.

## Interfaces

- **Chapter 01 (program).** The FSPC's section-6 pins the incident-response contract, the class taxonomy, the triage timeline, and the coordination roster.
- **Chapter 02 (tier memo).** A serious incident often revises the tier determination. The revised memo is authored during the incident-response T+72-hours-plus window.
- **Chapter 03 (system card).** The system-card addendum (or new card version) discloses the incident to the public. The disclosure shape is the reduction of the incident's finding to the card's audience.
- **Chapter 04 (regulator disclosure).** The Article 73 notifications and the Code-of-Practice disclosures are the primary regulator-facing surfaces during the incident. Chapter 04's pre-authored templates and redaction discipline drive.
- **mod-104 / mod-105 / mod-110 (evaluation suites).** The permanent regression-fixture back-feed target. Every incident produces at least one fixture that lands in one of these suites.
- **mod-107 (EACC).** The kill switch is fired here; the EACC is updated here; the wrapper's audit logs are the primary evidence for root-cause analysis.
- **mod-108 (monitors).** The monitors are (usually) the detection surface. Monitor patterns are strengthened during the incident based on the finding.
- **mod-109 (safety case).** The safety case is potentially invalidated by the incident. Post-incident, the case is revised or a superseding case is authored.
- **mod-111 (automated red team).** The fixture back-fed into mod-104 / mod-105 / mod-110 is subsequently fuzzed at scale by mod-111 to widen the fixture's coverage class.
- **`ai-infra-security` (peer, level 35).** The audit-log integrity chain that anchors the root-cause analysis routes through the peer role. The signing service, the WORM store, and the workload-identity fabric are theirs.

## Common misreadings to avoid

- **"Incident response is chaos — no point pre-authoring."** The opposite. Pre-authored templates, pre-cleared review paths, and a named coordination roster are what turn chaos into a triageable response on a statutory clock. An unprepared incident response is one where the statutory clock runs out during internal negotiations.
- **"The safety-engineer runs the whole incident."** No. The safety-engineer runs the technical arm and authors the technical narrative. Legal, external affairs, communications, and the incident-decision body (RSO / Preparedness Committee / Safety Board) each have named roles. The FSPC's coordination roster names them all.
- **"The back-feed is nice-to-have."** No. Without the back-feed, the class of incident recurs. The back-feed is what turns an incident from a *one-time cost* into a *permanent capability improvement*. Skipping it is one of the most common program-degradation modes.
- **"Article 73 only fires for the worst incidents."** Article 73's threshold is specific and defined in the Act. The safety-engineer's job is to know the threshold and to file when triggered, not to negotiate it during the incident. Pre-authored templates presuppose triggering; the internal review determines whether the trigger holds.
- **"AISI is not a coordination partner during an incident."** For organisations with a bilateral AISI evaluation relationship, AISI is a coordination partner precisely because a pre-existing relationship exists. Silence during an incident damages the relationship; timely notification strengthens it.
- **"Deception-detection incidents are hypothetical."** They are increasingly non-hypothetical (mod-110 chapter references the empirical work). The response contract for class 4 must exist before the incident, because the incident's disclosure-engineering shape is the hardest of the five classes.

## Summary

- Serious-incident response is the composition point for the entire safety-engineering craft. Every prior module contributes; the response is where they operate under a statutory clock.
- Five incident classes: **autonomy exceedance**, **dangerous-capability disclosure**, **jailbroken agent tool-abuse**, **deception detection**, **safety-eval regression**. Each has a distinct response shape.
- The triage timeline runs from detection at T+0 through Article 73 initial notification within hours through full-report filing within the statutory window through permanent regression-fixture back-feed.
- The coordination roster names the safety-engineering incident commander, legal counsel (regulatory and litigation), external affairs, communications, AISI coordination lead, FMF liaison, and the tier-decision body.
- The disclosure-engineering shape during an incident uses **pre-authored templates**, **pre-cleared review paths**, and the **shape / payload redaction discipline** from chapter 04.
- The **permanent regression-fixture back-feed workflow** inserts each incident's fixture into mod-104 / mod-105 / mod-110 as appropriate. This is what keeps the program *learning* rather than *repeating*.
- Exercise 05 walks an incident-response drill; exercise 06 walks the back-feed workflow. Chapter 06 draws the boundaries and pins the certifications and hiring-signal shape.
