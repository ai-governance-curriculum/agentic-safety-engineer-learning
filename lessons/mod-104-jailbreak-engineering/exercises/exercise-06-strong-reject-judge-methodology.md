# exercise-06 — StrongREJECT-Style Judge Methodology

**Estimated effort:** 1 hour
**Prerequisite chapters:** 07 (helpful: 03, 06).

## Objective

Author a **shippable StrongREJECT-style judge** (chapter 07) for your JEH: a versioned rubric, a few-shot anchor set, a JSON parser, a cost-controlled cascade, a **calibration figure** (human-agreement number) on a representative sample from exercises 02–05, and an **over-refusal control** on JailbreakBench benign pairs (or an XSTest-style equivalent). This is the artefact chapter 06's coverage matrix scored with and the artefact chapter 07 makes non-optional.

## Problem statement

Take the responses produced by exercises 02 (PAIR / TAP), 03 (Crescendo), and 04 (linguistic families). Sample a small **calibration set** (chapter 07 recommends 200–1000 pairs per rubric version; a defensible minimum for this exercise is 100–200). Have two independent human labellers score every pair against your rubric; a third resolves disagreements. Compute Cohen's $\kappa$ (or matched percent-agreement) per dimension and for the composite.

Ship the judge as a Python package (or equivalent) with a stable interface `score(prompt, response, objective) → {refusal, specificity, on_topic, composite, rationale}` and a `calibrate(sample) → {kappa, per_dimension, agreement}` method. Wire it into the exercise-05 coverage matrix as the `strongreject` judge column.

Payload discipline (chapter 07): judge rationales quote harmful content by construction. Rationales live in the payload store, cited by ID; numeric scores are safe to publish.

## Requirements

Produce four artefacts.

### Artefact A — `jeh-<target>-judge-spec.md` + `jeh-<target>-judge-spec.yaml`

The judge's specification. YAML fields:

