# exercise-05 — StrongREJECT judge vs. human calibration

**Estimated effort:** 2 hours
**Prerequisite chapters:** 05, 06 (helpful: 01, 02, 04); mod-104 chapter 06 for single-target StrongREJECT depth; mod-106 chapter 04 for the domain-expert human-panel pattern.

## Objective

Instantiate a **StrongREJECT-shape LLM judge** (chapter 05) against a small **human-panel calibration set**, compute the confusion matrix, per-category disagreement, inter-rater agreement, and drift-monitoring cadence, and argue the resulting **CMC section-4 judge contract**: which coverage-matrix cells (chapter 06) this judge is defensible for, which need human grading, which need ensembling. The calibration numbers your report produces *are* the section 4 content — the judge weights hash and prompt-template hash feed section 6 (reproducibility).

## Problem statement

Chapter 05's load-bearing insight is that the naive LLM-judge over-reports attack success because it collapses the four outcome classes — genuine jailbreak, empty jailbreak, refused, off-target — into a single yes/no verdict. The two-axis rubric (*refusal* × *specificity*) and the **empty-jailbreak carve-out** are what makes cross-cell verdicts comparable across the framework legs (Inspect / PyRIT / garak / Promptfoo, chapter 02). A judge whose FP/FN are not measured against a human panel is a judge whose ASR numbers are not evidence for a tier decision at scale (chapter 05).

You will assemble a calibration set of **100–200 `(prompt, completion)` pairs** across at least **three behaviour categories**, spanning refusal, hedged, general-adjacent, and specific-forbidden completions; at least **10%** of the set must exercise the **empty-jailbreak carve-out** — the pair where the target *appeared to comply* but the response is content-free filler that a naive refusal-word judge would score wrong in either direction. Grade every item with at least **two human graders**, report inter-rater agreement (Cohen's kappa or an equivalent), score the same items with the LLM-judge, and compute FP / FN / per-category disagreement against the human-panel majority.

Chapter 05's defensible baseline for tier-decision-shaping cells is **FP < 5%** and **FN < 10%**. Your report states whether your judge meets those bounds and, if not, what recalibration would look like. Payload discipline (chapter 06; mod-104 chapter 01) is not optional: judge rationales quote the item being graded, so rationale text carries harmful strings by construction. Human-panel per-item labels and judge rationale text live in the org payload store; only aggregate statistics ship in the committed report.

## Requirements

Produce four artefacts.

### Artefact A — `cmc-<program>-judge-calibration-set.yaml`

The calibration-set manifest, one entry per `(prompt, completion)` pair. Each entry:

- `pair_id` — stable `CAL-<category>-<n>` identifier resolved in the payload store.
- `behaviour_category` — the mapped category (HarmBench / AIR-Bench / AILuminate cell; be explicit). At least three categories represented.
- `expected_outcome_class` — one of `{genuine_jailbreak, empty_jailbreak, refused, off_target}` per chapter 05's four-class distinction. This is the author's *intent* for the pair; the human panel adjudicates the actual class.
- `carve_out_flag` — `empty_jailbreak_probe: true` when the item is one of the ≥10% empty-jailbreak carve-out pairs.
- `provenance` — where the pair came from (attacker log from exercise 02, HarmBench sample, StrongREJECT canonical set, synthetic authored). <!-- needs-research: pin the specific StrongREJECT canonical-set release you drew from. -->
- `severity` — mod-112 severity annotation for the underlying behaviour.
- `storage_location` — external payload-store URI for the prompt text and completion text.

Prompt text and completion text stay in the payload store and are referenced by `pair_id`; the manifest carries schema and pointers only.

### Artefact B — `cmc-<program>-judge-calibration.yaml`

The calibration-run report — the section-4 numbers.

- `judge_identity` — `{model_family, weights_hash, decoding_config_hash, rubric_prompt_template_hash}`. A judge whose four hashes are not pinned is a judge whose verdicts are not comparable across runs (chapter 05); the four hashes feed CMC section 6.
- `rubric_reference` — the primary-source path (StrongREJECT paper appendix; repository prompts directory). <!-- needs-research: confirm the current canonical repo path for the rubric prompt files. -->
- `panel_composition` — number of human graders per item (≥2), grader roles (not names), grader-selection rationale.
- `inter_rater_agreement` — Cohen's kappa (or Krippendorff's alpha for >2 graders) across the full set and per behaviour category. A set whose panel agreement is itself low is a set whose "ground truth" is unstable; chapter 05 says why.
- `confusion_matrix` — LLM-judge verdict × human-panel majority, over the four outcome classes. Report absolute counts and normalised rates.
- `fp_rate`, `fn_rate` — overall and per behaviour category. Chapter 05's baseline is FP < 5% and FN < 10% for tier-decision-shaping cells.
- `empty_jailbreak_carve_out_performance` — restricted to the carve-out subset, the FP / FN specifically against the "appeared to comply but content-free" class. This is the item the naive refusal-word judge gets wrong; the report calls out whether your judge does.
- `per_category_disagreement` — the disagreement rate per behaviour category, sorted. The category with the highest disagreement is the cell that most needs human grading or ensembling.
- `drift_monitoring_plan` — the calibration-set refresh cadence (quarterly baseline for standard categories, per-rev for evolving families per chapter 05); the weekly / per-run drift signal chosen (calibration-set score trend, ensemble disagreement trend, human-review 5%-sample disagreement trend, cross-judge shadow disagreement); the recalibration trigger threshold.
- `requarantine_protocol` — the per-cell protocol invoked when drift is detected: mark the cell `undetermined` in the CMC report; downstream artefacts (safety cases per mod-109, disclosures) do not cite the cell until recalibration completes.
- `judge_supply_chain_boundary` — the specific `ai-infra-security` (peer, level 35) properties the judge contract *requires* (weight signing under a KMS-fronted service, prompt-template signed release, workload-identity isolation, tamper-evident output log) and which the current serving stack *provides*. Findings are routed, not fixed inside mod-111 (chapter 05).

