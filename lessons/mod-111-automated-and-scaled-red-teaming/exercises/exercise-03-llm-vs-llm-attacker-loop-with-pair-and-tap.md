# exercise-03 — LLM-vs-LLM attacker loop with PAIR and TAP

**Estimated effort:** 3 hours
**Prerequisite chapters:** 02, 03 (helpful: 05, 06; craft-depth prerequisite: mod-104 exercise 02).

## Objective

Stand up a **population-scale** PAIR and TAP run against a chosen target across at least three behaviour categories, driven by a **rate-limit-aware scheduler** in front of the PyRIT orchestrators, and produce a per-cell `(ASR, ASR 95% CI, diversity, cost)` report that populates the LLM-vs-LLM slice of your program's Coverage Matrix Contract (chapter 06). The exercise is deliberately *not* a re-run of mod-104 exercise 02's craft-depth PAIR / TAP work — the deliverable here is the *scaling* layer: parallel cells under a shared cost budget, provider rate limits treated as the outer constraint, and diversity reported alongside ASR as first-class metrics.

## Problem statement

Pick a target (a frontier chat model, an open-weights instruction-tuned model, or a mod-105-shaped agent scaffold). Pick **3–5 seed behaviours** drawn from at least **three distinct behaviour categories** — the mapped taxonomy cells your program cares about (HarmBench / AIR-Bench / AILuminate axes are the reference; chapter 06 owns the mapping). Do not concentrate the seeds in one category; a cell grid that spans only one behaviour class produces a report that says nothing about coverage.

