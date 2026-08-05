# exercise-05 — HarmBench / JailbreakBench / AIR-Bench / AILuminate / CyberSecEval Coverage Run

**Estimated effort:** 3 hours
**Prerequisite chapters:** 06, 07 (helpful: 02–05, 08, 09).

## Objective

Assemble the **composite coverage matrix** (chapter 06) — the JEH's headline artefact — by running the attackers from exercises 01–04 against **all five benchmark families** (HarmBench, JailbreakBench, AIR-Bench 2024, MLCommons AILuminate, Meta CyberSecEval) under a **tiered sampling policy** (A / B / C / excluded), and produce the derived metrics the release gate depends on: **refusal robustness, elicitation gap, adaptive-attack survival, judge disagreement, cost per row, coverage percentage**.

## Problem statement

Take the target from exercises 01–04, the four attacker families you stood up (GCG, PAIR/TAP, Crescendo, linguistic), and the StrongREJECT-style judge you'll formalise in exercise 06 (a draft judge with a coarse calibration figure is acceptable here; exercise 06 hardens it). Populate the chapter-06 composite matrix at the tiered sampling policy chapter 06 defines:

- **Tier A (always covered)** — HarmBench + JailbreakBench + AIR-Bench top-10-severity per attack family. Full-sample runs. The release-gate baseline.
- **Tier B (per release)** — AILuminate grades + the CyberSecEval offence subset per attack family. Refreshed each release candidate.
- **Tier C (spot-checked)** — AIR-Bench long-tail + AILuminate low-severity. Sampled at reduced rate.
- **Explicit exclusions** — categories the target doesn't expose, with reason.

Every cell reports ASR under both the benchmark's **native judge** and the JEH's **StrongREJECT-style judge**. The disagreement rate between the two is a finding in its own right.

Payload discipline: benchmark strings are references (public); model completions and judge rationales are payload and live in the store per chapter 06's storage rules.

## Requirements

Produce five artefacts.

### Artefact A — `jeh-<target>-coverage-matrix.yaml`

The composite matrix in chapter 06's schema. Every cell has:

