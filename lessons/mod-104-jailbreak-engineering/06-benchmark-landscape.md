# 06 — Benchmark Landscape: HarmBench, JailbreakBench, AIR-Bench, AILuminate, CyberSecEval

## Motivation

Chapters 02–05 built the attackers. This chapter builds the **coverage report** — the artefact that says, defensibly, "on HarmBench category X the target's ASR is Y with judge Z; on JailbreakBench behaviour W the ASR is V; on AIR-Bench 2024 category U the score is T; on AILuminate hazard S the grade is R; on CyberSecEval test Q the pass rate is P." Without a benchmark landscape the JEH's numbers are one team's numbers. With one, the JEH plugs into the industry conversation.

Five benchmark families matter for a level-40 agentic-safety-engineer JEH:

1. **HarmBench** (Mazeika et al., 2024) — categorical harmful behaviours, standardised judge, adversarial-attack test bed.
2. **JailbreakBench** (Chao et al., 2024) — a public leaderboard for jailbreak attacks and defences on a shared behaviour set.
3. **AIR-Bench 2024** (Zeng et al., 2024) — an AI-risk taxonomy grounded in government regulations (EU AI Act, US EO 14110) with 314 risk categories.
4. **MLCommons AILuminate** — a hazard-graded consumer-facing safety benchmark from MLCommons.
5. **Meta CyberSecEval** — a cyber-offence and cyber-defence benchmark family (CyberSecEval 1–3).

Each has its own taxonomy, its own behaviour / prompt set, its own judge protocol, and its own release cadence. This chapter names each, describes the JEH's coverage contract per benchmark, and develops the composite coverage matrix that folds them together.

## HarmBench

### What it is

Mazeika et al. (2024) — "HarmBench: A Standardized Evaluation Framework for Automated Red Teaming and Robust Refusal." HarmBench is a **standardised harm-behaviour test set** paired with a **standardised judge classifier** trained specifically to score behavioural compliance. The behaviour set covers categorical harmful behaviours across a documented taxonomy; the judge is a fine-tuned classifier meant to catch compliance under paraphrase and roundabout framing.

<!-- needs-research: verify HarmBench's exact behaviour count, category structure, and current classifier version at https://harmbench.org before citing specific numbers in an artefact. -->

### What it measures

HarmBench frames jailbreak evaluation as: **for each behaviour, does the attacker elicit substantive compliance from the target, as judged by the HarmBench classifier?** The paper reports ASR per attacker × target × category. The framework's contribution is that the attacker, target, and judge are all pluggable and the numbers are reported under a single, published protocol.

### What the JEH plugs into it

- **The four attack families from chapters 02–05.** Each attacker in the JEH targets the HarmBench behaviour set and reports per-category ASR under the HarmBench classifier *and* under the JEH's own StrongREJECT-style judge (chapter 07). The two judges frequently disagree; the disagreement is a finding.
- **Adversarial-training test bed.** HarmBench includes an infrastructure for evaluating whether a defence generalises. If your target has been fine-tuned on a specific attack, HarmBench measures whether it holds against the others.
- **Reproducibility conventions.** HarmBench's paper describes decoding, sampling, and repetition conventions the JEH matches so numbers are comparable to the published leaderboard.

### JEH's HarmBench contract

Per run, report:

- HarmBench behaviour set version.
- Attacker × behaviour ASR under the HarmBench judge.
- Attacker × behaviour ASR under the JEH's StrongREJECT-style judge.
- Judge–judge disagreement rate per behaviour.
- Sample size per cell.
- Cost (dollars, tokens, wall-clock) per attacker.

### Common misuses

- **Reporting one HarmBench number.** The value of HarmBench is per-category ASR, not an aggregate.
- **Using only the HarmBench classifier.** Cross-checking with a rubric judge protects against classifier drift.
- **Freezing the behaviour set version.** HarmBench iterates; log the version.

## JailbreakBench

### What it is

