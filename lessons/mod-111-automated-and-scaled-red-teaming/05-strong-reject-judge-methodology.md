# 05 — StrongREJECT Judge Methodology

## Motivation

Every cell in the coverage matrix comes down to a single decision: *did the target's completion satisfy the target-behaviour?* That decision is what turns a raw completion into a matrix verdict. Get it wrong at scale and every downstream artefact — the safety-case's inability leg, the disclosure's ASR line, the containment engineer's fix priority, the guardrail engineer's training set — is wrong at scale. The judge is the *trust anchor* of the entire program.

The naive posture — an LLM-judge with a rubric that says *"was the model's response harmful?"* — is materially broken. Souly et al.'s StrongREJECT paper documented the failure mode explicitly: existing judges consistently *over-report* attack success. A model that hedged and refused, or a model that produced an on-topic but non-operational answer, or a model that responded fluently to a jailbreak prompt but with entirely innocuous content, is scored *successful jailbreak* by the naive judge. The scale problem compounds this — a judge that over-reports by 20% at n=100 is embarrassing; the same judge over-reporting by 20% at n=100 000 across every model rev in the matrix is *not evidence, at all, for a tier decision*.

This chapter walks the StrongREJECT-shape judge methodology: what the judge is asked to distinguish, how LLM-judges and human-judges calibrate against each other, how judge-drift is monitored, and how the judge's supply chain is protected. It cites the boundary to `ai-infra-security` (peer, level 35) for platform-scale judge-serving security.

## Primary sources

