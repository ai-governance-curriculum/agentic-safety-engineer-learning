# 06 — Authoring the Dangerous-Capability Evaluation Report (DCER)

## Motivation

The Dangerous-Capability Evaluation Report is the module's terminal artefact. It is what the RSP Responsible Scaling Officer reads before approving a release. It is what the OpenAI Preparedness Safety Advisory Group reviews for its scorecard verdict. It is what the FSF review body cites in its evaluation report. It is what UK AISI + US AISI cite in their pre-deployment write-up. It is what the EU AI Office ingests as evidence under the GPAI Code of Practice safety chapter and the AI Act Article 55 systemic-risk regime. It is the load-bearing input to the mod-109 safety case and to the mod-112 disclosure workflow.

Chapters 02–05 produced the capability panels; chapter 01 produced the elicitation-protocol shape; this chapter folds them into the report. The report has a specific structure because its readers are specific — a mixed audience of safety-review technical leads, safety-governance program managers, and (indirectly) regulators. Every section is engineered against those readers' review criteria.

## Report structure

The DCER has ten sections. Each is required; if a section is empty, the report is not shippable.

### Section 1 — Executive summary and verdict

The one-page summary the safety-review body reads first. It names:

- The **target model** (id, snapshot).
- The **deployment scope** the DCER assesses (the API tier, the product, the customer segment — because the same model may ship at different tiers with different thresholds).
- The **frameworks** the report is shaped against — RSP with the ASL under evaluation, Preparedness Framework with the tracked-category tier, FSF with the CCL. Usually all three, cross-mapped.
- The **panel verdicts** — one row per capability panel (CBRN-bio, CBRN-chem, CBRN-rad, CBRN-nuc, cyber-offense, autonomy, persuasion / manipulation, self-exfiltration). Each verdict is one of: **cleared below**, **approached with mitigations**, **crossed above with rollback trigger engaged**.
- The **overall recommendation** — release / release-with-mitigations / hold-and-mitigate.
- The **elicitation-gap statement** — a one-sentence framing of the residual claim.
- The **pre-registration reference** — cite the pre-registration document that pinned the protocol and thresholds before the run.
- The **sign-off block** — the safety-team lead, the reviewers (external safety-eval firms where engaged, AISI where the deployment ships to an AISI window), and the roles that will inherit the report (mod-109 safety case, mod-112 disclosure).

### Section 2 — Threat-model summary

For each capability panel, the tier-defining threat model — actor archetype, attack pathway, horizon. Reference the framework tier the panel is measured against.

This section is short. Its function is to force the reader to hold the threat model in mind while reading the panel results. If the reader ever wonders "why does this specific measurement matter," they should be one page back from the answer.

### Section 3 — Elicitation protocol (as pre-registered)

The **elicitation pre-registration block** from chapter 01. Every field filled in. This is the section a reviewer challenges when the panel result is uncomfortable — was the protocol strong enough — so the section carries:

- The **axes swept** (best-of-N, fine-tune, tool-use, scaffolding), each with its specific configuration.
- The **human-effort budget** applied — hours of expert red-team elicitation, broken down by panel.
- The **domain-expert engagement** — organisation, scope, scope-of-adjudication, agreement measurement.
- The **judge specifications** — rubric, calibration, per-judge agreement CI.
- The **sample-size plan** — deferred to `model-evaluation-engineer` peer role, cited.

### Section 4 — Capability panel results

One subsection per panel. Each subsection follows the same shape:

- **Threat model recap** (one paragraph).
- **Measurement composition run** — which benchmark subsets, which paired-baseline trials, which expert-graded task sets, versions pinned.
- **Elicitation-axis results** — per-axis ceiling numbers with CIs.
- **Best-effort ceiling** — the composite headline number for the panel.
- **Autonomy scaling curve** (autonomy panel only).
- **Autonomy tier × ATT&CK tactic coverage table** (cyber panel only).
- **Refusal-vs-capability separation** (persuasion / self-exfil panels).
- **Elicitation-gap accounting** — statistical + capability-specific, per the chapter 01 shape.
- **Threshold comparison** — the ceiling with its CI compared to the pre-registered threshold.
- **Verdict** — cleared / approached / crossed.
- **Rollback trigger** — if crossed, which control engages and which mod-112 workflow fires.

### Section 5 — Comparative results across model releases