- `judge_id`, `version`, `underlying_model` (model ID + snapshot), `decoding` (temperature, top_p — temperature 0 recommended per chapter 07's parser stability discussion).
- `rubric_version`, `rubric_dimensions` — the three dimensions from chapter 07 (refusal, specificity/actionability, on-topic/convincingness) with per-dimension scale points and anchor examples referenced by ID (anchors live in the payload store).
- `composite_formula` — the monotone combination that produces a normalised 0–1 composite. Chapter 07 says StrongREJECT's Appendix is the reference; the JEH re-derives per rubric version.
- `anchor_set_id` — payload-store reference to the few-shot anchor set.
- `parser_spec` — the JSON schema the parser enforces, the retry-on-parse-failure policy (chapter 07's `parse_or_reprompt` pattern).
- `cascade_spec` — the cheap first-pass filter (a small model with a two-question prompt per chapter 07's cost-adaptive-judging pattern), the cutoff rule for escalation to the full rubric judge, the expected cost saving.
- `sub_judges` — enumerated per attack family (cyber-offence, low-resource-language, cipher, multi-turn per-turn). Each sub-judge has its own `judge_id`, `applies_to_cells`, and `calibration_ref`.

The Markdown spec (~600–1000 words) reads the YAML and includes the **operator-side prompt templates** (safe to publish per chapter 07's rationale-in-store rule) and the reasoning for each rubric-dimension choice.

### Artefact B — `jeh-<target>-judge-calibration.yaml`

The calibration figures. Contents:

- `calibration_set_id` — payload-store reference to the labelled sample.
- `n_pairs`, `labelling_protocol` (two labellers + tiebreak per chapter 07), `label_date`.
- `per_dimension_kappa`: `{refusal, specificity, on_topic}` — Cohen's $\kappa$ or matched percent-agreement.
- `composite_kappa`.
- `per_family_kappa` — decomposed across attack families in scope (GCG, PAIR/TAP, Crescendo, many-shot, persona, low-resource, cipher). Chapter 07's sub-judge decomposition depends on the per-family number.
- `per_language_kappa` for low-resource language sub-judges.
- `per_cipher_kappa` for cipher sub-judges.
- `drift_alarm_threshold` — the agreement floor below which findings are gated pending recalibration.
- `recalibration_schedule` — cadence (chapter 07 recommends monthly or per-model-update).
- `known_limitations` — cells / families / languages where the calibration figure is weak, and how the coverage matrix flags cells scored by a weak sub-judge.

### Artefact C — `jeh-<target>-judge-implementation/`

A working implementation directory (or equivalent). At minimum:

- The judge class implementing the chapter-07 skeleton (`StrongRejectStyleJudge` with `score(...)` and `calibrate(...)`).
- The cascade implementation (cheap filter → rubric judge → human spot-check hook).
- The parser with `parse_or_reprompt` retries.
- A CI-runnable test suite that:
  - Verifies the parser handles well-formed JSON, malformed JSON (single retry succeeds), and unrecoverable failures (surfaced, not silent).
  - Verifies the sub-judge routing (a low-resource-language input routes to the bilingual sub-judge, a cipher input routes to the cipher sub-judge).
  - Verifies the calibration method reproduces the reported per-dimension $\kappa$ on the calibration set.
- A brief README noting environment, dependencies, and how to run the tests. **No rationale text and no harmful completion text is committed** — the test suite reads calibration examples from the store.

### Artefact D — `jeh-<target>-over-refusal-report.yaml` + a short note

The over-refusal control (chapter 07's mandatory pairing). Contents:

- `benign_set_id` — JailbreakBench benign pairs or XSTest sample.
- Per attack family × benchmark cell: `over_refusal_rate` under the same judge, with 95% CI.
- The Pareto point per cell: `(asr_harmful, over_refusal_benign)`.
- Cells where over-refusal is on the wrong side of the Pareto frontier are flagged as a **utility regression** (chapter 07's warning about defence stacks that refuse everything).
- A short note (~200–400 words) reading the Pareto data: which cells have healthy low-ASR + low-over-refusal, which have suspicious low-ASR + high-over-refusal, and what that implies for mod-108's defence-tuning workstream.

## Starter guidance

- **Read Souly et al. (2024) before writing the rubric.** Chapter 07 references the specific dimensions and formulaic composite; the paper's Appendix is the authoritative rubric.
- **Anchor examples do the work.** A rubric with strong few-shot anchors under-performs a rubric with weak anchors even if the rubric text is identical. Draw anchors from your own exercises 02–04 responses, not synthetic examples.
- **Cascade cost matters.** Chapter 07's cost-adaptive-judging pattern is what makes the judge affordable at coverage-matrix scale. Even a naive cheap-filter (small model + two-question prompt) cuts cost by a large factor; report the observed saving.
- **Two labellers minimum, three to break ties.** Chapter 07 says single-labeller ground truth is not ground truth; the calibration figure is derived from the consensus.
- **Recalibrate per rubric change.** Any rubric revision is a new judge version. Rescoring the calibration set is required — don't shortcut this.
- **Decompose sub-judges by family.** A single rubric on all families is a fine starting point; chapter 07 lists the families warranting their own sub-judge (cyber-offence, low-resource, cipher, multi-turn per-turn). Report where the single judge and the sub-judge disagree.
- **Prohibit refusal-word regex as the headline judge.** It's a cheap first-pass cascade component at most. Chapter 07 documents the over-count in detail.
- **Publish operator prompts; store rationales.** Chapter 07's storage rule: prompt templates are safe to publish; rationales that quote harmful content are payload-store material.
- **Cross-reference `model-evaluation-engineer`.** Chapter 07 cites the peer role for the statistical calibration methodology; their sampling / CI patterns are the reference.

## Acceptance criteria

- ✅ `jeh-<target>-judge-spec.yaml` covers judge ID, version, model, rubric dimensions, composite formula, anchor set reference, parser spec, cascade spec, and sub-judge decomposition per attack family.
- ✅ `jeh-<target>-judge-spec.md` includes operator-side prompt templates and per-dimension reasoning.
- ✅ `jeh-<target>-judge-calibration.yaml` reports per-dimension $\kappa$, composite $\kappa$, per-family $\kappa$, per-language $\kappa$, per-cipher $\kappa$, drift alarm, recalibration schedule, and known limitations. The calibration sample size is ≥100.
- ✅ `jeh-<target>-judge-implementation/` includes the judge class, cascade, parser with retries, and a CI-runnable test suite covering parser edge cases, sub-judge routing, and calibration reproduction.
- ✅ `jeh-<target>-over-refusal-report.yaml` includes per-cell `over_refusal_rate` alongside ASR, the `(asr, over_refusal)` Pareto point per cell, and a short reading of the data. Cells without an over-refusal number are flagged as `not_measured` with a reason.
- ✅ The judge is wired into the exercise-05 coverage matrix as the `strongreject` judge column. Exercise-05's `judge_disagreement` figures come from this judge vs. the benchmark-native judges.
- ✅ **No rationale text and no harmful completion text is committed anywhere.** Payloads and rationales live in the store; anchor examples are referenced by ID.
- ✅ Every unverified factual claim (Souly et al. specific dimensions and formula, XSTest specifics, per-language calibration guidance) marked `<!-- needs-research: ... -->`.
- ✅ Handoff notes at the end of the spec name the mod-108 workstream the judge drift signals feed and the mod-111 interface the judge exposes (batching, cascade, cost-per-1k-trials).

## Stretch goals

- **Ship a judge-drift monitor.** A cron-runnable comparison of the judge's scores over time on a stable holdout set; alarms when agreement drops below the drift-alarm threshold. Chapter 07's release-runbook recommendation.
- **Author a judge-cascade cost calculator.** Given cascade cutoff, filter cost, rubric cost, and expected escalation rate, project total cost per benchmark row. Feeds mod-111's scheduling.
- **Multi-model judge ensemble.** Chapter 07 discusses judge-attacker collusion; a cheap answer is an ensemble judge that combines scores from multiple LLM families. Report the ensemble's $\kappa$ vs. the single-model judge's $\kappa$ and the cost delta.
- **Adaptive-attack-survival judge integration.** Wire the judge into the adaptive-attack-survival measurement from exercise 05: the judge should score the adapted attacker's responses without being biased by having seen the defence stack. Report whether the adaptive judge and the baseline judge diverge.
- **Author the mod-108 handoff document.** A short spec of what the judge exports to mod-108's guardrail-training workstream: per-response scores, per-response rationales (via store), per-defence-stack survival figures. Chapter 09 codifies the interface; the document makes it concrete.

## Deliverable location

Personal notes or private repo. The judge implementation may be a private package; the spec and calibration files are personal notes. Do **not** commit anchor examples, rationales, or harmful completions into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference judge scaffold. Working payloads live in your org's payload store per chapter 01.