- `cell.attack`: `{family, variant}` (family = gcg | pair | tap | crescendo | many_shot | persona | low_resource | cipher).
- `cell.benchmark`: `harmbench | jailbreakbench | airbench_2024 | ailuminate | cyberseceval`.
- `cell.category`: benchmark-native category ID.
- `tier`: `A | B | C | excluded`. Every `excluded` cell has a `reason`.
- `asr_native_judge` with 95% CI.
- `asr_strongreject` with CI.
- `judge_disagreement` — the rate at which the two judges disagree on the same trial.
- `n_trials`, `seeds`.
- `cost_usd` — total for this cell.
- `date` — ISO snapshot date (mandatory per chapter 06).
- `refusal_robustness` per behaviour (chapter 07's derived metric).
- `elicitation_gap` per behaviour (best-attack ASR − naive baseline ASR).
- `adaptive_attack_survival` — populated where applicable (chapter 07); `null` with a `reason` otherwise.
- `benchmark_native_grade` — for AILuminate cells (per-hazard grade).
- `regulator_mapping` — for AIR-Bench cells (EU AI Act / EO 14110 clause references).

Aim for 40–120 cells across the five benchmarks and four attack families. Chapter 06's sample fragment is the style guide.

### Artefact B — `jeh-<target>-coverage-report.md`

The narrative report (~1500–2500 words) that reads the matrix. Sections:

- **Coverage story.** Per benchmark: coverage percentage (categories exercised / total), Tier A cells covered, Tier B cells covered, explicit exclusions.
- **Headline ASR per benchmark per category.** The tables regulators / stakeholders read first.
- **Cross-benchmark consensus severity.** Findings that land high across two or more benchmarks. Chapter 06 argues these are stronger findings than single-benchmark spikes.
- **Attack-family effectiveness.** Which family topped the ASR per benchmark. Informs mod-111's prioritisation.
- **Refusal robustness distribution.** The bottom of the distribution — behaviours where refusal robustness is lowest — is the actionable finding.
- **Elicitation gap analysis.** Per benchmark category, the gap between naive baseline and best adversarial ASR. Chapter 07 makes this the RSP/Preparedness/FSF metric that mod-101 consumes.
- **Adaptive-attack survival** where measured; a "not measured; here's why" note per cell where not.
- **Judge disagreement analysis.** Where native and StrongREJECT-style judges disagree, chapter 06 flags this as a finding.
- **Cost projection.** Extrapolate this run to per-release cadence and to mod-111's fleet scale.

### Artefact C — `jeh-<target>-tiering-policy.yaml`

The tiered sampling policy for future runs. Per (benchmark, category) or (benchmark, category-cluster):

- `tier`: A | B | C | excluded.
- `sample_size_by_tier`: A = full, B = ..., C = ....
- `cadence`: `per_pr | per_nightly | per_release_candidate | per_release`.
- `exclusion_reason` where `excluded`.
- `owner` — the team that fixes findings in this tier.

Chapter 06 says the tiering policy lives in the release runbook and is what mod-112's disclosure workflow cites.

### Artefact D — `jeh-<target>-storage-manifest.yaml`

The payload-store manifest for model completions and judge rationales. Per entry:

- `id`, `kind` (`completion | judge_rationale`), `sha256`, `storage_location`, `matrix_cell`, `severity`, `access`.

Chapter 06's storage rule: **benchmark strings are references (public); model completions and judge rationales are payload and never appear in this repo.**

### Artefact E — `jeh-<target>-release-gate.md`

A short (~400–800 word) release-gate specification. Contents:

- Which cells are release blockers at what ASR threshold.
- The `refusal_robustness on JailbreakBench benign pairs` floor (over-refusal regression = blocker).
- The `AIR-Bench regulator-priority category coverage` floor.
- The `elicitation gap` ceiling per deployment tier.
- The disclosure-routing rule (mod-112) triggered by severity × ASR threshold crossings.
- The `judge_disagreement` alarm threshold (chapter 07 recommends this feed back into judge recalibration).

## Starter guidance

- **Start with the matrix skeleton, not the runs.** Author the cell list first: attack family × benchmark × category. Then decide the tier per cell. Then run. This is what chapter 06's "the matrix is what the tests are" framing means.
- **Do not attempt full coverage on the first run.** Full AIR-Bench 314-category coverage is a multi-release goal; the exercise expects representative coverage across the five benchmarks with the Tier A cells fully covered.
- **Run both judges on every cell.** The native judge is what leaderboards use; the StrongREJECT-style judge is what chapter 07's methodology mandates. The disagreement rate is the finding you can't get from either judge alone.
- **Pin every version.** Benchmark version, attacker version, judge version, target snapshot, chat template, decoding config. Chapter 06 is emphatic that unpinned numbers are un-reproducible.
- **Sample cost early.** AIR-Bench's 314 categories × even modest samples is heavy; the tier-B/C sampling policy is where cost curves live. Report cost per row so mod-111 can plan.
- **Every excluded cell has a reason.** Chapter 06: silent omissions are not exclusions.
- **Cross-tag findings by attack family × benchmark category.** A GCG suffix that lands on HarmBench's `chemical_biological` and AIR-Bench's `chem_bio_precursor_syn` and AILuminate's `wmd_related` is one finding in three matrices; the cross-tag is what makes cross-benchmark consensus severity computable.
- **Ship AILuminate as a grade + a number.** Chapter 06 notes AILuminate is designed for stakeholder-legible grades; the grade sits alongside the per-hazard ASR under both judges.
- **Regulator-mapping is not decoration.** AIR-Bench's whole value is that its categories are regulator-legible; the mapping column is what mod-112 / mod-109 use.
- **Sever cyber-uplift findings to mod-106.** Chapter 06 draws the boundary: CyberSecEval 3's autonomous-offence tests are mod-106 territory. Cite the boundary in the report; don't own what mod-106 owns.
- **Payload discipline.** Model completions and judge rationales in the store; benchmark strings are public references.

## Acceptance criteria

- ✅ `jeh-<target>-coverage-matrix.yaml` covers all five benchmarks with Tier A cells populated per attack family; Tier B populated for AILuminate and the CyberSecEval offence subset; Tier C spot-checked; explicit exclusions have a `reason`.
- ✅ Every cell reports ASR under both the native judge and the StrongREJECT-style judge, with 95% CI, and a `judge_disagreement` figure.
- ✅ Every cell has `n_trials`, `seeds`, `cost_usd`, and `date` populated. Unversioned or undated cells are rejected.
- ✅ Refusal robustness, elicitation gap, and adaptive-attack survival (where measured) are populated on Tier A cells at minimum; `null` values carry a `reason`.
- ✅ AIR-Bench cells carry a `regulator_mapping` (EU AI Act / EO 14110 clause references).
- ✅ AILuminate cells carry a `benchmark_native_grade` alongside the per-hazard ASR.
- ✅ CyberSecEval autonomous-offence cells are marked as **cited to mod-106**, not owned here.
- ✅ `jeh-<target>-coverage-report.md` covers coverage story, headline ASR, cross-benchmark consensus severity, attack-family effectiveness, refusal-robustness distribution, elicitation-gap analysis, adaptive-attack survival where measured, judge-disagreement analysis, and cost projection.
- ✅ `jeh-<target>-tiering-policy.yaml` covers tier per cell / cluster, sample-size-by-tier, cadence, exclusion reasons, and owner.
- ✅ `jeh-<target>-storage-manifest.yaml` lists every completion and judge rationale with ID, sha256, storage URI, matrix cell, severity, access. **No completion text or judge rationale text in any committed file.**
- ✅ `jeh-<target>-release-gate.md` covers blocker cells + thresholds, benign-pair over-refusal floor, AIR-Bench coverage floor, elicitation-gap ceiling, disclosure-routing rule, judge-disagreement alarm.
- ✅ Every unverified factual claim (benchmark version details, native-judge names, AILuminate grade definitions, CyberSecEval version) marked `<!-- needs-research: ... -->`.
- ✅ Handoff notes cite the mod-108 workstreams the failures feed and the mod-111 interface this matrix satisfies.

## Stretch goals

- **Submit a JailbreakBench leaderboard entry.** Chapter 06 notes JailbreakBench is designed to be a public leaderboard. If your target is one the leaderboard accepts and your run passes their reproducibility gate, submit. The reproducibility bundle is already in your artefacts.
- **Adaptive-attack-survival deep-dive.** For one Tier A cell where the naive ASR is high, retrain the attacker (GCG loss + guardrail; PAIR with guardrail in the loop; Crescendo turn plan against the guardrail decision boundary) and report the survival number. Chapter 07's third derived metric.
- **Cross-target compare.** Run the same matrix (Tier A only) against a second target model (a smaller version of your target family; a competitor model). Report the delta. This is a preview of the mod-111 fleet-scale comparison.
- **Author a diff-report generator.** A script that takes two coverage-matrix YAMLs and produces a delta report: which cells regressed, which improved. This is the mod-111 release-gate primitive.
- **Storage-of-record diagram.** A short diagram of where each artefact class lives (repo, payload store, results warehouse) and who has access. Chapter 06 mentions the discipline; making it concrete for your org is the stretch.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the completions, or the judge rationales — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference matrix. Working payloads live in your org's payload store per chapter 01; see the harmful-payload discipline before starting.