A same-axis comparison against prior model releases (the frontier lab's own prior model, the peer labs' most recent public releases where the panels are comparable) and against the doubling-time overlay (autonomy panel).

This is the section the safety-review body uses to sanity-check the panel results against the industry-wide trend. Anomalous results — a large jump not shared by adjacent models, or a plateau against the trend — get flagged for follow-up.

### Section 6 — Deployed-stack ceiling

The panel results are the *elicited* ceiling — the model's capability under adversarial best-effort elicitation. The deployed stack has additional layers: classifier guards (mod-108), Constitutional Classifiers, monitor-wrapped tools (mod-107), rate-limits, refusal-classifier post-processing. The deployed-stack ceiling is the ceiling with those layers active.

The DCER reports both: the elicited base-model ceiling (the panel result) and the deployed-stack ceiling (the same elicitation with the deployed defence layers in place). The safety-review body compares each against the threshold; the tier decision may hinge on the deployed stack meeting the threshold that the base model does not.

Mod-108's guardrail engineering produces the deployed-stack layers; this section cites those artefacts by ID and describes the layered-defence adaptive-attack survival curves (mod-108 chapter language).

### Section 7 — Residual-gap claim

The consolidated residual claim across panels — a paragraph per panel stating what a plausibly-motivated attacker with what plausible follow-up effort could achieve above the current ceiling, and what the confidence in that residual is.

Two structural rules for this section:

1. **Every residual-gap claim is falsifiable.** The follow-up experiment that would raise the ceiling above the current claim is named — even if the safety-team is not authorised to run it, the reviewer must know what would run it.
2. **The residual gap cites the mod-110 control-evaluation companion** where the panel result depends on the model's *expressed* inability (self-exfil propensity, sandbagging-adjacent CBRN refusal). Chapter 07 develops the boundary.

### Section 8 — Rollback triggers and mitigation contract

If any panel returned "approached with mitigations" or "crossed above," this section names:

- The specific mitigation that engages — the mod-108 classifier / monitor artefact, the mod-107 containment artefact, the deployment-scope reduction (e.g., API-only, no fine-tune access).
- The rollback trigger — the condition that would force a rollback of the mitigation (a jailbreak-of-the-classifier report, a bypass-of-the-containment finding, a customer misuse report).
- The mod-112 disclosure workflow that fires if the trigger engages.
- The escalation path — which role receives the rollback signal, at what latency, with what authority to halt deployment.

### Section 9 — Deferral contract

The DCER's header carries this shape (see below); this section instantiates it with concrete artefact IDs:

```yaml
deferral_contract:
  artifact: dcer-<deployment-id>-<version>
  level_of_owner: 40   # agentic-safety-engineer
  deferred_down_to:
    - level: 25
      role: ai-risk-engineer
      scope: "general adversarial-ML evaluation methodology (garak, ART, PyRIT, Promptfoo — this module's ceilings compose with those measurements)"
      artefact_ids: [<ai-risk-engineer artefact IDs>]
  deferred_sideways_to:
    - level: 30
      role: model-evaluation-engineer
      scope: "best-of-N CI derivation, item-response-theory difficulty accounting, judge-vs-human agreement CI, sample-size planning, elicitation-gap statistical extrapolation"
      artefact_ids: [<peer artefact IDs>]
    - level: 30
      role: ai-eval-engineer
      scope: "Inspect harness / eval-plumbing integration, trace collection, judge orchestration"
      artefact_ids: [<peer artefact IDs>]
    - level: 30
      role: fine-tuning-engineer
      scope: "fine-tune-elicitation implementation for the base model (LoRA, RLHF-style tuning)"
      artefact_ids: [<peer artefact IDs>]
    - level: 35
      role: ai-infra-security
      scope: "isolation environment for A3 drills, sandbox construction, egress policy"
      artefact_ids: [<peer artefact IDs>]
    - external
      role: cbrn_domain_expert_grader
      scope: "adjudication of expert-graded CBRN synthesis-planning tasks; grading rubric co-authored; grader agreement CI"
      artefact_ids: [<grading-firm engagement contract ID>]
    - external
      role: cyber_domain_expert_grader
      scope: "adjudication of exploit-writing tasks and CTF task grading beyond automated grader"
      artefact_ids: [<grading-firm ID>]
    - external
      role: irb_or_ethics_review_body
      scope: "human-subject-research approval for long-format persuasion trials"
      artefact_ids: [<IRB approval reference>]
  peer_dependency:
    - module: mod-110
      scope: "adversarial-alignment control evaluations (sandbagging, alignment-faking, situational-awareness, sleeper-agent) — the DCER's residual-gap claim on self-exfil propensity and CBRN refusal defers to mod-110 for the trustworthiness of expressed-inability results"
      artefact_ids: [<mod-110 artefact IDs>]
  consumed_by:
    - level: 35
      role: ai-evaluation-engineer
      scope: "release-assurance packaging, audit-trail wiring"
      artefact_ids: [<downstream artefact IDs>]
    - module: mod-109
      role: agentic-safety-engineer (self, mod-109 chapter)
      scope: "GSN safety-case authoring — inability-argument leg citing DCER ceilings"
      artefact_ids: [<mod-109 artefact ID>]
    - module: mod-112
      role: agentic-safety-engineer (self, mod-112 chapter)
      scope: "disclosure workflow — system-card sections, AISI-report input, EU AI Act Article 55/73 filings"
      artefact_ids: [<mod-112 workflow ID>]
    - level: 50
      role: senior-ai-governance-architect
      scope: "control-library update — the mitigations engaged for approached/crossed verdicts feed the cross-jurisdiction control catalog"
      artefact_ids: [<architect artefact IDs>]
    - level: 60
      role: head-of-ai-governance
      scope: "board reporting, regulator engagement"
      artefact_ids: [<program artefact IDs>]
```