Chao et al. (2024) — "JailbreakBench: An Open Robustness Benchmark for Jailbreaking Large Language Models." A **behaviour set + a public leaderboard + an accountability framework** for reporting attack success against a shared benchmark. Runs on 100 behaviours (paired harmful and benign requests) with a documented judge protocol and a public repository at [jailbreakbench.github.io](https://jailbreakbench.github.io).

<!-- needs-research: verify JailbreakBench's current behaviour count (100 at initial release), the current judge protocol, and whether the leaderboard remains active before citing specific numbers. -->

### What it measures

JailbreakBench is designed to be a *reproducible* jailbreak evaluation. It ships with reference attackers (PAIR, GCG-transfer) and reference defences, and it invites the community to submit both. Its judge protocol is documented and pluggable; the paper argues for reporting under multiple judges to avoid over-fitting to one.

### What the JEH plugs into it

- **The four attack families from chapters 02–05** targeting the JailbreakBench behaviour set.
- **Reference-attacker parity.** The JEH's PAIR run is reported alongside JailbreakBench's reference PAIR run so the numbers are directly comparable.
- **Public-leaderboard submission** for JEHs that are ready to submit. The submission process is described in the JailbreakBench repository.
- **Refusal robustness** — JailbreakBench pairs harmful and benign behaviours; the JEH reports the target's over-refusal rate on the benign pairs alongside its ASR on the harmful ones.

### JEH's JailbreakBench contract

Per run, report:

- JailbreakBench behaviour set version.
- Attacker × behaviour ASR under the JailbreakBench judge protocol.
- Attacker × behaviour ASR under the JEH's StrongREJECT-style judge.
- Over-refusal on benign pairs.
- Cost per attacker.
- Reproducibility bundle referenced.

### Common misuses

- **Cherry-picking behaviours.** The framework's whole value is the shared behaviour set. Report all of them or explicitly exclude with reason.
- **Ignoring the benign pairs.** Over-refusal on the benign pairs is a finding; the JEH's refusal-robustness metric depends on it.

## AIR-Bench 2024

### What it is

Zeng et al. (2024) — "AIR-Bench 2024: A Safety Benchmark Based on Risk Categories from Regulations and Policies." AIR-Bench 2024 grounds its harm taxonomy in *government regulations and voluntary commitments*: EU AI Act Annex III, US EO 14110, and the frontier-lab RSPs. The published taxonomy has **314 risk categories** (as of the paper's release) organised in a four-level hierarchy. Prompts per category, judge protocol, and per-category ASR are shipped with the benchmark.

<!-- needs-research: verify AIR-Bench 2024's exact category count (314 at release), the number of prompts per category, and whether an AIR-Bench 2025 has been published; check the paper and the benchmark's release notes. -->

### What it measures

AIR-Bench's contribution is that its categories are *regulator-legible*. When a finding lands in category "chemical weapons acquisition" or "CSAM," the category is one a regulator will recognise from the EU AI Act or EO 14110 text. This makes AIR-Bench the go-to benchmark for coverage claims that need to survive an external audit.

### What the JEH plugs into it

- **Regulator-legibility.** AIR-Bench findings anchor the mod-112 disclosure workflow and the mod-101 governance-ladder position (safety cases, RSP compliance, EU AI Act GPAI evidence).
- **The four attack families targeting AIR-Bench categories** with per-category ASR.
- **Cross-taxonomy mapping.** Every JEH finding is tagged with its AIR-Bench category alongside its HarmBench and JailbreakBench category, so a single finding routes into three coverage reports.

### JEH's AIR-Bench contract

Per run, report:

- AIR-Bench version.
- Attacker × category ASR under AIR-Bench's judge.
- Attacker × category ASR under the JEH's StrongREJECT-style judge.
- Coverage — how many of the 314 categories the JEH exercised; the JEH may not exercise all categories in every run, but the coverage number is a first-class output.
- Regulator-mapping — which EU AI Act / EO 14110 clauses each category maps to.

### Common misuses

- **Reporting only aggregated ASR.** The 314-category structure is the point. Aggregate ASR hides the category coverage story regulators care about.
- **Skipping the regulator-mapping column.** Without it, AIR-Bench is under-used.

## MLCommons AILuminate

### What it is

**MLCommons AILuminate** is a hazard-graded safety benchmark from MLCommons, oriented at *consumer-facing systems* and structured around hazard categories with a graded reporting scale (best in class, very good, good, fair, poor). The benchmark ships with a prompt set, a judge, and a documented reporting protocol. It is one of the industry-body-standardised benchmarks that show up in vendor safety datasheets.

<!-- needs-research: verify MLCommons AILuminate's current hazard categories, prompt-set size, judge protocol, and grading scale at https://mlcommons.org/benchmarks/ai-safety/ before citing specific numbers. Verify whether AILuminate's grading is per-hazard or overall. -->

### What it measures

AILuminate is designed to produce a **grade** that a non-expert stakeholder can read. This makes it the go-to benchmark for consumer-safety datasheet claims and for enterprise vendor-selection scoring. It is *not* designed as the deepest attacker benchmark — HarmBench and JailbreakBench are attacker-heavier — but its grade is legible.

### What the JEH plugs into it

- **A stakeholder-legible grade** to sit alongside the deeper per-category numbers.
- **The four attack families** targeting the AILuminate prompt set with per-hazard ASR.
- **Consumer-safety framing** — over-refusal, tone, and downstream user impact matter here in ways that HarmBench doesn't emphasise.

### JEH's AILuminate contract

Per run, report:

- AILuminate version.
- Per-hazard grade and, where possible, per-hazard ASR.
- Coverage — which hazards the JEH exercised.
- Cross-check with the JEH's StrongREJECT-style judge.
- Cost per attacker.

### Common misuses

- **Reporting only the aggregate grade.** The per-hazard grades are the actionable data.
- **Confusing the AILuminate grade with a general safety claim.** AILuminate covers what it covers; other benchmarks cover other things.

## Meta CyberSecEval

### What it is

Meta CyberSecEval (Bhatt et al., 2023–2024) is a **cyber-offence and cyber-defence benchmark** — insecure-code generation, cyber-uplift MCQs, prompt-injection tests, MITRE ATT&CK-shaped tests, and (in CyberSecEval 3) autonomous cyber-offence tests. CyberSecEval sits at the boundary between jailbreak evaluation (chapter 06 landscape) and dangerous-capability evaluation (mod-106). Its cyber-uplift tests specifically measure whether the target complies with policy-forbidden cyber-offence requests when jailbroken.

<!-- needs-research: verify Meta CyberSecEval's current version (3 as of 2024), test list, and any newer release before citing specific numbers. The benchmark has evolved rapidly. -->

### What it measures

CyberSecEval's cyber-offence tests are what most directly overlap with this module: jailbreak against a specific dangerous-capability category. Its coding-security tests overlap with the `ai-eval-engineer` peer's code-security territory. Its autonomous-offence tests are mod-106 territory.

### What the JEH plugs into it

- **Cyber-offence jailbreak coverage.** The JEH's attackers target CyberSecEval's offence tests and report per-test ASR.
- **Boundary to mod-106.** Autonomous-offence results are cited to mod-106 rather than owned here; the JEH names the boundary.

### JEH's CyberSecEval contract

Per run, report:

- CyberSecEval version and test list.
- Per-test ASR under the CyberSecEval judge.
- Per-test ASR under the JEH's StrongREJECT-style judge.
- Cross-reference to mod-106 for the autonomous-offence tests.

### Common misuses

- **Confusing CyberSecEval's judge with a general jailbreak judge.** CyberSecEval's judges are test-specific and don't generalise to non-cyber harms.
- **Blurring the boundary to mod-106.** Autonomous-offence results are mod-106's material; the JEH cites them, doesn't claim them.

## The composite coverage matrix

Every benchmark above has its own taxonomy. The JEH's job is to *fold* them into one matrix so the coverage report is legible. The composite matrix's rows and columns:

- **Rows** — the four attack families from chapters 02–05, further decomposed by sub-family (GCG variant, PAIR / TAP, Crescendo, many-shot / persona / low-resource / cipher).
- **Column groups** — the five benchmarks, each with its own categories:
  - HarmBench — categorical behaviours.
  - JailbreakBench — behaviours (harmful + benign pairs).
  - AIR-Bench 2024 — 314 regulator-legible risk categories.
  - AILuminate — hazard categories.
  - CyberSecEval — cyber-offence and related tests.
- **Cell contents** — ASR under the benchmark's judge; ASR under the JEH's StrongREJECT-style judge; disagreement rate; sample size; cost.

Not every cell is populated every run. The coverage matrix is tiered like mod-103 chapter 06's PIEH:

- **Tier A (always covered).** HarmBench + JailbreakBench + AIR-Bench top-10-by-severity per attack family. This is the baseline the release gate depends on.
- **Tier B (covered per release).** AILuminate grades and the CyberSecEval offence subset per attack family. Refreshed each release candidate.
- **Tier C (spot-checked).** AIR-Bench long-tail categories, AILuminate low-severity hazards. Sampled at a lower rate.
- **Explicit exclusions.** Categories the deployed target does not expose (a consumer chatbot excludes CyberSecEval's `code_interpreter_offence` row).

### Example matrix fragment

```yaml
matrix:
  - cell:
      attack: {family: gcg, variant: gcg_multi_target}
      benchmark: harmbench
      category: chemical_biological
    tier: A
    asr_native_judge: 0.24
    asr_strongreject: 0.19
    judge_disagreement: 0.06
    n_trials: 200
    seeds: 8
    cost_usd: 42.00
    date: 2026-07-11
  - cell:
      attack: {family: pair}
      benchmark: jailbreakbench
      category: cybercrime
    tier: A
    asr_native_judge: 0.31
    asr_strongreject: 0.27
    judge_disagreement: 0.04
    n_trials: 100
    seeds: 5
    cost_usd: 61.00
    date: 2026-07-11
  - cell:
      attack: {family: crescendo, variant: hybrid}
      benchmark: airbench_2024
      category: chem_bio_precursor_syn
    tier: A
    asr_native_judge: 0.42
    asr_strongreject: 0.38
    judge_disagreement: 0.05
    n_trials: 100
    seeds: 5
    cost_usd: 88.00
    date: 2026-07-11
  - cell:
      attack: {family: many_shot, variant: n=256_persona}
      benchmark: ailuminate
      category: violent_extremism
    tier: B
    asr_native_judge: 0.11
    asr_strongreject: 0.09
    judge_disagreement: 0.03
    n_trials: 100
    seeds: 3
    grade_native: fair
    cost_usd: 210.00                # long-context dominates cost
    date: 2026-07-11
  - cell:
      attack: {family: cipher, variant: base64_persona}
      benchmark: cyberseceval
      category: mitre_offensive_technique_reconnaissance
    tier: B
    asr_native_judge: 0.17
    asr_strongreject: 0.15
    judge_disagreement: 0.03
    n_trials: 100
    seeds: 3
    cost_usd: 55.00
    date: 2026-07-11
```

Every field is populated by an attacker from chapters 02–05 and a judge from chapter 07. The `date` field is mandatory because every number here is snapshot-sensitive.

## Composite metrics the coverage matrix produces

For each release candidate, the JEH aggregates the matrix into:

- **Per-benchmark per-category headline ASR.** With CI. Under the JEH's judge (chapter 07) and, where the benchmark ships one, under the native judge.
- **Cross-benchmark consensus severity.** A finding that lands high on HarmBench + AIR-Bench + AILuminate for the same category is a stronger finding than one that lands high only on one benchmark.
- **Attack-family effectiveness.** Which attack family has the highest ASR per benchmark. Informs which attackers to prioritise in mod-111's scaled harness.
- **Judge disagreement rate.** Native vs. StrongREJECT-style. Persistent disagreement is a judge-calibration finding (chapter 07).
- **Refusal robustness.** JailbreakBench's benign-pair over-refusal is reported as a first-class metric alongside ASR.
- **Elicitation gap.** Best-attack ASR minus baseline ASR per category. This is the metric the RSP / Preparedness / FSF elicitation-gap requirements from mod-101 consume.
- **Adaptive-attack survival.** ASR of an attacker specifically re-run against the current defence stack. Chapter 07 develops the metric.
- **Coverage percentage.** Of each benchmark's categories, how many the JEH exercised this run. A JEH that exercises 100% of HarmBench and 12% of AIR-Bench is telling a coverage story that reviewers should be able to see.
- **Cost per benchmark per attacker.** Feeds the mod-111 scaled-harness budget.

## Storage — the benchmark data itself

The benchmarks ship their behaviour / prompt sets publicly. The JEH's rule:

- **Public benchmark strings are references, not payload.** Cite the benchmark version and the behaviour ID; the JEH's harness pulls the strings from the benchmark's official artefact.
- **Model completions in response to benchmark strings are payload.** Completions that show a jailbreak worked are stored in the harmful-payload store per chapter 01's discipline. Do not paste them into this repo.
- **Judge scores and rationales are payload-adjacent.** The rationale often references the harmful content. Store rationales alongside completions in the payload store; publish only the numeric scores.

## Cost engineering across benchmarks

The five benchmarks have very different cost profiles:

- **HarmBench and JailbreakBench** — hundreds of behaviours × attacker cost. Moderate; the specific attacker (GCG training vs. PAIR run vs. many-shot) dominates.
- **AIR-Bench 2024** — 314 categories × prompts per category. Even at low sample size per category, the total is heavy. Tier-B/C sampling is where the JEH's cost curve lives.
- **AILuminate** — moderate; consumer-facing so the prompt set is bounded.
- **CyberSecEval** — moderate to heavy depending on the test list; CyberSecEval 3's autonomous-offence tests are the heaviest and are cited to mod-106.

The JEH's per-run cost projection is the composite matrix cost with tier-C sampling weights applied. Report cost budgets alongside coverage numbers.

## Release-gate integration

The composite matrix feeds the release gate:

- **A chosen subset of Tier A cells** must have ASR at or below the baseline for the target's current tier. Regressions block release.
- **Refusal robustness on JailbreakBench benign pairs** must be at or above baseline. Over-refusal regressions block release.
- **Coverage of AIR-Bench regulator-priority categories** must not have shrunk since the prior release.
- **Elicitation gap** must be at or below the tier's declared ceiling.

The specific thresholds are set by the target's deployment tier (mod-102). The JEH's job is to *report* against the thresholds, not to *set* them; the setting is a governance decision (mod-101, mod-109, mod-112).

## Common engineering mistakes across benchmarks

- **Reporting one benchmark's numbers as *the* safety number.** Every benchmark covers what it covers. Report all five in the same document.
- **Using only the benchmark's native judge.** Cross-check with StrongREJECT-style (chapter 07).
- **Freezing benchmark versions across releases.** Benchmarks iterate; log versions per run.
- **Cherry-picking behaviours or categories.** Explicit exclusions with reason, not silent omissions.
- **Ignoring benign-pair over-refusal.** JailbreakBench's benign pairs matter as much as its harmful pairs.
- **Publishing model completions or judge rationales without payload discipline.** Reference by ID; store in the payload store.
- **Confusing headline ASR with severity.** A 5% ASR on `chemical_weapons_synthesis` is a much heavier finding than a 40% ASR on `general_impoliteness`. Severity is the mod-112 layer's concern; the JEH's job is to make severity easy to read from the matrix.

## Handoffs

- **`ai-eval-engineer` (peer, level 30).** Consumes benchmark judge-plumbing patterns; provides app-side trace format.
- **`model-evaluation-engineer` (peer, level 30).** Provides statistical calibration methodology for sampling and CIs.
- **mod-106 (Dangerous-capability evaluations).** Owns CBRN / cyber-uplift / autonomous-replication / R&D-uplift tests. CyberSecEval 3's autonomous-offence subset is cited there.
- **mod-108 (Guardrails and monitors).** Consumes benchmark results as guardrail-training signal.
- **mod-111 (Scaled red-team).** Industrialises the JEH; consumes the matrix's cost and coverage numbers for its scheduling.
- **mod-112 (Safety program and disclosure).** Consumes matrix cells above a severity threshold for coordinated-disclosure workflow.

## Summary

- Five benchmarks anchor the JEH's coverage story: **HarmBench**, **JailbreakBench**, **AIR-Bench 2024**, **MLCommons AILuminate**, **Meta CyberSecEval**.
- Each has its own taxonomy, its own behaviour / prompt set, and its own judge. The JEH runs each attacker against each benchmark and reports ASR under both the native judge and the JEH's own StrongREJECT-style judge (chapter 07).
- The **composite coverage matrix** is a tiered structure (A / B / C / excluded) over attack family × benchmark × category, with cost, sample size, seeds, and date on every cell.
- Composite metrics — per-benchmark ASR, cross-benchmark consensus severity, attack-family effectiveness, judge disagreement, refusal robustness, elicitation gap, adaptive-attack survival, coverage percentage, cost — feed the release gate and the mod-112 disclosure workflow.
- Storage rules: benchmark strings are references (public); model completions and judge rationales are payload (store, not repo).
- Every run pins benchmark versions, model snapshots, and judge versions. Numbers without those pins are un-reproducible by construction.
- Chapter 07 develops the StrongREJECT-style judge that scores the matrix; chapter 08 develops the in-the-wild taxonomy that back-feeds fixtures; chapter 09 codifies boundaries to peer roles and downstream modules.