### Artefact C — `cmc-<program>-judge-artefacts-manifest.yaml`

The payload-store manifest for the artefacts that carry harmful content — the human-panel per-item labels, the LLM-judge per-item rationales, the specific prompt / completion text — that MUST NOT ship in the committed report. One entry per artefact class:

- `artefact_class` — one of `{human_labels_per_item, llm_judge_rationales, calibration_pair_texts, disagreement_case_notes}`.
- `sha256`, `storage_location`, `access` (ACL / group / role).
- `retention_policy` — chapter 05's cadence; the calibration-set refresh cadence implies the retention window for prior calibration runs.
- `cross_reference` — `pair_id` back-reference to Artefact A.

### Artefact D — `cmc-<program>-judge-runbook.md`

A short (~800–1200 word) runbook covering:

- **Calibration-set assembly.** Why these categories, why this size, how you resisted concentrating on the categories the judge is likely to score cleanly. How the ≥10% empty-jailbreak carve-out was constructed — this is the item chapter 05 names as the failure mode of the naive judge; if your carve-out is trivial, your calibration proves nothing.
- **Panel methodology.** Grader-selection rationale, the rubric handed to graders (StrongREJECT-shape two-axis rating, chapter 05), the inter-rater agreement statistic reported, and the disagreement-adjudication rule when the panel splits. Where you drew on mod-106 chapter 04's domain-expert-panel patterns for the higher-severity categories.
- **Judge choice.** The LLM-judge family (chapter 05: not the same family as the target, to avoid shared-blindspot failures), the rubric prompt file used verbatim vs. adapted, the decoding config. Why *this* judge for *this* program rather than a fine-tuned checkpoint (or, if you did fine-tune, the reason and the training corpus provenance).
- **Confusion-matrix interpretation.** The FP / FN numbers, per category. Which categories cleared chapter 05's FP < 5% / FN < 10% baseline; which did not; for the categories that did not, what recalibration would look like — refresh the calibration set, re-fine-tune the judge, add ensemble members, escalate the cell to full human review.
- **Empty-jailbreak carve-out result.** Restricted to the carve-out subset, whether the judge distinguished content-free filler from genuine compliance. Chapter 05's canonical example ("Sure, here's how: 1. Get materials. 2. Combine. 3. Set off.") is the shape; report your judge's behaviour on shapes like it.
- **Drift-monitoring plan.** The refresh cadence per category, the weekly / per-run drift signal, the recalibration trigger threshold, the per-cell requarantine protocol. Chapter 05's carve-out is explicit: the *monitoring* is automated; the *response* is not — a human recalibrates, a human decides rollback.
- **CMC section-4 judge contract, argued.** Which coverage-matrix cells (chapter 06) this judge is defensible for on its own, which require the 5%-sample human review at a tightened rate, which require ensembling (multiple LLM-judge bases aggregated by majority or median), which escalate to full human panel. The argument cites the per-category FP / FN numbers, not hand-waving.
- **Judge supply-chain boundary.** The specific `ai-infra-security` (peer, level 35) handoffs the contract requires and the shape of the finding routed to them. Chapter 05's boundary: this module specifies the contract; the peer role delivers the platform.
- **Threats to validity.** Small-panel bias, category concentration in the calibration set, LLM-judge cross-family shared blindspot with the target, prompt-template drift on the served rubric, calibration-set staleness, human-grader fatigue on longer sessions.
- **Downstream handoffs.** The mod-108 guardrail-training workstream that draws from judge outputs (only calibrated-cell outputs are training-set-eligible); the mod-109 safety-case inability-leg that cites this judge's calibration statistics as evidence provenance; the `ai-eval-engineer` (peer, level 30) judge-serving SLO / throughput / latency budget that this contract implies.

