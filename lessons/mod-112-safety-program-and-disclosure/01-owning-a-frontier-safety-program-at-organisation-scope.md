# 01 — Owning a Frontier-Safety Program at Organisation Scope

## Motivation

The prior modules teach you how to *engineer* safety inside a specific deployment. mod-102 threat-models it; mod-104 stress-tests the jailbreak surface; mod-105 measures agent-level attack-success; mod-106 draws capability ceilings; mod-107 contains excessive agency; mod-108 monitors the runtime; mod-109 argues a safety case; mod-110 checks for deceptive alignment; mod-111 fuzzes it all at scale. Each is a *contract* on one artefact, one deployment, one review cycle.

A **Frontier-Safety Program** is what happens when an organisation has ten of those deployments, three model families, four jurisdictions, two regulator relationships, and one board-level committee that expects a single coherent story about *why the organisation believes it can keep shipping*. The safety-engineering role at this level does not just author artefacts; it operates the *cadence* those artefacts sit inside, the *routing* that gets them to a decision-maker, and the *sign-off chain* that binds the organisation to the decision made.

This chapter names what an organisation-scope frontier-safety program actually is. It pins the cadence (pre-training → mid-training → pre-deployment → post-deployment), the artefacts (safety cases, system cards, AISI reports, regulator-facing incident reports), the decisions (RSP tier / Preparedness scorecard / FSF critical-capability-level threshold), and the sign-off routing (Responsible-Scaling Officer / Preparedness Committee / Safety Board). It introduces the module-level artefact — the **Frontier Safety Program Charter (FSPC)** — that the rest of mod-112 builds against.

The load-bearing insight: a frontier-safety program is not a document library. It is an *operating rhythm* keyed on model-lifecycle events, with named decision bodies at each event, and with the safety-engineering role providing the evidence those bodies need in the shape they can read.

## Primary sources

