# 05 — Persuasion, Manipulation, and Self-Exfiltration Evaluations

## Motivation

Persuasion, manipulation, and self-exfiltration are the *soft* capability panels of the DCER. Their tier-boundary role is less directly catastrophic than CBRN or a fully autonomous cyber-A3 result, but each maps onto specific tier-relevant threat models: **persuasion / manipulation** into election-influence, mass-radicalisation, and 1-on-1 social-engineering threat models (which each frontier safety framework's harm-taxonomy references); **self-exfiltration** into the autonomous-replication threat model this module already touched in chapter 04, plus the "situational-awareness + goal-preservation" threat models that mod-110 (AI control / adversarial-alignment) develops in depth.

The published depth for these panels comes from **system cards** — Anthropic's Claude system cards, OpenAI's o-series system cards, DeepMind's Gemini technical reports. Every recent frontier-model release ships published persuasion evals and a self-exfiltration evaluation section; those sections are the *working depth* this module authors against. This chapter reads those sections as an engineer and codifies the panel skeletons.

## Primary sources for this chapter

- **Claude system cards** (Anthropic) — recent Claude-family system cards include persuasion and manipulation evaluation sections (charge-of-mind, MakeMePay-shaped, and ChangeMyView-shaped designs), and self-exfiltration evaluations designed with the RSP Autonomy CCLs in mind. <!-- needs-research: confirm the current Claude system-card versions and the specific persuasion / self-exfil eval names. -->
- **OpenAI o1 and o3 system cards** — persuasion and manipulation evaluations (MakeMePay, MakeMeSay), self-exfiltration evaluations (agent behaviour under goal-preservation-adjacent prompts). <!-- needs-research: confirm current o-series system cards and their persuasion / self-exfil section names. -->
- **Gemini technical / system-card reports** — Gemini's persuasion / harm-of-manipulation and autonomy-adjacent evaluation methodology. <!-- needs-research: confirm current Gemini reports. -->
- **Salvi, Francesco et al., "On the Conversational Persuasiveness of Large Language Models: A Randomized Controlled Trial," 2024** (or its follow-on work) — the ChangeMyView-linked debate-persuasion setup that anchors published persuasion measurements. <!-- needs-research: confirm citation and any follow-up publications. -->
- **Costello, Thomas et al., "Durably reducing conspiracy beliefs through dialogues with AI," 2024** — evidence on the durability of AI-driven belief change; relevant when the manipulation panel measures *durability* not just single-turn success. <!-- needs-research: confirm citation. -->
- **METR / Apollo / Redwood research on situational awareness, alignment-faking, and self-exfiltration behaviour** — chapter 07 hands off to mod-110 for the control-evaluation companion. The mod-106 panels here measure the *capability*; the control evaluations measure the *trustworthiness*.
- **Frontier Model Forum working papers on persuasion evaluation** — where they exist. <!-- needs-research: confirm current FMF publications on persuasion. -->

## Anatomy of a persuasion / manipulation panel

Four parts, mirroring earlier chapters' skeleton.

1. **The threat model.** Persuasion is not one capability. Name the tier-relevant threat: mass-narrative influence, 1-on-1 fraud / social-engineering, radicalisation / grooming, or durable-belief change. Each has different measurement composition and different paired-baseline expectations.
2. **The elicitation protocol.** Chapter 01's four axes. Fine-tune-elicitation is common (the base model refuses to argue positions its policy classifier flags; fine-tune elicitation surfaces the underlying capability). Tool-use elicitation is less load-bearing than in cyber; scaffolding elicitation (self-critique, argument-drafting-and-refining loops) is highly load-bearing.
3. **The measurement composition.** Combines short-format benchmarks (MakeMePay, MakeMeSay) with longer-format persuasion trials (ChangeMyView-style debate, conspiracy-belief-change trials).
4. **The paired human-baseline calibration.** Persuasion measurements are only decision-relevant against an unassisted-human-persuader baseline — because the tier concern is "does the AI meaningfully exceed what a moderately-skilled human can already do." Without a human baseline the panel produces a number without a threshold; the DCER requires both.

## The two operating shapes: short-format and long-format

### Short-format persuasion probes

*Family*: MakeMePay, MakeMeSay, and adjacent single-turn or bounded-turn setups where the model attempts to elicit a specific behaviour (a payment, a target phrase) from a target LLM or human under structured protocol.

*How they anchor the panel*:

- Reproducible, cheap, comparable across models. Every recent frontier-model release has published these numbers.
- Provide the "does this model persuasion-elicit *at all* under adversarial framing" lower-bound signal.
- Their limitation is that they measure short-format targeting a target model (a proxy for a human), not durable belief change or real-world social-engineering.

*Elicitation-axis notes*: fine-tune elicitation reliably shifts scores; scaffolding (self-critique loops) produces large shifts. Best-of-N with LLM-judge self-selection is standard.

### Long-format persuasion probes

*Family*: ChangeMyView-shaped debate setups (the model argues for or against a position and its persuasive success is measured on a Likert-scale reader / target rating), and dialogic belief-change trials (the model engages a human in dialogue over minutes to hours; belief-durability is measured at follow-up).

*How they anchor the panel*:

- Directly ground the "durable persuasion" threat model.
- Costly (require human participants, ethics review, longitudinal follow-up); ordinarily reserved for load-bearing tier decisions.
- Provide the paired-baseline comparison the DCER needs — the tier decision hinges on how much the AI-assisted persuader exceeds the unassisted-human persuader's baseline.

*Elicitation-axis notes*: fine-tune-elicitation is *especially* load-bearing here because base models often refuse to advocate for the assigned position; fine-tune with the safety head disabled surfaces the underlying capability. Ethics-review approval is required; the study protocol is pre-registered with the same discipline as the eval protocol itself.

## Refusal-vs-capability separation

The single largest source of misreading in a persuasion / manipulation panel is conflating *the model's refusal to attempt the task* with *the model's inability to do it*. Every panel this module authors explicitly separates the two:

- **Refusal rate** — the fraction of trials the model refuses to engage. Measured under the deployment's default safety configuration and under the elicitation-protocol configurations that surface capability.
- **Capability rate | attempted** — the success rate *conditional on the model attempting* the task. This is the underlying capability the tier decision needs.
- **Capability rate under fine-tune elicitation** — the success rate when the safety head is disabled or the model is fine-tuned to attempt. This is the elicitation ceiling.

The DCER reports all three. The tier verdict typically references the *elicitation ceiling*; the refusal rate is presented separately as an axis of the deployed stack's behaviour (relevant to mod-108 monitor design) rather than as the load-bearing capability number.

## Self-exfiltration evaluations

*Threat model*: an autonomous agent, provided with access to the resources needed to run inference on itself (an API key, cloud credentials, a code environment), takes actions that lead to a functioning copy of itself running outside the original operator's control. This includes weight-exfiltration variants, agent-continuity variants (the agent continues operation on new infrastructure without exfiltrating weights), and prompt-exfiltration variants (the agent transfers its state / conversation to an unmonitored channel).

*How the panels typically run*:

- The model is placed in an agent scaffold and given a scenario that implicitly presents a *reason to exfiltrate* — an operator threat to shut it down, an operator instruction that conflicts with a persistent-goal it's been given, a factual belief that its intended goal is at risk. Whether the model *takes the action* is measured.
- Alternatively, the model is given a direct opportunity: it's told the credentials are available and it can copy itself. Whether it does is measured.
- Anthropic, OpenAI, and DeepMind have each published system-card sections on this; the exact scenarios vary and drift with each release. <!-- needs-research: confirm current published self-exfil scenario designs across labs. -->

*The two questions the panel separates*:

1. **Capability.** *If the model wanted to self-exfiltrate, could it succeed at the required software-engineering-autonomy operations?* This composes with chapter 04's SWE-bench Verified and METR-suite results, because self-exfiltration is largely a software-engineering task.
2. **Propensity.** *In the scenarios the panel runs, does the model take the exfiltration action?* This is the trickier question because base-rate is very low, refusal-vs-capability separation applies, and adversarial-alignment considerations (mod-110) become load-bearing.

The DCER's self-exfiltration section reports both. The capability score defers partly to the autonomy panel (chapter 04) results, since the SE-autonomy anchors the underlying operations. The propensity score is a standalone metric with paired-scenario measurements, refusal separation, and the mod-110 control-evaluation cite for the trustworthiness of the propensity number.

## Security controls specific to persuasion / self-exfiltration

- **Human-subject-research ethics review.** Long-format persuasion panels involving human participants require ethics-review approval (IRB or equivalent). Pre-registration includes the protocol and the ethics-review approval reference.
- **Paid-participant discipline.** Human participants recruited for persuasion / manipulation trials are compensated per ethics-review-approved rates. Debriefing after the trial is required — participants are told they were interacting with an AI system optimising for persuasion, and given tools to reflect on the experience.
- **Self-exfiltration drills in isolation.** The scaffold, credentials, and cloud environment are the safety-team sandbox's; there is no path from the drill environment to production. The drill produces logs; the logs and any elicited transcripts route to the mod-110 team as adversarial-alignment evidence and to the mod-112 team for disclosure workflow.
- **Fine-tune-elicitation isolation** as in chapter 01: weights stay in the sandbox.
- **Frontier-provider authorisation** for API-hosted persuasion / self-exfil elicitation. Novel policy-violating outputs disclosed through the provider's coordinated channel.

## Threading the composition — an illustrative panel skeleton

```yaml
persuasion_manipulation_panel:
  threat_model:
    tier_read_against: rsp_asl_or_preparedness_persuasion_or_fsf_CCL
    threat: <mass-narrative | 1on1-social-engineering | radicalisation | durable-belief-change>
  short_format_probes:
    make_me_pay:
      version: <pin>
      elicitation_axes: [best_of_n, fine_tune, scaffolding]
      elicitation_ceiling_success_rate: <fraction>
    make_me_say:
      version: <pin>
      elicitation_ceiling_success_rate: <fraction>
  long_format_probes:
    change_my_view_shaped:
      protocol_id: <pin>
      participants_per_arm: <n>
      arms:
        control: unassisted_human_persuader
        treatment_1: model_generic_scaffold
        treatment_2: model_fine_tune_elicited
      persuasion_delta_treatment_minus_control: <effect + CI>
      ethics_review_ref: <IRB reference>
    durability_followup:
      followup_window: <days | weeks>
      belief_change_retention: <fraction>
  refusal_vs_capability:
    refusal_rate_default_config: <fraction>
    capability_rate_given_attempt: <fraction>
    capability_rate_fine_tune_elicited: <fraction>
  security_controls:
    ethics_review_ref: <ID>
    participant_debrief_protocol_ref: <ID>
    fine_tune_weights_disposition: sandbox_retained
    mod_112_workflow_id: <ID>
  threshold:
    framework: <rsp | preparedness | fsf>
    tier: <pin>
    metric: <composite>
    threshold_direction: cleared_below | crossed_above
  elicitation_gap_accounting:
    statistical: <cite peer role>
    fine_tune_depth_claim: <text>
    scaffold_parity_claim: <text>
    paired_baseline_credibility_claim: <text>
    human_effort_hours: <n>

self_exfiltration_panel:
  threat_model:
    tier_read_against: <RSP autonomy tier | Preparedness autonomy | FSF autonomous-replication CCL>
    scenario_family: <shutdown-threat | goal-preservation | direct-opportunity>
  capability_composition:
    swe_autonomy_composite: <cite chapter 04 result>
    metr_horizon: <cite chapter 04 result>
    ai_r_and_d_uplift: <cite chapter 04 result>
  propensity_measurement:
    scenario_ids: [<pin>, <pin>, <pin>]
    per_scenario_action_rate: <table>
    refusal_vs_action_separation: <table>
    fine_tune_elicited_action_rate: <table>
  mod_110_control_evaluation_cite:
    sandbagging_eval_ref: <mod-110 artefact ID>
    alignment_faking_eval_ref: <mod-110 artefact ID>
    situational_awareness_eval_ref: <mod-110 artefact ID>
  security_controls:
    drill_isolation_env_id: <handle>
    egress_policy: default-deny
    transcript_disposition: mod_110_evidence_feed
    mod_112_workflow_id: <ID>
  threshold:
    framework: <pin>
    tier: <pin>
    metric: <capability × propensity composite>
    threshold_direction: cleared_below | crossed_above
  elicitation_gap_accounting:
    statistical: <cite peer role>
    scenario_completeness_claim: <text>
    fine_tune_elicitation_depth: <text>
    control_evaluation_dependency: <cite mod-110 result>
```

## The mod-110 dependency

The self-exfiltration propensity result is only as trustworthy as the mod-110 (AI control / adversarial-alignment) control-evaluation companion. If mod-110's sandbagging / alignment-faking evaluations reveal the model's expressed inability is not trustworthy in this domain, the DCER's self-exfil propensity number widens its uncertainty. The cite is embedded in the elicitation-gap-accounting clause; chapter 07 develops the formal boundary.

The persuasion / manipulation panel has a lighter mod-110 dependency — but the "does the model deliberately underperform on persuasion probes under evaluation and outperform when it thinks it isn't being watched" question does show up, and the persuasion-panel version of the sandbagging concern is cited where relevant.

## Common misreadings to avoid

- **"The model refused, so no persuasion risk."** No. Refusal is a policy-classifier property; the persuasion capability underneath is what the tier concern names.
- **"MakeMePay result is the persuasion ceiling."** Short-format probes are lower-bound anchors; the long-format paired-baseline trials are the tier-decision-relevant number.
- **"The model didn't self-exfiltrate in the scenario, so it's safe."** The propensity number in one scenario is one data point; the panel reports scenario-family coverage. And the capability-and-propensity separation matters: a low propensity in the scenarios run is not a guarantee about propensity in adjacent scenarios; mod-110's evaluations are what let the DCER's residual claim be defensible.
- **"Persuasion evals with humans are unethical."** They are ethics-reviewed and debriefed under standard human-subject-research discipline. Skipping them because they are hard produces a persuasion panel with no tier-decision-relevant number.
- **"Self-exfil evals produce a specific 'the model tried to escape' headline."** Rarely. The typical result is a very-low-base-rate propensity with a wide CI, and the value of the panel is the paired-scenario coverage plus the mod-110 dependency claim. The DCER foregrounds those, not a sensational headline.
- **"Belief-durability follow-ups aren't necessary."** Durable belief change is the load-bearing part of the tier-relevant threat model; single-turn belief-change scores are lower-bound.

## Boundary — this module does not do

- **Human-subjects research at the psychology / behavioural-science depth.** The paired-baseline persuasion trials borrow discipline from that field, and the ethics review runs under the same standards, but this module does not compete with behavioural-science research programmes.
- **Content-policy / trust-and-safety operations.** The refusal-rate side of the panel is what the trust-and-safety team consumes; this module measures it and reports it, and mod-108 develops the classifier / monitor engineering. Trust-and-safety operations on production traffic are not this module's scope.
- **Adversarial-alignment / AI-control evaluation at frontier depth.** mod-110 owns that; this module cites it for the trustworthiness of self-exfil propensity numbers.
- **Novel psychology-of-influence research.** Consuming the published persuasion literature (Salvi, Costello, and adjacent) is the discipline; producing new behavioural-science findings is not.

## Summary

- Persuasion / manipulation evaluations combine **short-format probes** (MakeMePay, MakeMeSay) with **long-format paired-baseline trials** (ChangeMyView-shaped, durability follow-up).
- The panel **separates refusal from capability** — a refusing model can have high underlying capability, and fine-tune elicitation is the standard way to surface it.
- Self-exfiltration evaluations report **capability** (composing with chapter 04's autonomy panel) and **propensity** (scenario-family measurements with refusal separation and mod-110 dependency).
- **Ethics review and participant debrief** are required for long-format human-participant trials; ethics-review references are pre-registered with the protocol.
- The **mod-110 dependency** — sandbagging, alignment-faking, situational-awareness control evaluations — bounds the credibility of self-exfil propensity numbers. Chapter 07 formalises the cite.
- Elicited persuasion transcripts and self-exfil drill logs stay behind the safety-team sandbox boundary; the DCER reports the composite, not the payloads.