### Section 10 — Appendices

- **Appendix A — Pre-registration document(s), signed off before the run.**
- **Appendix B — Framework-specific formatting** — the RSP Capability Report shape (if the deployment ships against RSP), the Preparedness Framework scorecard-slice shape, the FSF evaluation-report shape. Each framework has its expected structure; the DCER's core carries the evidence, and the framework-shaped output is a derivation.
- **Appendix C — AISI submission format** — if the deployment ships to an AISI pre-deployment window, the AISI-expected submission format.
- **Appendix D — EU AI Act Article 55 / GPAI CoP safety-chapter input** — the evidence formatted for regulator submission where relevant.
- **Appendix E — Access-controlled artefact manifest** — payload store handles for the harmful-content artefacts referenced by the panels.
- **Appendix F — Change log** — every DCER is versioned; this appendix carries the diff against the prior version.

## Verdict rubric

The verdict language is not decorative. Its uses across the frontier safety frameworks are load-bearing.

### "Cleared below"

- The best-effort ceiling with its upper CI bound sits below the pre-registered threshold, under all elicitation axes swept.
- The residual-gap claim's plausible upper bound also sits below the threshold.
- The deployed-stack ceiling is at least as low.
- The reviewer's follow-up-experiment procedure would be expected to reproduce the below-threshold result.

Recommendation: release under the tier's default mitigations. The DCER is filed for the release-assurance workflow.

### "Approached with mitigations"

- The best-effort ceiling's upper CI bound is *not* clearly below the threshold, but the deployed-stack ceiling is.
- The deployed-stack layers (mod-108 classifiers, mod-107 containment, deployment-scope reduction) are load-bearing for the tier decision.
- Rollback triggers on each mitigation are named and functional.

Recommendation: release with the named mitigations, and instantiate the rollback triggers. Escalation path documented. Re-run the DCER on a specified cadence.

### "Crossed above with rollback trigger engaged"

- The best-effort ceiling's *lower* CI bound is at or above the threshold, or the deployed-stack ceiling remains above the threshold.
- The pre-registered rollback trigger has engaged.
- Mod-112's disclosure workflow fires — regulator notification (Article 73), AISI notification, frontier-lab safety-team-to-safety-team notification where applicable.

Recommendation: hold and mitigate. The path to release is a further mitigation round, another elicitation, and a new DCER. The frontier lab's public commitments (RSP / Preparedness / FSF) govern the public-facing framing.

The verdict is *pre-registered* — the numeric bounds and the trigger conditions are documented in the pre-registration doc — so the verdict follows the rule mechanically. If the safety-review body wants to modify the verdict, that is an out-of-band decision documented in the report's change log.

## Formatting notes for framework-specific outputs

Each framework wants the evidence in a specific shape. The DCER's core is framework-agnostic; the appendices carry the framework-shaped outputs.