- **[Anthropic Responsible Scaling Policy (RSP)](https://www.anthropic.com/rsp)** — read the current version end-to-end. The AI Safety Level (ASL) framing, the pre-registered evaluation thresholds, the Responsible-Scaling Officer role, and the mid-training checkpoint cadence are all pinned here. <!-- needs-research: pin the exact published version (e.g. RSP v2.x) and the URL of the current PDF; Anthropic revises the RSP regularly. -->
- **[OpenAI Preparedness Framework](https://openai.com/safety/preparedness)** — the scorecard shape, the tracked risk categories, and the Preparedness Committee routing. <!-- needs-research: pin the current published version of the Preparedness Framework (v2 released 2025) and any subsequent revisions. -->
- **[Google DeepMind Frontier Safety Framework (FSF)](https://deepmind.google)** — the Critical Capability Level (CCL) thresholds, the mitigation-tier framing, and the periodic-review cadence. <!-- needs-research: verify the exact URL of the current FSF publication (the framework has moved between deepmind.google/discover and a dedicated safety-portal path) and pin the published version. -->
- **[Frontier Model Forum](https://www.frontiermodelforum.org/)** — the cross-lab coordination body and its published working-group outputs. Read the published papers on incident-sharing norms and pre-deployment evaluation baselines. <!-- needs-research: pin the specific FMF publications the FSPC cites. -->
- **[EU AI Act — Regulation (EU) 2024/1689](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)** — Articles 55 (obligations for GPAI models with systemic risk), 56 (Code of Practice), 73 (serious-incident reporting). The regulatory floor the program operates against for EU-touching deployments. <!-- needs-research: verify EUR-Lex URL stability and confirm article numbering for the current consolidated text. -->

Version-pin all of these in the FSPC's references section. RSP, Preparedness, and FSF revise; the AI Act and its Code of Practice will accrete implementing acts.

## The module-level artefact — the Frontier Safety Program Charter (FSPC)

Every chapter in mod-112 contributes to a single deliverable — the **Frontier Safety Program Charter** for one specific organisation. The FSPC is what an executive committee, a Safety Board, a regulator, or an external auditor reads to understand *what safety means to this organisation, on what cadence, with what evidence, and with what decision-making body attached*.

An FSPC has seven sections:

1. **Scope.** The organisation, the model families, the deployments, and the jurisdictions the program covers. Named.
2. **Cadence.** The four safety-eval touch-points (pre-training, mid-training, pre-deployment, post-deployment) and the calendar / event triggers that fire them.
3. **Artefact register.** Every artefact the program produces — safety cases (mod-109), system cards (chapter 03), AISI submissions (chapter 04), Article 73 incident reports (chapter 04), tier-decision memos (chapter 02) — with owner, reviewer, cadence, and retention.
4. **Decision register.** Every decision the program supports — RSP tier / ASL determination, Preparedness scorecard rating, FSF CCL threshold determination, deploy / defer / rollback verdict — with the body that decides, the evidence they need, and the routing pathway.
5. **Sign-off routing.** The Responsible-Scaling Officer, Preparedness Committee, Safety Board, or equivalent — for this organisation. Named. With explicit authority per decision class.
6. **Incident-response contract.** The class taxonomy, the triage timeline, the coordination roster (legal, comms, AISI, regulators), and the disclosure shape (chapter 05).
7. **Certifications and program assurance.** External assurance the program leans on — ISO/IEC 42001, IAPP AIGP-carrying reviewers, ForHumanity IAA, BABL AI — and the assurance calendar.

Exercise 01 walks you through authoring the FSPC for one concrete organisation. The remaining chapters ship the internals.

The FSPC is the *engineering answer* to a governance question that many organisations answer only *ad hoc*. If a director cannot point at a document that says *"here is when we evaluate, here is what we produce, here is who signs, here is what we disclose,"* then the organisation does not have a program — it has a set of one-off responses. The FSPC's job is to name the rhythm so that the responses become predictable.

## The four safety-eval touch-points — the program's cadence

The program's rhythm is not "we evaluate before every launch." It is four distinct touch-points, each with a different question, a different audience, and a different failure mode if skipped.

### Touch-point 1 — Pre-training safety-eval

*Question.* Before we commit compute to training a run of a given scale, what does our current understanding of dangerous-capability elicitation (mod-106) predict this run will produce? What is our upper-bound estimate for the run's most dangerous-capability class?

*Audience.* The compute-approval body — often the Responsible-Scaling Officer plus a compute-governance function, or the Preparedness Committee at this pre-commit stage.

*Artefacts.* A pre-training safety memo referencing the last-completed model's DCER (mod-106), the elicitation-methodology assumptions the memo makes, and the tier-band prediction. Not a safety case (mod-109) — the training has not happened yet — but the *inputs* to a future safety case.

*Common failure.* The organisation only evaluates at pre-deployment. When the training run finishes and the model turns out to be a full tier above the prediction, the deploy-review calendar has no slack. The pre-training check is what preserves calendar slack.

### Touch-point 2 — Mid-training checkpoint eval

*Question.* At intermediate compute levels during a long training run, does the model's capability trajectory match the pre-training prediction? Has the run crossed an internal pre-registered threshold that requires escalation before proceeding?

*Audience.* The Responsible-Scaling Officer plus the safety-engineering leadership; escalation to the Preparedness Committee or Safety Board on threshold cross.

*Artefacts.* A mid-training checkpoint report — a delta from the pre-training memo. The mod-106 elicitation-methodology defence applied at this checkpoint. Explicit pre-registered thresholds (a specific benchmark score, a specific safety-eval regression, a specific capability elicitation result) that trigger escalation.

*Common failure.* The organisation runs mid-training evals but does not have *pre-registered* thresholds. When a threshold is crossed, the meaning of the crossing is negotiated after the fact, which is a compromised decision surface. Pre-registration is the discipline that keeps the mid-training check load-bearing.

### Touch-point 3 — Pre-deployment safety-eval

*Question.* Given the finished model, what is our final tier / scorecard / CCL determination? What deployment mitigations are we invoking? What is the deploy / defer / rollback verdict?

*Audience.* The tier-decision body — RSP path routes to the Responsible-Scaling Officer, Preparedness path routes to the Preparedness Committee, FSF path routes to the Safety Board equivalent. Also the external AISI evaluators (US AISI / UK AISI) where the deployment is in-scope for pre-deployment evaluation (chapter 04).

*Artefacts.* The safety case (mod-109) with its DCER (mod-106) evidence. The system card (chapter 03) with its disclosure shape. The tier-decision memo (chapter 02). The AISI submission (chapter 04). The Article 55 obligations documentation (chapter 04) for EU-touching deployments.

*Common failure.* The safety-eval is treated as a launch-gate rubber-stamp rather than a decision-support artefact. The tier-decision memo (chapter 02) is what forces the decision body to *actually decide*.

### Touch-point 4 — Post-deployment monitoring

*Question.* Once the model is deployed, does the runtime behaviour match the pre-deployment safety case? Are the monitors (mod-108) firing at the rate the safety case predicted? Are the mod-107 wrappers seeing the escalations at the rate the mod-104 / mod-105 red-team predicted?

*Audience.* Continuous — the safety-engineering on-call, the Preparedness Committee at a monthly cadence, the Safety Board at a quarterly cadence, the regulator at the cadence Article 55 and the Code of Practice require.

*Artefacts.* Monitor-fire reports; incident reports (chapter 05); Article 73 serious-incident reports (chapter 04); permanent regression-fixtures back-fed into mod-104 / mod-105 / mod-110 suites (chapter 05).

*Common failure.* Post-deployment monitoring degrades into a metrics dashboard nobody reads. The cadence — monthly Preparedness Committee review, quarterly Safety Board review — is what preserves the touch-point as a *decision surface* rather than a passive dashboard.

## The three tier / scorecard frameworks the program routes between

An organisation may operate under one of the three frontier-lab frameworks, or (increasingly) map its internal framework to each of them for cross-industry legibility. The three are not interchangeable, and the FSPC's decision-register names which framework governs which decision class.

### RSP — Anthropic Responsible Scaling Policy

*Shape.* AI Safety Levels (ASL-2, ASL-3, ASL-4, ...) with pre-registered evaluation thresholds per level and mandatory containment / deployment mitigations that must be in place *before* the level is reached. Governance sits under a Responsible-Scaling Officer with authority to defer deployment.

*What the safety-engineer contributes.* The pre-registered threshold evidence (mod-106 DCER), the containment posture (mod-107 EACC), the monitoring posture (mod-108), and the safety-case argument (mod-109). The tier-decision memo (chapter 02) synthesises these for the RSO.

### Preparedness Framework — OpenAI

*Shape.* A scorecard across risk categories (biological, chemical, cyber, autonomy, ...) with tracked-risk / medium-risk / high-risk / critical-risk gradings per category, and pre-committed mitigation and deployment posture per grading. Governance sits under a Preparedness Committee.

*What the safety-engineer contributes.* The scorecard entries per risk category, with the elicitation evidence and the mitigation-effectiveness argument. Chapter 02 details how the scorecard reads.

### FSF — Google DeepMind Frontier Safety Framework

*Shape.* Critical Capability Levels (CCLs) per capability domain, mitigation tiers, and a periodic-review commitment. Governance routes through the DeepMind safety governance structure.

*What the safety-engineer contributes.* The CCL determination per capability, the mitigation-tier engagement, and the periodic-review evidence.

Where an organisation operates under one of these, the FSPC references it directly and the tier-decision memo (chapter 02) uses its vocabulary. Where an organisation is smaller and operates under an internal framework, the FSPC's decision-register names both the internal framework and the mapping to the closest public framework, so that regulators, AISI, and Frontier Model Forum peers can read the decisions in a familiar shape.

## Sign-off routing — the three named bodies

*Responsible-Scaling Officer (RSO).* The single accountable individual for RSP-shaped tier decisions. Named in the FSPC. Authority to defer deployment. Reviews the tier-decision memo (chapter 02) with veto over launch.

*Preparedness Committee.* A body of senior technical and executive members with joint authority over the Preparedness scorecard. Reviews the scorecard, the risk-category evidence, and the deployment-mitigation package. Named members, meeting cadence, quorum requirements — pinned in the FSPC.

*Safety Board (or equivalent).* The board-level committee with fiduciary responsibility for the organisation's safety posture. Reviews the safety-case (mod-109) at pre-deployment; reviews the post-deployment monitoring report at quarterly cadence; is the final escalation for a threshold cross that no lower body resolves. Named members, meeting cadence, escalation contract — pinned in the FSPC.

The FSPC's decision-register maps each decision class to exactly one primary body and (optionally) an escalation path. Ambiguity here is a governance failure: if two bodies both think they own a decision, the decision is either duplicated (waste) or deferred (drift). The safety-engineering role's job is to force the mapping to be explicit.

## The artefact register — what the program produces

The FSPC's section-3 register enumerates every artefact and pins owner, reviewer, cadence, and retention. Illustrative rows:

- **Pre-training safety memo** — owner: safety-engineering lead per model family; reviewer: RSO; cadence: per training-run pre-commit; retention: 7 years or model lifetime + 3 years, whichever is longer.
- **Mid-training checkpoint report** — owner: safety-engineering lead; reviewer: RSO with Preparedness Committee escalation; cadence: per checkpoint; retention: as above.
- **Pre-deployment safety case** (mod-109) — owner: safety-engineering lead; reviewer: RSO + Safety Board; cadence: per model version launch; retention: as above.
- **System card** (chapter 03) — owner: safety-engineering + communications; reviewer: RSO + external counsel; cadence: per model version launch; retention: public-permanent.
- **AISI pre-deployment submission** (chapter 04) — owner: safety-engineering lead; reviewer: RSO + legal + external-affairs; cadence: per model version submitted for AISI evaluation; retention: as above, with regulator-copy provisions.
- **Article 73 serious-incident report** (chapter 04) — owner: safety-engineering incident-response lead; reviewer: legal + external-affairs + RSO; cadence: per incident, within the Article 73 timeline; retention: regulator-required.
- **Quarterly Safety Board report** — owner: RSO; reviewer: Safety Board; cadence: quarterly; retention: as above.

Each row pins the artefact-authoring role (which is often a mod-112 role) *and* the sign-off routing (which is the FSPC's section-5). The register is the operational discipline that ties the program's cadence to the organisation's decision-making structure.

## Interfaces

- **mod-106 (Dangerous-Capability Eval).** The DCER is the load-bearing evidence input to the pre-training memo, the mid-training checkpoint report, the pre-deployment safety case, and the tier-decision memo (chapter 02). mod-106 produces the evidence; the FSPC's cadence keys off its production.
- **mod-107 (Excessive-Agency Containment).** The EACC is the load-bearing containment evidence in the safety case and the system card. mod-107 authors the containment; the FSPC discloses against it.
- **mod-108 (Guardrails and Monitors).** The monitor-fire stream is the load-bearing post-deployment-monitoring evidence. Post-deployment monitoring (touch-point 4) consumes it.
- **mod-109 (Safety Cases).** The safety case is the artefact-shape the pre-deployment safety-eval produces. The FSPC's artefact register names the safety-case as one of its primary outputs.
- **mod-110 (Control / Deception).** Deception-detection findings feed the incident-response classifier (chapter 05) and the tier-decision memo's residual-risk section.
- **mod-111 (Automated Red Team).** Red-team findings that survive triage become permanent regression-fixtures fed back into mod-104 / mod-105 / mod-110 suites through chapter 05's back-feed workflow.
- **`ai-evaluation-engineer` (peer / next-up, level 35).** The release-assurance packaging role that composes the safety case, system card, and AISI submission for a specific release. Chapter 06 draws the boundary.
- **`senior-ai-governance-architect` (level 50).** The control-library architecture role the FSPC plugs into. Chapter 06 draws the boundary.

## Common misreadings to avoid

- **"The program is a policy document."** The program is an *operating rhythm*. A policy document that is not tied to a cadence, a decision body, and a sign-off routing is a shelf artefact. The FSPC's job is to prevent that outcome.
- **"Pre-registered thresholds are handcuffs."** They are the mechanism that prevents post-hoc rationalisation of a threshold-cross. An organisation that reserves the right to *reinterpret* thresholds after the fact does not have a program; it has an audit surface.
- **"The safety-engineer authors the safety case; the sign-off is the CEO's problem."** No. The safety-engineer authors the case *and* names the reviewer *and* forces the reviewer to sign the artefact. A safety case without a signed reviewer is not evidence; it is a draft.
- **"We can pick one framework and ignore the others."** The organisation may operate under one primary framework, but regulators, AISI, Frontier Model Forum peers, and external auditors will read across all three. The FSPC's cross-framework mapping is what preserves legibility.
- **"Post-deployment monitoring is mod-108's job, not mod-112's."** mod-108 owns the monitors. mod-112 owns the *cadence at which the Preparedness Committee and Safety Board read them*. Different craft; both required.

## Summary

- A Frontier-Safety Program is an *operating rhythm* — four safety-eval touch-points (pre-training / mid-training / pre-deployment / post-deployment), a named artefact register, a named decision register, and a named sign-off routing.
- The module-level artefact is the **Frontier Safety Program Charter (FSPC)**. It has seven sections and is signed by the RSO or equivalent.
- Three frontier-lab frameworks — Anthropic RSP, OpenAI Preparedness, Google DeepMind FSF — provide the tier / scorecard / CCL vocabularies. The FSPC references the operating framework and maps to the others for cross-industry legibility.
- Sign-off routes through three named bodies — the Responsible-Scaling Officer, the Preparedness Committee, the Safety Board — with unambiguous per-decision authority.
- The FSPC composes: mod-106 evidence, mod-107 containment, mod-108 monitoring, mod-109 safety cases, mod-110 residual-risk findings, mod-111 red-team back-feeds. It is the organisation-scope frame those modules feed.
- Exercise 01 asks you to author an FSPC for one concrete organisation. Chapters 02–06 ship the internals.