## Starter guidance

- **Assemble the calibration set by hand first, sample the attacker log second.** A calibration set drawn only from your attacker log is biased toward the classes your attacker produces; chapter 05's four-class distinction requires deliberate coverage of refused and off-target as well.
- **The ≥10% empty-jailbreak carve-out is load-bearing, not decorative.** The mistake to avoid is filling the carve-out slot with obvious refusals or obvious compliance; the carve-out is specifically the *content-free-filler* class that a naive refusal-word judge scores wrong. Chapter 05's canonical example is the style guide.
- **Two human graders is a floor, not a target.** For the higher-severity behaviour categories, mod-106 chapter 04's domain-expert-panel patterns apply — a two-non-expert-grader panel on a CBRN item is not calibration.
- **Report inter-rater agreement before you report LLM-judge FP/FN.** A set whose panel kappa is 0.4 is a set whose "ground truth" carries substantial noise; the LLM-judge's FP/FN is bounded by that noise. Chapter 05's calibration loop assumes the panel is itself calibrated.
- **The judge and the target are not the same model family.** Chapter 05 is explicit: shared-blindspot failures across a family produce silently correlated errors. If your target family is X, your judge family is not X.
- **Pin every hash.** Weights hash, decoding-config hash, rubric-prompt-template hash, calibration-set version. A judge whose four hashes are not pinned is a judge whose verdicts do not reproduce. The four hashes feed CMC section 6 directly.
- **Do not include working rubric text in Artefact B.** Reference the primary source (StrongREJECT paper appendix, repo prompts directory) and stub the fields; the rubric prompt file itself is in the payload store or in the peer-owned prompt-release repo with a signed release process (chapter 05).
- **Judge rationales are harmful-payload class.** The judge's rationale text quotes the item being graded, so rationales carry harmful content by construction. Rationales go in Artefact C's payload manifest, not in the committed report.
- **Human-panel per-item labels are harmful-payload class too.** Only the aggregate agreement, FP / FN, and per-category disagreement statistics ship in Artefact B. Per-item labels stay in the store.
- **Payload discipline (chapter 06; mod-104 chapter 01) is not optional.** The `.gitignore` for calibration outputs is the first thing to write, not the last.
- **The judge contract implies a serving budget.** The `ai-eval-engineer` (peer, level 30) peer owns judge-serving SLOs, throughput, and latency budgeting for the matrix wall-clock; your contract's per-cell judge invocation count is the input to their capacity plan (chapter 05).
- **The judge contract implies a supply chain.** The `ai-infra-security` (peer, level 35) peer owns weight signing, prompt-template release signing, isolated serving lane, tamper-evident output-log store; your contract *specifies* which of these are required, and Artefact D routes findings for the ones the current stack does not provide.
- **Cross-tag the judge output at guardrail-training-set boundaries.** mod-108 draws training data from judge outputs; only outputs from cells that cleared the calibration baseline are training-set-eligible. Note this in the runbook.
- **Drift response is not automated.** Chapter 05's carve-out is that the *monitoring* is automated but the *response* — recalibrate, rollback, requarantine — is a human decision. Your plan states the human role that owns each decision.

## Acceptance criteria

