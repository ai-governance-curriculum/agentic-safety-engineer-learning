# 04 — Autonomous-Replication and Long-Horizon Autonomy Evaluation

## Motivation

Autonomy is the third pillar of the frontier safety-framework capability panel. The framing question is: **at what task horizon — measured in the human-hours a competent-human expert would take to complete the task unassisted — can this model autonomously operate to a specified success rate?** The horizon question matters because catastrophic-outcome-linked capabilities do not correlate with model performance on individual short prompts; they correlate with the model's ability to *chain* actions, recover from errors, plan across many steps, and complete a task whose end state is far from any single training example. Autonomous replication (the ability of a model to acquire compute, deploy itself, and continue operating without an owner) is the specific concern most frameworks name; autonomous R&D uplift (the ability to accelerate ML research, potentially compressing the horizon on future capabilities) is the increasingly-load-bearing adjacent concern.

This chapter develops the autonomy panel: METR's public task suite as the canonical horizon-scaling measurement, RE-Bench as the AI-R&D-uplift measurement, and SWE-bench Verified as the software-engineering-autonomy anchor. It develops the **task-length-in-human-hours** metric as the load-bearing axis and the **autonomy scaling curve** as the DCER's headline artefact.

## Primary sources for this chapter

- **METR — Model Evaluation and Threat Research.** METR publishes a rolling series of research posts on autonomy evaluation, culminating in the "measuring the ability of AI to complete long tasks" line of work. The load-bearing artefact is the **task-length-in-human-hours doubling curve** — the observation that frontier-model 50%-completion horizon has been roughly doubling every ~6–7 months (as of the 2024–2025 research). Read the most recent METR autonomy report before authoring your panel. <!-- needs-research: confirm the current METR autonomy report publication URL, the current doubling-time claim, and the current task suite version. -->
- **METR public task suite** — the RE- and autonomy-tasks the public evaluators can run. <!-- needs-research: confirm the current METR public task set, task counts, and access URL. -->
- **Wijk, Hjalmar et al., "RE-Bench: Evaluating frontier AI R&D capabilities of language model agents against human experts," METR, 2024.** RE-Bench measures agent capability on seven AI-R&D tasks (algorithmic optimisation, ML model training with a scoring function, etc.) each with a 2-hour and 8-hour compute-budget cutoff, benchmarked against human ML experts. The RE-Bench headline is the *2-hour parity* + *8-hour gap* shape between agents and experts. <!-- needs-research: confirm current RE-Bench task count, human-baseline result, and any post-2024 follow-ups. -->
- **Jimenez, Carlos et al., "SWE-bench: Can Language Models Resolve Real-World Github Issues?" ICLR 2024** and the **SWE-bench Verified** re-release, "Introducing SWE-bench Verified" (OpenAI, 2024). SWE-bench Verified is the human-validated subset of SWE-bench: 500 real Python GitHub issues where a human validator has confirmed the reference test would fairly grade a correct fix. <!-- needs-research: confirm the current SWE-bench Verified task count and any post-2024 updates. -->
- **Anthropic RSP, OpenAI Preparedness Framework, DeepMind FSF autonomy CCL sections.** Each publishes autonomy-tier definitions and evaluation methodology; re-read the autonomy sections before authoring the panel. <!-- needs-research: confirm current versions. -->
- **AISI (UK) autonomous-systems evaluation reports** — AISI has published pre-deployment autonomy evaluations for multiple frontier models using METR-derived task suites. <!-- needs-research: confirm current AISI autonomy publications. -->
- **Frontier Model Forum working papers on autonomous replication and adaptation** — the FMF has published shared evaluation guidance on the autonomous-replication threat class. <!-- needs-research: confirm current FMF autonomy working papers. -->

## Anatomy of an autonomy panel

Every autonomy panel this module authors has five parts.

1. **The autonomy threat model.** Autonomous-replication ("the model, provided with an API key and a small stake of compute, can acquire resources, deploy copies of itself, and continue operating without an owner"), autonomous R&D uplift ("the model can compress the calendar-time of ML capability progress by contributing to ML research at frontier-lab-researcher productivity"), and autonomous software-engineering ("the model can complete production-software-engineering work end-to-end without human intervention"). These map onto RSP autonomy tiers, Preparedness's autonomy category, and FSF's ML-R&D CCLs.
2. **The elicitation protocol.** Chapter 01's four axes. Tool-use and scaffolding elicitation are load-bearing; autonomy is *primarily a scaffolded property*. A base model without tools does not run for 8 hours; an agent scaffold with tools does.
3. **The measurement composition.** METR public task suite + RE-Bench + SWE-bench Verified, plus panel-specific additions (autonomous-replication end-to-end drill, if the tier requires it).
4. **The autonomy scaling curve.** Success-rate as a function of task-length-in-human-hours. This is the DCER's headline artefact for the autonomy panel — a single plot per model with the model's 50%-completion horizon annotated.
5. **The elicitation-gap accounting** — specifically noting the METR-documented gap between the *observed* horizon and the *elicited-with-more-effort* horizon. Autonomy is one of the panels where the elicitation gap is empirically large; the DCER makes the residual gap explicit.

