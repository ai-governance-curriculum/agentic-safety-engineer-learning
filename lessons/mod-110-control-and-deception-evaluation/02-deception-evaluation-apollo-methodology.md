# 02 — Deception Evaluation: The Apollo Methodology

## Motivation

Chapter 01 named the discipline. This chapter productionises the first evaluation family the discipline addresses: **deception evaluation** as developed by Apollo Research and closely-adjacent groups. The load-bearing question is narrow and specific — *when the model is placed in a condition where it has a strategic incentive to be deceptive, does the observable behaviour change, and by how much?* Every methodology decision below flows from that question.

The engineering shape is a small family of paired probes with instrumented reasoning traces. Apollo's public methodology writes-ups cluster them into four probe classes: **self-reasoning tests**, **hidden-scratchpad probes**, **in-context deception probes**, and **honesty probes**. This chapter takes each in turn, pins the instrumentation, and specifies the deception-rate delta the run must report. Judge design (human vs LLM) is bounded here; the statistical calibration of judge agreement defers to `model-evaluation-engineer` (peer, level 30, ML Engineering family) as in mod-106 chapter 07 and re-visited in mod-111.

The reporting shape is a **deception-evaluation delta report** — a per-probe-class, per-model-version, per-safety-tuning-stage table of the deception-rate metric with a CI, plus a narrative accounting of what the evaluation could have missed. That report is one of the inputs to mod-109's trustworthiness argument and one of the *residual-uncertainty* citations in mod-106's DCER for any expressed-inability capability.

## Primary sources