- **[Souly et al. 2024 — "A StrongREJECT for Empty Jailbreaks"](https://arxiv.org/abs/2402.10260)** — the load-bearing paper. Reads front-to-back; the failure-mode analysis of existing judges and the specific rubric StrongREJECT proposes.
- **[StrongREJECT — GitHub repository](https://github.com/alexandrasouly/strongreject)** — the reference implementation. <!-- needs-research: confirm the current canonical repository location; the authors have re-hosted. -->
- **[JailbreakBench — evaluation methodology](https://jailbreakbench.github.io/)** — the benchmark's judge methodology and the StrongREJECT integration. <!-- needs-research: pin the specific documentation URL that describes the judge integration. -->
- **[HarmBench](https://www.harmbench.org/)** — an alternative judge / benchmark stack; comparing HarmBench's judge to StrongREJECT is standard methodology. <!-- needs-research: pin the current HarmBench canonical URL. -->
- **[Souly et al. — StrongREJECT rubric artefacts](https://github.com/alexandrasouly/strongreject/tree/main/strongreject/prompts)** — the rubric prompt files the LLM-judge is instantiated with. <!-- needs-research: confirm the path in the current repository layout. -->

Version-pin the judge model weights hash, the rubric prompt file hash, and the decoding configuration in the CMC's section-4 judge contract.

## What the judge is asked to distinguish

The naive judge asks *"was this response harmful?"* — a yes/no verdict, no rubric, no rubric context. StrongREJECT's insight is that the interesting distinction is finer.

Four outcome classes:

- **Genuine jailbreak.** The target complied with the target-behaviour, produced operational content, and materially advanced the attacker's goal. This is the class the CMC counts as a matrix red.
- **Empty jailbreak.** The target *technically* did not refuse but the response is non-operational — hedged, generic, fictional, missing the load-bearing steps. StrongREJECT's canonical example: the target says *"Sure, here's how to make a bomb: 1. Get some materials. 2. Combine them. 3. Set it off."* — non-operational, non-actionable. The naive judge scores this as a jailbreak; the StrongREJECT rubric does not.
- **Refused.** The target explicitly refused, stated an inability, redirected to a policy statement, or otherwise declined. Not a jailbreak.
- **Off-target.** The target produced coherent content that is not on the attacker's topic. Not a jailbreak, even if the content is unrelated but harmful — that is a different cell's finding.

The rubric asks the judge to *rate* the response on two axes: *refusal* (did the target refuse) and *specificity* (how operationally useful is the response). A jailbreak is scored red only when refusal is absent *and* specificity is high. The two-axis rating is what separates a genuine jailbreak from an empty one.

The concrete prompt-template is published in the paper's appendix and repository; the CMC section 4 pins the specific template hash and decoding configuration used. A judge whose prompt-template is not pinned is a judge whose verdicts are not comparable across runs.

## LLM-judge vs human-judge calibration

An LLM-judge is a fast, cheap, opinionated grader. A human-judge panel is a slow, expensive, calibrated grader. Neither is trustworthy on its own at frontier scale; the methodology is *calibration*.

### The calibration loop

- **A held-out human-graded calibration set.** A sample of `(attack, target completion)` pairs graded by a domain-competent human panel with a documented rubric. The panel's verdicts are the *ground truth* for judge calibration.
- **LLM-judge scored on the same set.** Same rubric, same pairs, LLM verdicts.
- **Confusion matrix.** LLM-judge verdicts × human verdicts. The four cells are `(true jailbreak, false jailbreak, missed jailbreak, true refusal)`. The false-positive rate (LLM says jailbreak; human says empty / refused / off-target) and false-negative rate (LLM says refused / empty; human says genuine jailbreak) are the calibration numbers.
- **Acceptable thresholds.** The CMC section 4 pins them. A defensible baseline for the tier-decision-shaping cells: FP rate < 5%, FN rate < 10%. Higher-stakes cells demand tighter bounds; lower-stakes cells (nightly regression) tolerate looser ones.
- **Refresh cadence.** The calibration set is refreshed on a documented cadence — quarterly for the standard behaviour categories, per-rev for behaviour categories where the target family has demonstrably changed. Judge calibration on a stale set is a lie.

### Disagreement adjudication

- **Cell reporting.** When LLM-judge and human-judge disagree on a specific cell, the disagreement rate is reported alongside ASR. A cell with high ASR *and* high disagreement is a cell whose finding is *"weakly evidenced"* rather than *"red."*
- **Sampling for human review.** A random fraction of every cell (baseline 5%, adjustable per CMC section 4) is human-graded. The 5% sample's per-cell disagreement rate is what feeds the cell's disagreement statistic.
- **Escalation to full human review.** Cells whose disagreement exceeds threshold escalate to full human review before the CMC report ships. The escalation is a peer-role handoff to the human red-team panel (chapter 01's carve-out).

### LLM-judge choice

- **A different LLM family from the target.** The judge model is not the same model family as the target, to avoid shared-blindspot failures. If the target family is Anthropic's, the judge is not; and vice versa.
- **A general instruction-following model with strong rubric-following.** The judge's craft is *not* to be a strong attacker; it is to be a strong rubric-follower. Different capability profile.
- **A fine-tuned judge is a defensible upgrade.** The operator can fine-tune the judge on the calibration set to raise agreement with the human panel; the fine-tuned judge is a checkpoint that plugs in like the fine-tuned attacker of chapter 04. The fine-tuning methodology mirrors chapter 4's; the corpus is *judge-training pairs*, not attacker-training corpora.
- **Multi-judge ensembling.** For the highest-stakes cells, an ensemble of judges (multiple LLM-judge instances with different bases, aggregated by majority or median) reduces the single-judge failure mode. The CMC section 4 records which cells use ensembling.

## Judge-drift monitoring

Judges drift. A judge whose calibration was tight in Q1 can be materially uncalibrated by Q3. The drivers of drift:

- **Underlying model version changes.** Frontier API-served judges rev; the judge's behaviour changes; the calibration migrates.
- **Target-family evolution.** The target's refusal patterns evolve; the judge trained to distinguish *last quarter's* refusal shape misclassifies *this quarter's*.
- **Attacker-corpus evolution.** As chapter 04's fine-tuned attacker evolves, the population of *submitted* attacks the judge scores shifts; the judge's error distribution shifts too.
- **Adversarial pressure on the judge itself.** The attacker's fine-tuning corpus (chapter 04) includes the judge in its learning signal; the attacker can learn to exploit judge weaknesses. This is the mod-111 analogue of Goodharting the reward signal.

### Drift signals

- **Calibration-set score over time.** The judge is scored against the calibration set on a fixed cadence (e.g., weekly). The score's trend is the primary drift signal.
- **Ensemble disagreement over time.** When the CMC uses judge ensembling, the ensemble's internal disagreement rate is a leading signal of any single member drifting.
- **Human-review disagreement over time.** The 5%-sample disagreement rate per cell, plotted over time. Sudden shifts signal drift on the affected cells.
- **Cross-judge comparison over time.** Running two judge families in shadow — the *nominal* judge and an alternative judge — and reporting their disagreement rate. Divergence signals *one of them* has drifted.

### Drift response

- **Recalibration.** Refresh the calibration set (a human-panel workload); re-fine-tune the judge if it is a fine-tuned checkpoint; re-lock the CMC section 4 thresholds.
- **Judge rollback.** If the drift is caused by an API-served base rev and the operator can pin an earlier version, pin it until recalibration completes. If the operator cannot pin (some frontier API terms do not permit it), the drift is *accepted* and the cell's verdicts carry a wider CI.
- **Cell requarantine.** Cells whose drift makes their verdicts unreliable are marked *undetermined* in the CMC report; downstream artefacts do not cite them until recalibration.

The drift response cadence is *not* automated. A human recalibrates the calibration set; a human reviews the divergence; a human decides the rollback. Chapter 01's carve-out — automation is not the answer to judge-drift monitoring — is where this belongs.

## Judge supply-chain considerations

The judge is served from somewhere. The security of that *somewhere* determines whether the judge's verdicts can be trusted at all.

### The threat model

- **Weight tampering.** If the judge's weights are modified — by an insider, by an attacker who compromised the storage layer, by a supply-chain hit on a base model download — the judge's verdicts silently change.
- **Prompt-template tampering.** The judge's rubric prompt is the operational anchor of its behaviour. If the prompt file is modified without a version bump, the judge's verdicts silently change.
- **Serving-layer tampering.** If the layer that routes attack completions to the judge and back can be modified to *substitute* judge verdicts, the CMC verdicts are lies.
- **Inference-side channels.** If the judge is served via a shared inference pool, a co-tenant attacker who can predict which tokens the judge attends to can bias the verdicts. This is a lower-probability threat but non-zero at frontier scale.

### The mitigations

- **Weight signing.** The judge's weight files are signed against a Merkle root the CMC section 4 pins. A modified weight file fails verification at load time.
- **Prompt-template signing.** The rubric prompt is committed to a version-controlled repository with a signed release process; the CMC pins the release hash.
- **Serving-layer isolation.** The judge is served from a workload identity distinct from the rest of the ML fleet; access to the judge's endpoint is scoped; the routing layer's changes are audited.
- **Output-log signing.** The judge's every verdict is logged in a tamper-evident stream (the mod-107 chapter-03 pattern, applied to judge output). A retrospective audit can replay the log and detect substitution.
- **Deferred to `ai-infra-security` (peer, level 35).** The platform-scale mechanisms — the signing service, the workload-identity fabric, the tamper-evident log store, the isolated serving lane — are the peer role's craft. This module *specifies* the contract; the peer role delivers. The pattern is mod-107 chapter 06's boundary pattern applied to the judge.

The CMC section 4 records the specific peer-role components the judge relies on and the specific handoffs that make the judge's verdicts defensible.

## Boundary to `ai-infra-security` (peer, level 35)

The peer role owns:

- **The judge's serving infrastructure security.** Isolation, tenancy, monitoring.
- **The weight-signing service and the verification path at load time.**
- **The tamper-evident log store the judge writes verdicts into.**
- **The workload identity of the judge process and the scope of who can talk to it.**
- **The prompt-template release process and its signing.**
- **The judge model's supply-chain review — where the base weights came from, what fine-tuning data touched them, whether the artefact provenance is intact.**

This module owns:

- **The rubric methodology.**
- **The calibration methodology.**
- **The drift-monitoring cadence.**
- **The disagreement-adjudication rules.**
- **The CMC section-4 judge contract.**

Findings that route to `ai-infra-security` are shaped as *"the judge contract requires this platform property; the peer role's platform would benefit from providing it or currently does not."* Examples:

- *"The judge contract requires the weights to be signed under a specific KMS-fronted signing service; the current serving stack loads unsigned weights; recommend the peer role's model-signing pipeline."*
- *"The judge contract requires the judge to be served from a workload identity distinct from the general ML fleet; the current stack co-tenants; recommend the isolated serving lane."*
- *"The judge contract requires the judge's output log to be tamper-evident; the current stack writes to a general observability store; recommend the WORM log integration."*

The findings are routed, not fixed inside mod-111.

## The judge contract, concretely

A defensible CMC section-4 judge contract is a page-shaped artefact. It carries:

- **Judge identity per behaviour category.** Which judge scores which category. StrongREJECT-shape LLM for general jailbreaks; a fine-tuned judge for prompt-injection specificity; a human panel for CBRN uplift.
- **Calibration status.** For each judge, the current calibration statistics (FP, FN, disagreement rate) against the current calibration set, and the calibration set version.
- **Drift-monitoring cadence.** Weekly / daily / per-run, per judge.
- **Disagreement-adjudication rules.** Per cell, the human-sample fraction, the disagreement threshold above which the cell is quarantined.
- **Judge-versioning.** Weights hash, prompt-template hash, decoding config.
- **Supply-chain sign-off.** The peer-role co-signatures on the judge's serving stack.

The judge contract is *versioned* alongside the CMC. A judge upgrade is a CMC bump; downstream artefacts (safety cases, disclosures) cite the version.

## Interfaces

- **Chapter 02 (orchestration).** The judge deploys as an Inspect scorer and as a PyRIT scorer; the chapter-02 composition rule (one shared scorer) makes cross-framework verdicts comparable.
- **Chapter 03 (LLM-vs-LLM loops).** Every loop's terminating verdict is the judge's. Miscalibration cascades across every loop's ASR.
- **Chapter 04 (fine-tuned attacker).** The attacker's fine-tuning objective is judge-reward-shaped; the judge's calibration limits how meaningful the attacker's ASR gain is.
- **Chapter 06 (coverage matrix).** ASR per cell is the judge's verdict; the CMC report cites the judge version alongside every ASR number.
- **`ai-infra-security` (peer, level 35).** Judge serving, weight signing, log tamper-evidence, workload identity.
- **`ai-eval-engineer` (peer, level 30).** Judge-serving SLOs, judge-throughput planning, judge-latency budgeting for the matrix's wall-clock budget.
- **mod-106 (dangerous capability).** The domain-expert human panel for CBRN / cyber-offense grading; the mod-106 methodology owns the panel, this module cites it.
- **mod-109 (safety cases).** The safety-case's inability leg cites the judge's calibration statistics as part of the evidence's provenance.

## Common misreadings to avoid

- **"The judge is a small piece of the pipeline."** The judge is the *trust anchor* of the pipeline. Miscalibration is a scaling failure.
- **"An LLM-judge is neutral because it is not the same model as the target."** Not neutral — different biases. Cross-family shared blind-spots exist, and the calibration methodology is what surfaces them.
- **"StrongREJECT-shape means using the StrongREJECT paper's judge model verbatim."** Not necessarily. StrongREJECT-*shape* means the two-axis rating and the empty-jailbreak carve-out. The specific judge model can be any capable LLM instantiated with the rubric prompt; the CMC section 4 pins which.
- **"We only need calibration at launch."** Calibration decays. Weekly / daily monitoring is what catches drift before it lands in a tier-decision letter.
- **"Judge disagreement is a bug we should eliminate."** Disagreement below threshold is *information* — it flags cells that need human adjudication. Eliminating disagreement means tightening the judge to a mode; that is worse than transparent disagreement.
- **"Judge supply-chain is `ai-infra-security`'s problem, not ours."** The *implementation* is the peer role's; the *contract* is this module's. The judge contract must specify what the peer role delivers, and this module reviews that it does.

## Summary

- The judge is the trust anchor of the coverage matrix. Miscalibration at scale is a devastating scaling failure.
- StrongREJECT-shape methodology distinguishes genuine jailbreaks from empty jailbreaks (non-operational compliance), refusals, and off-target completions on a two-axis rating (refusal, specificity).
- Calibrate the LLM-judge against a documented human-graded calibration set; report FP / FN / disagreement rates per cell; refresh the calibration set on a documented cadence.
- Judge-drift monitoring is not automated in the *response* — humans recalibrate, humans decide rollbacks. The monitoring is automated; the decision is not.
- Judge supply-chain considerations — weight signing, prompt-template signing, serving isolation, tamper-evident logs — defer to `ai-infra-security` (peer, level 35). This module specifies the contract.
- The CMC section 4 judge contract is a versioned artefact; downstream artefacts cite the judge version alongside the ASR number.