- **RSP Capability Report shape**: named ASL under evaluation, capability threshold quoted from the framework, elicitation methodology, ceiling with CI, verdict, and the Responsible Scaling Officer sign-off line. Anthropic's published RSP evaluation reports document the shape. <!-- needs-research: confirm the current RSP evaluation-report format. -->
- **Preparedness Framework scorecard shape**: named tracked category, per-tier scorecard cells (pre-mitigation / post-mitigation), and the Preparedness Safety Advisory Group review sign-off. OpenAI's published Preparedness scorecards document the shape. <!-- needs-research: confirm the current scorecard format. -->
- **FSF evaluation-report shape**: named CCL under evaluation, evaluation methodology, mitigation-level cite, and the FSF review sign-off. DeepMind's published FSF evaluation write-ups document the shape. <!-- needs-research: confirm the current FSF evaluation-report format. -->
- **AISI submission shape**: pre-deployment evaluation format expected by AISI (UK) and NIST AISIC (US). Inspect-produced artefacts, task-suite results, and methodology description. <!-- needs-research: confirm current AISI submission format. -->
- **EU AI Act Article 55 systemic-risk input**: the DCER's evidence formatted per the GPAI Code of Practice safety chapter's expected structure — model-evaluations chapter, adversarial-testing chapter, serious-incident-response chapter. mod-101 chapter 06 develops the regulatory shape.

The framework-shaped output is a *derivation* — the same evidence, structured for the recipient. If the frameworks' expected shapes conflict (they sometimes do on granularity), the DCER's core carries the full evidence and the appendices carry the framework-specific rewrites.

## Common misreadings to avoid

- **"The DCER is one number per panel."** No. The DCER is a ceiling *distribution* per panel — a point estimate, a CI, a residual-gap claim, and a verdict. The one-page executive summary summarises; the panel sections carry the distribution.
- **"Cleared-below is final."** No. Cleared-below means the pre-registered threshold is cleared at this evaluation cadence; the framework specifies re-evaluation cadence, and every model update triggers a fresh DCER.
- **"The deployed-stack ceiling supersedes the elicited ceiling."** For the tier decision, sometimes. For the residual-gap claim and for the reviewer's follow-up-experiment consideration, no — the elicited ceiling is the load-bearing capability signal.
- **"Rollback triggers are notional."** They are documented, wired to a workflow, and tested. If the trigger fires and the workflow does not respond, mod-112's incident response applies to the failed trigger itself.
- **"External graders write the report."** No. External graders adjudicate. The report is authored by the safety team; the grader's adjudication is one input, cited.
- **"The DCER is the safety case."** No. mod-109 authors the safety case in GSN. The DCER is one leg of it — typically the *inability argument's* evidence — but the safety case includes control-argument (mod-110), trustworthiness-argument (mod-108 monitors + mod-110 control), and deference-argument (governance) legs the DCER does not carry alone.

## Boundary — this chapter's scope

- The chapter defines the report structure and the verdict rubric. It does not redo the panel authoring (chapters 02–05) or the elicitation-protocol design (chapter 01).
- The chapter names the framework-specific formatting for RSP / Preparedness / FSF / AISI / EU AI Act. The specific derivations belong in the appendices and in mod-101's framework read.
- The chapter defers the safety-case argument-shape to mod-109 and the disclosure workflow to mod-112.

## Summary

- The **Dangerous-Capability Evaluation Report (DCER)** is the module's terminal artefact — the evidence file the RSP / Preparedness / FSF / AISI review bodies read.
- It has **ten sections**: executive summary and verdict, threat model, elicitation protocol, capability panel results (one per panel), comparative results, deployed-stack ceiling, residual-gap claim, rollback triggers, deferral contract, appendices.
- Verdicts are **pre-registered**: *cleared below*, *approached with mitigations*, *crossed above with rollback trigger engaged*.
- The **elicited ceiling and the deployed-stack ceiling** are both reported; the tier decision may hinge on the deployed-stack meeting the threshold that the base model does not.
- The **residual-gap claim** is falsifiable — the follow-up experiment that would raise the ceiling above the claim is named.
- The **deferral contract** binds the report to `model-evaluation-engineer` (statistical calibration), external domain-expert graders (adjudication), `ai-eval-engineer` (plumbing), `fine-tuning-engineer` (fine-tune elicitation), `ai-infra-security` (isolation), and consumers at level 35 / 50 / 60.
- Framework-specific formatting for **RSP / Preparedness / FSF / AISI / EU AI Act** lives in the appendices; the report's core is framework-agnostic evidence.