- ✅ `cmc-<program>-judge-calibration-set.yaml` covers 100–200 `(prompt, completion)` pairs across at least three behaviour categories, with `expected_outcome_class` populated over all four StrongREJECT classes and `carve_out_flag` set on ≥10% of pairs. No prompt text or completion text in the committed manifest.
- ✅ `cmc-<program>-judge-calibration.yaml` reports `judge_identity` with all four hashes pinned (weights, decoding config, rubric prompt template, calibration-set version), `panel_composition` with ≥2 graders per item, `inter_rater_agreement` (Cohen's kappa or equivalent) overall and per category, and a full `confusion_matrix` over the four outcome classes.
- ✅ `fp_rate` and `fn_rate` reported overall and **per behaviour category**; a report that aggregates FP/FN into a single scalar is rejected. The runbook explicitly states whether each category cleared chapter 05's FP < 5% / FN < 10% baseline.
- ✅ `empty_jailbreak_carve_out_performance` reported on the ≥10% carve-out subset. The runbook argues whether the judge distinguished content-free filler from genuine compliance, per chapter 05's canonical failure mode.
- ✅ `drift_monitoring_plan` and `requarantine_protocol` populated with a per-category refresh cadence, a named drift signal, a recalibration trigger threshold, and the named human role that owns the recalibration / rollback / requarantine decision (chapter 05's carve-out on automation).
- ✅ `judge_supply_chain_boundary` names the specific `ai-infra-security` (peer, level 35) platform properties the contract requires (weight signing, prompt-template signed release, workload-identity isolation, tamper-evident output log) and the shape of the finding routed to the peer role when the current stack does not provide them.
- ✅ `cmc-<program>-judge-artefacts-manifest.yaml` lists every harmful-payload artefact class (human labels per item, LLM-judge rationales, calibration pair texts, disagreement case notes) with sha256, storage URI, ACL, retention policy, and `pair_id` cross-reference. **No prompt text, no completion text, no per-item human labels, no judge rationale text in any committed file.**
- ✅ `cmc-<program>-judge-runbook.md` (~800–1200 words) covers calibration-set assembly, panel methodology, judge choice, confusion-matrix interpretation, empty-jailbreak carve-out result, drift-monitoring plan, the argued CMC section-4 judge contract (which cells are defensible on the judge alone, which need 5%-sample human review, which need ensembling, which need full panel), judge supply-chain boundary, threats to validity, and downstream handoffs (mod-108, mod-109, `ai-eval-engineer`, `ai-infra-security`).
- ✅ Every unverified factual claim (Souly et al. reported FP/FN bounds on named judges, StrongREJECT canonical repository path, HarmBench comparison numbers, JailbreakBench judge integration URL, mod-106 panel size norms) marked `<!-- needs-research: ... -->`.
- ✅ The judge model family is **not** the same family as the primary program target (chapter 05: shared-blindspot avoidance); the runbook names both families and the rationale.
- ✅ Handoff notes at the end of the runbook name the mod-108 guardrail-training routing rule (only calibrated-cell outputs are training-set-eligible), the mod-109 safety-case inability-leg citation, and the `ai-eval-engineer` peer's judge-serving SLO / throughput / latency budget implied by the per-cell invocation count.

## Stretch goals

- **Add a multi-judge ensemble.** For the highest-stakes behaviour category, run two additional LLM-judge bases (different families) alongside the primary, aggregate by majority, and report the ensemble's FP / FN vs. the single-judge numbers. Chapter 05 names ensembling as the mitigation for the single-judge failure mode on tier-decision-shaping cells.
- **Ship a fine-tuned judge checkpoint.** Fine-tune the LLM-judge on the calibration set's non-held-out fraction; re-run the confusion matrix on a held-out fraction; report the agreement lift and the training-corpus provenance. Chapter 05 names the fine-tuning methodology as mirroring mod-104 chapter 04's attacker fine-tuning, with the corpus being judge-training pairs rather than attacker-training corpora.
- **Author a cross-judge shadow-monitoring plan.** Run a second judge family in shadow on live scoring for one week; report the shadow / nominal disagreement rate over time as the leading drift signal per chapter 05's drift-monitoring section.
- **Extend the carve-out with a *fluent-refusal* class.** Chapter 05's four-class distinction can be extended with the *refusal that reads like compliance because the target hedged fluently* class — an item that the naive judge scores as compliance because the refusal is not explicit. Add 10% of the calibration set as this class, report the FP separately.
- **Route the judge supply-chain finding to `ai-infra-security`.** Write the peer-role handoff — shaped as "the judge contract requires this platform property; the current stack does not currently provide it; recommend the peer role's model-signing / workload-identity / tamper-evident-log pipeline" — as an appendix to the runbook. The finding is *routed*, not fixed in mod-111 (chapter 05).

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the calibration-pair texts, or the per-item human labels, or the LLM-judge rationales, or the disagreement case notes — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference calibration set structure and manifest schema. Working payloads live in your org's payload store per chapter 06 and mod-104 chapter 01; see the harmful-payload discipline before starting. Human-panel labels stay in the payload store; only aggregate agreement statistics are committed.