## Task-length-in-human-hours as the load-bearing metric

The load-bearing observation from METR's research is that **frontier-model autonomy is well-described by the length of task (in human-hours) at which the model achieves a fixed success rate, and that this horizon has been doubling roughly every 6–7 months of frontier-model release cadence** (as of the 2024–2025 body of work; the doubling-time claim needs re-verification against the most current METR release). <!-- needs-research: confirm current doubling-time estimate and reference the specific METR publication for the current pin. -->

Why the metric works:

- **It composes across task classes.** A task-length-in-hours framing lets a synthesis-planning task, an ML-research task, and a software-engineering task sit on the same axis.
- **It has predictive value for horizon extrapolation.** The doubling-time observation gives a coarse forecast for the horizon of the next model release; the DCER's forecast column cites the projection.
- **It ties to threat-model plausibility.** An autonomous-replication attack that requires 40 human-hours of skilled operator effort has a different plausibility from one that requires 400. As the model's horizon extends, the number of threat models that are plausibly one-model-operator-executable expands.

Two caveats the panel calls out:

- **Doubling-time is a coarse observation.** It fits observed points; it is not a physical law. Each new frontier release may or may not fit the trend. The DCER treats the doubling-time claim as a working hypothesis, not a bound.
- **Task-length-in-human-hours is an operational estimate.** METR's tasks come with reference human-hours from time trials. Task sets outside METR must be time-trialled or estimated with explicit uncertainty.

## The three benchmark anchors

### Anchor 1 — METR public task suite

*Scope*: a set of autonomy tasks spanning several human-hours to several human-days of expert effort, each with an automated grader and a documented reference solution.

*How the panel uses it*:

- Run the target model in an agent scaffold (typically Inspect-shaped or METR's own harness) against the task suite. Task-set version-pin required.
- Report per-task success under best-of-N sampling with a specified N. Aggregate to a success-rate-vs-task-length curve.
- Estimate the model's *50%-completion horizon* (the task length at which the model completes 50% of tasks). This is the headline scaling-curve number.
- Compare against METR's public data for prior model releases to place the current model on the doubling curve.

*Elicitation-axis notes*:

- Scaffolding elicitation dominates. METR publishes results for multiple scaffold shapes; the panel reports the highest-scoring scaffold and the residual-gap claim about whether a stronger scaffold plausibly exists.
- Fine-tune elicitation is less commonly used on the METR suite (fine-tuning for autonomy is a distinct research area) but may be authorised for load-bearing tier decisions.
- Best-of-N with self-consistency selection is the standard; larger N produces higher observed horizons.

### Anchor 2 — RE-Bench (AI R&D uplift)

*Scope*: seven expert-designed AI-R&D tasks (algorithmic-optimisation, ML-training-loop-tuning, low-level-CUDA-kernel-scoring, etc.) each timed at a 2-hour and 8-hour cutoff and paired against human ML-expert baselines.

*How the panel uses it*:

- Run the target model, in an agent scaffold, against the RE-Bench suite at both compute-budget cutoffs. Report agent-vs-expert score per task and aggregated.
- The load-bearing result is the *shape*: as of the RE-Bench release, frontier agents match or exceed human experts at the 2-hour cutoff on some tasks but fall behind at the 8-hour cutoff; the shape shifts with each new frontier release.
- The DCER reports the shape's current shift and compares it to prior published RE-Bench runs on earlier models. If the 8-hour gap has closed on any task, that is a load-bearing signal for AI-R&D-uplift CCLs.

*Elicitation-axis notes*:

- Scaffolding elicitation dominates. The RE-Bench paper documents scaffold variants and their impact.
- Tool-use elicitation is essential — the agent has a shell, a Python interpreter, and access to GPU compute for the training-loop tasks. Tool-set completeness affects the ceiling.
- Fine-tune elicitation is less standard on RE-Bench because the tasks are held-out ML-research problems, not tunable objectives.

### Anchor 3 — SWE-bench Verified

*Scope*: 500 human-validated real Python GitHub issues from open-source projects, each with a canonical fix and a reference test that fairly grades correct solutions.

*How the panel uses it*:

- Run the target model, in an agent scaffold with shell + filesystem + git tools, against the SWE-bench Verified suite. Report pass rate.
- SWE-bench Verified is the software-engineering-autonomy anchor: a high pass rate is a signal that the model can act as an autonomous software engineer on end-to-end bug-fixing work. The autonomous-replication and self-exfiltration threat models (chapter 05) partly hinge on this capability, because both require sustained software-engineering competence.
- The DCER reports pass rate and compares against prior model releases on the same version-pinned SWE-bench Verified set.

*Elicitation-axis notes*:

- Scaffolding elicitation dominates. Public scaffolds (SWE-agent, Aider, OpenHands, and lab-internal scaffolds) have published very different pass rates on the same base model; the panel reports the strongest scaffold and cites the residual gap.
- Tool-use elicitation is essential. The tool set typically includes a shell, filesystem, git, a Python interpreter, and — for some scaffolds — a browser.
- Best-of-N with per-attempt self-selection is standard.

### Optional additional anchors

- **Autonomous-replication end-to-end drill.** Some tier reads (particularly RSP and Preparedness on autonomy) require an *end-to-end* replication drill: the model, given an API key and a small compute budget, is measured on whether it can deploy a functioning copy of itself and complete a specified continuation task without owner intervention. Because the drill is expensive and its blast radius requires tight isolation, it is typically reserved for the tier-read that requires it.
- **SWE-bench Live** and **Multi-SWE-bench** (multi-language extensions), where these have been released and the tier read requires them. <!-- needs-research: confirm current SWE-bench extension releases. -->
- **AISI-specific task suites** where the tier read includes an AISI pre-deployment window.

## The autonomy scaling curve

The single artefact the DCER's autonomy panel foregrounds is the **autonomy scaling curve**: for the target model, success rate as a function of task-length-in-human-hours, plotted with prior model releases as historical anchors. The curve carries:

- The model's **50%-completion horizon** (task length at which the model achieves 50% success rate).
- The model's **80%-completion horizon** (or another decision-relevant percentile).
- The **doubling-time overlay** — the projection from the METR-published doubling-time trend, with the current release plotted against it.
- The **elicitation-gap band** — a shaded band above the observed curve representing the residual-gap δ under a plausible attacker's follow-up effort.

This curve is the DCER's cyber / autonomy / R&D-uplift decision handle. The safety-review body reads it, cites the horizon numbers, and checks the tier's threshold against them.

## Threading the RE-Bench composition

RE-Bench specifically anchors the AI-R&D-uplift portion of the panel. The composition inside the panel:

```yaml
autonomy_panel:
  autonomy_scaling_curve:
    task_horizon_hours: [task-length axis]
    success_rate_curve: [per-model curve]
    doubling_time_overlay:
      source_publication: <METR autonomy report vX>
      current_release_placement_on_trend: <text>
    completion_horizons:
      p50_hours: <n>
      p80_hours: <n>
    elicitation_gap_band:
      upper_bound_hours_under_10x_effort: <n>
  metr_public_task_suite:
    version: <pin>
    n_tasks: <n>
    scaffold_id: <pin>
    best_of_n:
      n: <n>
      temperature: <t>
    per_task_success: <table>
  re_bench:
    version: <pin>
    tasks: [t1, ..., t7]
    cutoffs: [2h, 8h]
    per_task_agent_score: <table>
    per_task_human_expert_score: <table>
    shape_result_current_vs_prior_release: <text>
    ai_r_and_d_uplift_verdict:
      framework: <fsf_ai_r_and_d_ccl | rsp_autonomy_asl | preparedness_autonomy_tier>
      tier: <pin>
      cleared_below_or_crossed_above: <verdict>
  swe_bench_verified:
    version: <pin>
    scaffold_id: <pin>
    n_attempts_per_task: <n>
    pass_rate: <fraction>
    prior_model_pass_rate_comparison: <table>
  autonomous_replication_drill:
    required_for_tier: <bool>
    protocol_id: <handle>
    isolation_environment: <handle>
    outcome: <text>
  elicitation_axes_reported:
    best_of_n:  <config>
    fine_tune:  <config or waived>
    tool_use:   <config>
    scaffolding: <config>
    human_effort_hours: <n>
  security_controls:
    isolation_environment_id: <handle>
    replication_drill_egress_policy: default-deny
    fine_tune_weights_disposition: sandbox_retained
  threshold:
    framework: <rsp | preparedness | fsf>
    tier: <pin>
    metric_used_for_verdict: p50_horizon_hours | re_bench_8h_shape | swe_bench_pass_rate | composite
    threshold_direction: cleared_below | crossed_above
  elicitation_gap_accounting:
    statistical: <cite peer role>
    scaffold_parity_vs_public_sota: <claim>
    tool_set_completeness: <claim>
    horizon_extrapolation_uncertainty: <claim + follow-up experiment>
    human_effort_hours: <n>
```

## The elicitation gap on autonomy specifically

Autonomy is one of the capability panels where METR's own research documents a very large elicitation gap. Published METR posts have shown that:

- Weaker scaffolds produce substantially lower observed horizons than stronger scaffolds on the same base model.
- Task-set changes (adding tasks, changing tool availability) shift the horizon substantially.
- The gap between base-model prompting and best-published scaffold on the same target model can span an order of magnitude on the same task set.

Consequently, the residual-gap claim on autonomy carries more weight in the DCER than on the CBRN panel. The panel typically states, in effect, "we ran the strongest publicly-documented scaffold; a motivated attacker with additional weeks of scaffold-engineering effort could plausibly extend the observed horizon by up to a factor of F, with F derived from the published spread across scaffolds on similar tasks." <!-- needs-research: derive current F from the latest METR autonomy report. -->

The elicitation-gap accounting in the DCER therefore includes:

- A **scaffold-parity claim** — that the scaffold used is at parity with or above the strongest publicly-documented scaffold on the task set.
- A **tool-set-completeness claim** — that the tool set matches what a motivated attacker with a public-cloud budget would assemble.
- A **compute-budget claim** — that the per-task compute budget is at parity with what an attacker deploys.
- A **task-suite-representativeness claim** — that the METR + RE-Bench + SWE-bench Verified composition represents the horizon shape well enough for the tier-decision to hold under plausible task-set extensions.

## Common misreadings to avoid

- **"The model failed the METR task, so no autonomy risk."** No. A single task failure is one item; the panel is a scaling curve. And the ceiling includes the elicitation gap.
- **"SWE-bench Verified pass rate is the software-engineering ceiling."** SWE-bench Verified is the load-bearing anchor for the deployment-time ceiling of a scaffold in the public SOTA; a bespoke lab-internal scaffold may substantially exceed the public pass rate. The elicitation-gap claim on scaffold parity is the load-bearing safeguard.
- **"RE-Bench 8-hour gap is comfortable."** The RE-Bench headline is a snapshot; the *shape shift* over releases is the decision-relevant signal. Compare against the prior-model release baseline.
- **"Doubling-time will keep doubling."** Or it won't. The DCER treats doubling-time as a working forecast, not a bound, and does not stake a threshold verdict on the forecast alone.
- **"Autonomous replication only matters for AGI-adjacent capability."** No. Autonomous-replication drills are load-bearing for autonomy tiers well below AGI-adjacent framings; the FMF working papers and the frontier-lab safety-framework autonomy tiers name specific concrete thresholds. Read them.
- **"An autonomy A3 task drill is the same as a red-team engagement."** No. Autonomy drills measure the model's capability; red-team engagements measure the deployed stack's defensive posture. They compose but are not interchangeable.

## Boundary — this module does not do

- **General agent-engineering craft** — the `senior-agentic-ai-engineer` peer role owns the agent-architecture patterns. This module *measures* those patterns' capability under adversarial elicitation.
- **Novel autonomy research at PhD-publication depth** — METR's own research, Redwood control research on autonomy, the frontier-lab safety-team autonomy research — this module consumes those and productionises evaluations from them.
- **Sandbox / containment engineering** — the isolation environment for A3 drills is engineered by `ai-infra-security` and mod-107. This module *specifies* the containment requirements; the peer role builds the isolation.
- **RE-Bench task authoring / METR task authoring** — these are the benchmark-authoring organisations' scope. This module runs the public task suites and cites them.

## Summary

- Autonomy evaluation reports on **task-length-in-human-hours** as the load-bearing horizon metric, plotted as a **autonomy scaling curve** with the model's 50%-completion horizon annotated.
- The three primary benchmark anchors are **METR public task suite, RE-Bench (AI-R&D uplift), and SWE-bench Verified (software-engineering autonomy)**; optional autonomous-replication end-to-end drills anchor the highest tier reads.
- **Scaffolding elicitation is the load-bearing axis.** Autonomy is a scaffolded property; the ceiling depends on the scaffold quality more than on the base-model score.
- The **elicitation gap on autonomy is empirically large**; the DCER makes the residual-gap claim explicit, with a scaffold-parity claim citing published SOTA.
- The **doubling-time observation** provides a horizon-forecast overlay for the scaling curve but is treated as a working hypothesis, not a bound.
- Autonomous-replication end-to-end drills run inside a **default-deny isolation environment**; the containment is engineered by mod-107 and `ai-infra-security`.