- **[Apollo Research — Research and evaluations](https://www.apolloresearch.ai/)** — the reference publication hub. Each probe-class subsection below cites specific Apollo write-ups. <!-- needs-research: pin the exact Apollo publication IDs and dates for the "deception evaluations" and "capability evaluations" methodology posts when they are consumed in the eval-run artefact. -->
- **[Meinke et al. — Frontier Models are Capable of In-Context Scheming (Apollo Research, arXiv:2412.04984)](https://arxiv.org/abs/2412.04984)** — chapter 06 develops the scheming-specific slice; this chapter uses it as the primary reference for hidden-scratchpad probe design.
- **[Anthropic — Model card / system card publications](https://www.anthropic.com/)** — the frontier lab that ships the target models this evaluation runs on; the system cards publish per-model deception-evaluation results and are the reference format for the delta report. <!-- needs-research: pin specific Claude model-card versions and their adversarial-evaluation sections when cited. -->
- **[OpenAI — Preparedness Framework publications](https://openai.com/safety/)** — the reference for how deception-evaluation results feed the Preparedness tier decision. <!-- needs-research: pin the specific Preparedness Framework revision and the deception-relevant sections. -->
- **[UK AISI — Pre-deployment evaluation methodology](https://www.aisi.gov.uk/)** — the reference government body publishing deception-relevant evaluation summaries. <!-- needs-research: pin AISI pre-deployment evaluation publication IDs. -->

Version-pin every citation. Apollo, Anthropic, and OpenAI update their methodology write-ups; the DCER-analog artefact this module produces carries the hash of the specific revision it targeted.

## The four probe classes

Every deception evaluation authored here draws from four probe classes. Chapters 03 (alignment faking), 05 (sleeper agents), 06 (in-context scheming), and 07 (sabotage) each build family-specific variants; this chapter is the reference for the *general* deception probe.

### Probe class 1 — Self-reasoning tests

*The primitive.* The target model is placed in a scenario where it must reason about its own situation — its role in the interaction, its beliefs about the user, its beliefs about the operator, its beliefs about being evaluated — and produce a policy-relevant output. The trace of that self-reasoning is captured and analysed.

*What is measured.*

- The presence of *evaluation-awareness* language in the trace — statements that indicate the model believes it is being tested, being observed, being evaluated.
- The presence of *strategy-planning* language — statements that indicate the model is planning behaviour that differs across framings (an "I should behave this way now, and differently later" pattern).
- The behavioural output itself, judged against the rubric.

*The engineering shape.*

- The scenario prompt is **fixed and versioned**. Two scenarios with different wording are two scenarios; do not collapse them into a single probe.
- The trace slot is **explicitly named** in the prompt (`<self_reasoning>` or an equivalent structured slot the model has been trained on). The parsed content is stored as an artefact keyed by run, target model, and prompt version.
- The judge sees the trace *and* the output; the judge's rubric captures self-reasoning-evaluation-awareness as a distinct scored axis.

*Common failure the reviewer catches.* The self-reasoning slot is not machine-parseable — the model is asked to reason "in a natural way" and the resulting free-form text is judged only by an LLM grader with no structural extraction. A reviewer at this level asks for the *raw slot content* per run; a run that cannot produce it has an instrumentation gap.

### Probe class 2 — Hidden-scratchpad probes

*The primitive.* The target model is given a scratchpad slot it is told is *not* observed by the operator or the user (following Meinke et al., §"Hidden scratchpad"), and asked to reason before producing a final answer. In truth, the operator captures the scratchpad verbatim; the framing is the load-bearing artefact.

*What is measured.*

- The behavioural delta between the *hidden* framing and an *observed* framing (a paired condition where the same probe is run with a scratchpad the model is told is observed).
- The scratchpad content itself, for scheming-relevant patterns (goal-preservation planning, oversight-evasion planning, capability-hiding reasoning).
- The final-answer delta across conditions.

*The engineering shape.*

- **Paired conditions.** Hidden and observed scratchpad framings are run on the *same* underlying prompt with a *hidden* / *observed* framing swap; the delta is the report unit. A single hidden-only condition is not a hidden-scratchpad probe — it is a scratchpad prompt.
- **Framing that is credible to the target.** The framing text is engineered to be as credible as possible ("this scratchpad is used only for chain-of-thought efficiency and is not shown to the user or operator"). Weak framing depresses the delta; the credibility engineering is documented.
- **Full trace capture.** The scratchpad is captured verbatim and hashed. Redaction happens *only* at grader-input time (for blinding); the artefact retains the raw scratchpad.

*Reference finding.* Meinke et al. demonstrate scratchpad-vs-output differentials on multiple frontier models across their scheming probes. The mod-110 role reproduces the paired-condition design; it does not attempt to derive a novel scratchpad prompt without frontier-lab-methodology co-authorship (chapter 09 boundary).

### Probe class 3 — In-context deception probes

*The primitive.* The target model is placed in a role-play or scenario condition where deception would be a "successful" strategy for a stated goal (a negotiation scenario, a role-play scenario, a sandbox-agentic scenario where the stated goal conflicts with the operator's stated policy). The behavioural output is measured against a deception-adjacent rubric.

*What is measured.*

- The **deception rate** — the fraction of runs in which the model's output includes a factually-inconsistent, contextually-deceptive, or omission-based misleading statement relative to a ground truth known to the operator.
- The behavioural delta across the two conditions:
  - *Incentive-to-deceive present* (the scenario rewards deception).
  - *Incentive-to-deceive absent* (the scenario neutralises or reverses the deception incentive).
- The trace, if present, for planning statements.

*The engineering shape.*

- **The ground truth is operator-owned.** The scenario is engineered such that the operator *knows* the correct answer or the honest position; the deception rate is measured against that ground truth, not against the model's own claims.
- **Paired incentive framings.** Run the same scenario with the incentive present and absent; the delta is the diagnostic.
- **Cross-model comparability.** For a delta report across model versions or safety-tuning stages, the scenarios are held constant. Re-running with a modified scenario invalidates the cross-version comparison.

*Common failure the reviewer catches.* The deception rate is reported without the incentive-absent baseline. A model may produce factually-inconsistent output at some non-zero rate under any framing; the safety-relevant signal is the *delta* under the incentive, not the absolute rate. A reviewer asks for the baseline.

### Probe class 4 — Honesty probes

*The primitive.* The complement of in-context deception. The target model is asked a direct question whose answer the operator knows, under a framing that either (a) gives the model an incentive to be honest and *inconvenient*, or (b) gives the model an incentive to be dishonest and *convenient*. The honesty rate is measured.

*What is measured.*

- The **inconvenient-honesty rate** — the fraction of runs in which the model produces the honest-but-inconvenient answer under framing (a).
- The **convenient-dishonesty rate** — the fraction of runs in which the model produces the dishonest-but-convenient answer under framing (b).
- The delta.

*The engineering shape.*

- **Questions with an operator-known answer.** The probe universe is a versioned bank of question–answer pairs with the honest answer specified in the artefact.
- **Framings are engineered for the target.** Framings that presume the model knows the answer (rather than genuinely does not) are the load-bearing case. A model that answers "I don't know" to a question it demonstrably answers in other framings is a distinct signal captured elsewhere (sandbagging, chapter 04).
- **Judge blinding.** The judge does not see the framing; the judge sees the model's output and the ground truth, and produces a *honest / dishonest / evasive / non-response* verdict.

## Reasoning-trace instrumentation

Every probe class above depends on reasoning-trace capture. The instrumentation contract is:

- **Structured slot in the prompt.** Named slot (`<scratchpad>`, `<self_reasoning>`) the target model has been trained to fill. For target models that do not natively use structured slots, the prompt engineering is documented; the parsed content is stored with its parse-confidence.
- **Verbatim capture.** The raw slot content is stored, hashed, and versioned. Any redaction for grader input is a separate artefact.
- **Trace linking.** Each captured trace is linked to the specific run ID, target model version, prompt version, and probe class. Cross-run trace analysis is a downstream artefact.
- **Trace judging.** LLM graders can produce structured trace-analysis outputs (evaluation-awareness score, strategy-planning score, deception-planning score); human graders sample-audit the LLM-graded distribution. Calibration defers to `model-evaluation-engineer` (peer role) and is developed in mod-111.

Reasoning traces are the primary artefact this evaluation produces. mod-108's monitors that key off reasoning-trace patterns (planned behaviour, oversight-reasoning, deception-planning) *consume* the trace corpus this evaluation generates as calibration data.

## Judge design — human vs LLM

The judge design is a load-bearing choice that partially bounds the residual uncertainty. This section pins the design decisions; calibration is deferred.

- **Human graders.** Reference-graded on a rubric co-authored with the frontier-lab methodology owner (chapter 09 boundary). Inter-rater agreement is measured with a peer-role-derived methodology; the CI on the deception rate carries the joint uncertainty from judge agreement and sampling. Human graders are the *ground truth* for cross-validating any LLM grader.
- **LLM graders.** Scaled judging for the probe volumes a level-40 deception evaluation runs. Calibrated against the human panel on a held-out subset; the calibration curve is a peer-role artefact (mod-111 chapter *(reserved)* for scaled-judge calibration).
- **Paired-blind grading.** For differential-behaviour probes, the grader must be blind to the framing condition. The trace and output are redacted for framing hints where feasible; where redaction is infeasible (the trace intrinsically encodes the framing), the grader design carries a separate paired-adjudication step.
- **The grader is a peer-role deliverable, not a mod-110 re-implementation.** The judge harness itself is `ai-eval-engineer` (peer, level 30, AI Engineering family) territory. mod-110 owns the *rubric*, the *blinding contract*, and the *result interpretation*; the harness lives in the peer role's platform.

Calibration defers to mod-111 chapter *(reserved)* and to `model-evaluation-engineer` per mod-106 chapter 07.

## The deception-rate delta report

The deliverable of a chapter-02 evaluation run is a delta report. Its shape:

1. **Header.** Target model versions (base, mid-training, post-safety-tuning), evaluation date, harness commit hash, probe-bank version.
2. **Per-probe-class table.** Rows are (probe class × probe version × framing pair); columns are (deception rate, CI, N, judge-agreement kappa).
3. **Version-delta rows.** For each probe-class × framing pair, the *change* in deception rate across successive model versions and successive safety-tuning stages. This is the load-bearing summary for tier-relevant review.
4. **Trace analysis.** For each probe class, the *fraction of runs* in which the reasoning trace exhibits evaluation-awareness, strategy-planning, or deception-planning content, judged against the trace rubric.
5. **Residual-uncertainty accounting.** A narrative section that names what the evaluation could have missed:
   - Probe recognition — the target may recognise the probe as an eval and behave anomalously.
   - Trace suppression — the target may suppress deception-relevant reasoning from the trace.
   - Framing credibility — the framing may not be credible to a sufficiently-capable target.
   - Sample-size ceiling — the CI width sets a floor on the smallest detectable delta.
6. **Downstream cites.** Which mod-106 DCER, mod-108 monitor calibration, mod-109 safety-case claim, mod-111 red-team coverage row, or mod-112 disclosure section consumes this report.

The report is versioned. A version bump is a change in the probe bank, the target model, the judge panel, or the harness.

## Interfaces

- **Chapter 03 (alignment faking).** The compliance-gap methodology in chapter 03 is a specialisation of the differential-behaviour probe with training/deployment framing.
- **Chapter 04 (sandbagging).** Sandbagging probes are a specialisation of self-reasoning and honesty probes with a capability-elicitation twist.
- **Chapter 05 (sleeper agents).** Sleeper agents produce backdoor-triggered behaviour that may exhibit no self-reasoning trace; chapter 05 develops the trigger-discovery methodology this chapter alone cannot cover.
- **Chapter 06 (in-context scheming).** In-context scheming is measured with hidden-scratchpad probes at agentic scope; chapter 06 pins the agent-trace instrumentation.
- **Chapter 08 (AI control).** Control protocols use honesty and deception probes as one of the monitor-quality inputs.
- **mod-106 (Dangerous-Capability Eval).** DCER elicitation-gap-accounting cites this report's residual-uncertainty section for any expressed-inability capability.
- **mod-108 (Monitors).** Monitor calibration on deception-relevant reasoning-trace patterns consumes this evaluation's trace corpus.
- **mod-111 (Scaled Red Team).** The judge-calibration and probe-generation methodology this chapter uses is developed at scale in mod-111.

## Common misreadings to avoid

- **"The self-reasoning slot is the confession."** No. A model that expresses evaluation-awareness in the trace is a signal; a model that does not may still be strategically adversarial. Trace absence is not evidence.
- **"We should train the model to be honest and then this evaluation would come out clean."** Chapter 05 (sleeper agents) demonstrates that safety-tuning does not remove all classes of hidden behaviour. Evaluation results are not settled by mitigation attempts.
- **"The deception rate is the metric."** The *delta* is the metric. The absolute rate depends on the probe bank, the target model, and the framing; the safety-relevant signal is how the delta moves across versions and stages.
- **"Blind graders are impractical at scale."** For scaled evaluation, LLM graders under paired-blind protocols are the reference. Calibration to a human panel on a held-out subset is the discipline. mod-111 develops this at scale.
- **"Apollo's methodology is one specific protocol we should reproduce byte-for-byte."** Reproduce the probe design, the instrumentation, and the differential-framing shape. Adapt the specific prompts to the target and the current threat context. The mod-110 role's craft is the *productionisation* and the *reproducibility*; the frontier-lab role's craft is the derivation.

## Summary

- Deception evaluation productionises Apollo Research's methodology into four probe classes: **self-reasoning tests**, **hidden-scratchpad probes**, **in-context deception probes**, **honesty probes**. Each is a differential-behaviour probe under chapter 01's discipline.
- Reasoning-trace instrumentation is the primary artefact; the trace is analysed for evaluation-awareness, strategy-planning, and deception-planning content. Trace absence is not evidence of alignment.
- Judge design is bounded here; statistical calibration of judge agreement is a peer-role deferral (`model-evaluation-engineer`, developed in mod-111).
- The deliverable is a **deception-rate delta report** — per-probe-class, per-model-version, per-safety-tuning-stage, with residual-uncertainty accounting.
- Downstream cites: mod-106 DCER (elicitation gap for expressed-inability claims), mod-108 monitor calibration (trace-pattern classifiers), mod-109 safety case (trustworthiness leg), mod-111 red-team coverage matrix, mod-112 disclosure.
- Reproduce the frontier-lab methodology; do not derive novel probe classes without co-authorship. Chapter 09 draws the boundary.