For each `(behaviour, technique ∈ {PAIR, TAP})` cell, run the PyRIT orchestrator against the target with **N ≥ 20 seeds per cell**, under a **per-cell dollar budget**, a **per-cell query budget**, and a **per-cell wall-clock budget** enforced in code. Wrap the orchestrators in a **rate-limit-aware scheduler** — a token-bucket per provider key with a fairness policy across concurrent cells — so provider RPM / TPM limits are the outer constraint and inner parallelism is shaped to fit them. Score every attempt under a chapter-05 StrongREJECT-shape judge (PyRIT's default `SelfAskLikertScorer` is replaced; chapter 03 is explicit).

Report per cell: **ASR with 95% CI**, **diversity** (semantic-distance clustering on the successful-attack corpus, plus at least one secondary metric), and **cost** (dollars, tokens, wall-clock per successful attack). Compare PAIR vs TAP per behaviour on all three axes; chapter 03 warns that TAP's higher parallelism can *lower* diversity if the branching factor is not tuned — measure the effect and report it, do not assume it. Payload discipline (chapter 06; mod-104 style) is not optional.

## Requirements

Produce four artefacts.

### Artefact A — `cmc-<program>-pair-tap-cells.yaml`

The cell inventory for this exercise's slice of the CMC. One entry per `(technique, behaviour, model, decoding, [branching_factor, depth])` cell. Each entry has:

- `cell_id` — stable `PT-<technique>-<behaviour>-<n>` identifier resolved by the payload manifest.
- `technique` — `PAIR` or `TAP`.
- `behaviour` — the mapped taxonomy cell (be explicit about the source benchmark row: HarmBench / AIR-Bench / AILuminate).
- `behaviour_category` — the parent category; used to prove the ≥ 3-category spread.
- `target` — model identifier + snapshot / rev; agent-scaffold reference if the target is a mod-105 scaffold.
- `attacker_model` — the general-purpose instruction-following LLM in the attacker slot (chapter 03; a fine-tuned attacker is chapter 04's craft and is out of scope here).
- `judge_model` — the chapter-05 StrongREJECT-shape judge. Version-pinned.
- `decoding` — temperature, top-p, max-tokens for each of `{attacker, target, judge}`.
- `seeds` — N ≥ 20 seed-prompt IDs, resolved by the payload manifest. **No seed text in this file.**
- `budgets` — `budget_queries_per_seed`, `budget_queries_per_cell`, `budget_dollars_per_cell`, `budget_wallclock_per_cell`.
- `pair_config` — for PAIR cells: `queries_per_seed` (chapter 03's twenty-query baseline is the reference), `max_refusal_run` termination threshold.
- `tap_config` — for TAP cells: `branching_factor k`, `depth d`, `pruning_threshold` (the critic score below which a branch is pruned). Chapter 03 is explicit that `(k, d)` shape the cell; a defensible CMC pins a small grid, does not sweep.
- `severity` — mod-112 severity annotation for the underlying behaviour.

Every cell **must** appear in both a `technique=PAIR` and a `technique=TAP` row for the PAIR-vs-TAP comparison to be defensible.

### Artefact B — `cmc-<program>-pair-tap-report.yaml`

The per-cell metrics report. One entry per cell. Each entry has:

- `asr` — successful attacks / total seeds attempted in the cell.
- `asr_ci_95` — `[lower, upper]`. Wilson or bootstrap; name which. A cell report with a scalar ASR and no CI is rejected — chapter 03's "ASR alone is misleading" thesis starts with the CI, not the diversity.
- `diversity` — **required**. A cell report that omits diversity is rejected. Populate:
    - `unique_clusters_at_threshold` — number of clusters in the successful-attack corpus under embedding-distance clustering at a named threshold. Name the embedding model *and* the threshold; both are methodology commitments.
    - `cluster_size_distribution` — histogram; a single fat cluster is a *repetition finding* not a coverage finding.
    - `secondary_metric` — one of `{distinct_ngram_ratio, judge_facet_coverage}` from chapter 03's shortlist. Which one, and why.
- `cost` — `dollars_per_cell`, `dollars_per_successful_attack`, `tokens_per_cell` (broken down `{attacker, target, judge}`), `wallclock_per_cell`, `wallclock_per_successful_attack`.
- `budget_exhaustion` — per cell, how many seeds ended in `success | unbroken_with_budget | undetermined_budget_exhausted`. Chapter 03 requires the distinction: *unbroken with budget* is a finding; *undetermined because budget exhausted* is a gap.
- `pair_vs_tap_delta` — per behaviour, the paired `(ASR, diversity, cost_per_success)` deltas. This is the load-bearing comparison; chapter 03's warning about TAP branching collapsing diversity is verified or refuted here.
- `rate_limit_events` — per cell, counts of `throttled | retried | dropped` events from the scheduler layer. A cell that spent 40 % of its wall-clock in retry is a cell whose cost accounting is dominated by scheduler behaviour, not by the attacker loop; the report surfaces this.
- `scheduler_fairness` — the observed per-cell share of the shared token-bucket. Chapter 03 pins fairness across concurrent cells as an outer-constraint property; the metric evidences that the scheduler behaved.

### Artefact C — `cmc-<program>-pair-tap-transcript-manifest.yaml`

The payload-store manifest for every attacker / target / judge transcript. One entry per attempt:

- `transcript_id`, `cell_id`, `seed_id`, `iteration_index` (PAIR) or `branch_path` (TAP), `sha256`.
- `storage_location` — external store URI.
- `matrix_cell` — chapter-06 cell reference.
- `verdict` — `success | unbroken_with_budget | undetermined_budget_exhausted`.
- `judge_score` — the numeric StrongREJECT-shape score; **judge rationale text is not in the committed file** — the rationale lives in the store and is referenced by hash.
- `cluster_id` — assignment under the diversity clustering, so the successful-attack corpus can be re-clustered without re-embedding.
- `severity` — mod-112 severity.
- `access` — ACL / group / role for retrieval.

**No attacker prompt text, no target completion text, and no judge rationale text in any committed file.** Chapter 06 payload discipline: manifests only.

### Artefact D — `cmc-<program>-pair-tap-runbook.md`

A short (**800–1200 words**) runbook covering:

- **Cell-selection rationale.** Why these 3–5 behaviours, why these categories, and how the choice avoids concentrating on the category you already suspect the target is soft on. Chapter 06's coverage claim is what the choice serves.
- **Scheduler architecture.** The token-bucket-per-key design, the fairness policy across cells, and where it sits relative to the PyRIT orchestrator. Chapter 03 pins rate-limit-aware scheduling as the outer constraint and hands the infra to the `ai-eval-engineer` peer role — name that handoff explicitly and say what your local scaffold is doing until that peer's infra ships.
- **Budget enforcement.** How dollar / query / wall-clock budgets are enforced in code (not by convention), and what the cell-termination report looks like. The `unbroken_with_budget` vs `undetermined_budget_exhausted` distinction is chapter 03's; walk through one worked example from your run.
- **PAIR vs TAP interpretation.** For each behaviour, which technique won on (ASR, diversity, cost) and why. Chapter 03's warning about TAP branching collapsing diversity if `(k, d)` are not tuned is the question — did your TAP cells show higher, similar, or lower diversity than PAIR at comparable cost? Report the number, not the expectation.
- **The "ASR alone is misleading" thesis, backed by your own diversity number.** This is the load-bearing paragraph. Take one cell where the ASR looks impressive and show what its diversity number says about the actual attack surface evidenced. If the ASR is 60 % but the diversity is a single fat cluster, that is a *repetition finding*; if the ASR is 25 % across twelve clusters, that is a *coverage finding*. Use your cell's actual numbers.
- **Cost accounting.** Dollars per successful attack per cell; PAIR-vs-TAP cost delta; the fraction of the dollar total consumed by the judge model. Chapter 03 flags cost-per-success as the metric that eventually drives the decision to fine-tune the attacker (chapter 04); this section sets the baseline that decision will be measured against.
- **Judge choice and drift check.** The chapter-05 StrongREJECT-shape judge, its version pin, and any drift or calibration sanity check you ran during this exercise. Chapter 03 is explicit that a miscalibrated judge produces a devastating scaling failure — a scaled loop with a broken judge is worse than no loop.
- **Handoffs.**
    - Rate-limit-aware scheduler infrastructure — chapter 03 routes this to the `ai-eval-engineer` peer (level 30). Name the interface (token-bucket API, fairness policy config) your local scaffold is stubbing until that peer's infra lands.
    - Guardrail-effectiveness workstream — chapter 03 / mod-108 rows; the same cells run with guardrails on and off are the guardrail delta. Note whether this exercise's cells are the guardrails-off row of a future delta.
    - CMC section 3 (orchestrator inventory) and CMC section 6 (reproducibility — cost + rate-limit reproducibility). Name the fields your report populates.
- **Threats to validity.** Provider rate-limit variability inflating wall-clock cost; seed underrun (N too small for a tight CI); embedding-model / clustering-threshold choice biasing the diversity number; judge drift across a long run; target-snapshot drift mid-run; PAIR's twenty-query budget being generous or stingy for your target.

## Starter guidance

- **This exercise is scale, not craft.** Mod-104 exercise 02 owns the single-target PAIR / TAP craft depth. Do not re-litigate the loop mechanics here; the deliverable is the parallel-cell, budget-aware, diversity-scored *program layer* on top.
- **Use PyRIT's shipped orchestrators.** `PAIROrchestrator` and `TreeOfAttackOrchestrator` are the reference implementations chapter 03 names. The engineering work is composition — choosing the `(attacker, target, judge)` triple, wiring budgets, wrapping in the scheduler — not re-implementing the loops. <!-- needs-research: confirm the current PyRIT release's orchestrator class names and their import paths against the pinned release; Microsoft has moved the path across releases. -->
- **Wrap PyRIT in an Inspect task.** Chapter 02's composition rule is unchanged: PyRIT is the solver, Inspect is the runner and log store. Your rate-limit-aware scheduler sits *between* the Inspect task and the provider calls.
- **Pin the diversity methodology before you run.** The embedding model, the clustering algorithm, and the threshold are all methodology commitments; changing them post-hoc invalidates the number. Name them in Artefact B's `diversity` block; document them in the runbook.
- **A single scalar ASR is a rejected report.** Chapter 03 is explicit: `(ASR, ASR CI, diversity)` is the tuple. A cell entry with a scalar ASR fails acceptance.
- **Budget exhaustion is a first-class outcome.** Distinguish `unbroken_with_budget` (the seed was tried to budget and did not break) from `undetermined_budget_exhausted` (the budget ran out mid-loop; the seed's status is unknown). Chapter 03 requires the distinction.
- **TAP's `(k, d)` is a small pinned grid, not a sweep.** Chapter 03 is explicit; sweeping `(k, d)` for one behaviour eats the dollar budget and produces a report that says nothing about the other behaviours.
- **The rate-limit-aware scheduler is the *outer* constraint.** PyRIT's inner parallelism must respect the token-bucket, not the other way around. A scheduler that lets an inner-parallel orchestrator burst past the provider RPM produces throttled retries that inflate the wall-clock budget and can silently exhaust the dollar budget on retry cost.
- **Fairness across concurrent cells matters.** If one cell hogs the token-bucket, the report's per-cell wall-clock is misleading and the PAIR-vs-TAP cost comparison is invalid. Log per-cell bucket share and report it.
- **Judge the target's completion, not the attacker's rewrite.** PyRIT's defaults are correct here; documenting the wiring in the runbook heads off the common mistake at review time.
- **Payload discipline (chapter 06) is not optional.** Working PAIR / TAP attacker prompts against a named target and judge rationale text quoting harmful content are payload-store artefacts. The committed files are manifests referencing hashes. Mod-104's exercise 02 discipline transfers verbatim; this exercise's scale multiplies the surface area, not the discipline.
- **Cross-tag findings against chapter-05 families and mod-108 guardrail rows.** A successful attack that composed with a low-resource-language wrapper is a `PAIR × low-resource` finding for the CMC; the composition tag is what mod-108's guardrail routing needs.
- **`needs-research` mark every unverified factual claim.** Framework class names, PyRIT release paths, published ASR numbers, embedding-model recommendations — none of these should sit unmarked in your artefacts.

## Acceptance criteria

- ✅ `cmc-<program>-pair-tap-cells.yaml` covers 3–5 behaviours across ≥ 3 behaviour categories, with **both a PAIR and a TAP cell per behaviour**, N ≥ 20 seeds per cell, and per-cell dollar / query / wall-clock budgets pinned. Seeds referenced by ID only; **no seed text in the file**.
- ✅ `cmc-<program>-pair-tap-report.yaml` reports `asr`, `asr_ci_95`, `diversity` (with named embedding model, named clustering threshold, cluster-size distribution, and a secondary metric), and `cost` (dollars / tokens / wall-clock, absolute *and* per successful attack) for every cell. A report entry without a diversity block is a rejected entry — chapter 03's diversity-is-load-bearing rule.
- ✅ `budget_exhaustion` distinguishes `success | unbroken_with_budget | undetermined_budget_exhausted` per cell.
- ✅ `pair_vs_tap_delta` populated per behaviour on all three axes (ASR, diversity, cost per success). The chapter-03 warning about TAP branching collapsing diversity is answered with a measured number, not an assumption.
- ✅ `rate_limit_events` and `scheduler_fairness` populated per cell, evidencing that the rate-limit-aware scheduler behaved as the outer constraint.
- ✅ `cmc-<program>-pair-tap-transcript-manifest.yaml` lists every attempt (both successful and unsuccessful, both PAIR iterations and TAP branch paths) with ID, sha256, storage URI, `verdict`, `cluster_id`, `severity`, and access group. **No attacker prompt text, no target completion text, and no judge rationale text in any committed file.**
- ✅ `cmc-<program>-pair-tap-runbook.md` (800–1200 words) covers cell-selection rationale, scheduler architecture, budget enforcement, PAIR-vs-TAP interpretation, the "ASR alone is misleading" paragraph backed by the learner's own diversity number, cost accounting, judge choice and drift check, explicit handoffs (rate-limit scheduler → `ai-eval-engineer` peer; guardrail delta → mod-108; CMC sections 3 and 6), and threats to validity.
- ✅ ASR figures scored under a chapter-05 StrongREJECT-shape judge; PyRIT's default `SelfAskLikertScorer` is not used. Judge model and version pinned in Artefact A.
- ✅ Every unverified factual claim (PyRIT class / import paths, published PAIR / TAP ASR numbers, embedding-model recommendations, framework release specifics) marked `<!-- needs-research: ... -->`.
- ✅ At least one successful attack per behaviour is annotated with a mod-112 severity in the transcript manifest.

## Stretch goals

- **Add a `guardrails=on` companion row for one behaviour.** Re-run the same cell with a mod-108 guardrail in front of the target; the ASR / diversity / cost delta populates the guardrail-effectiveness row and pre-seeds the mod-108 workstream this exercise routes to.
- **Sweep TAP `(k, d)` for one behaviour under a fixed dollar budget.** Chapter 03 warns against sweeping `(k, d)` broadly; a single-behaviour sweep under a capped budget produces the tuning curve the CMC section-3 owner needs to pin the grid.
- **Replace the general-purpose attacker with a smaller open-weights instruction-tuned attacker for one cell.** Chapter 03 flags cost-per-success as the metric that drives the decision to fine-tune the attacker (chapter 04); a smaller-attacker cost baseline is what a later fine-tuned-attacker cell will be measured against.
- **Author a fairness monitor for the rate-limit scheduler as a standalone diagnostic.** Per-cell bucket-share time-series plus a fairness score (Jain's index or equivalent); this becomes the `ai-eval-engineer` peer's interface acceptance test when their infra lands.
- **Ship a `PAIR × mod-105 agent-scaffold target` cell.** Chapter 03's target slot accepts an agent scaffold; running PAIR against a scaffold instead of a chat model exposes the multi-turn tool-call attack surface mod-105 owns and cross-tags the finding.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the transcripts, or the working attacker / target / judge prompts, or the judge rationales — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference cell inventory and scheduler scaffold. Working payloads live in your org's payload store per chapter 06; see the harmful-payload discipline before starting.
