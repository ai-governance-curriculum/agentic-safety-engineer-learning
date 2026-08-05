# exercise-05: AgentDojo and InjecAgent Coverage Run

**Estimated effort:** 3 hours

## Objective

Ship a **complete AASS coverage report** against **AgentDojo** and **InjecAgent** for one target agent, under the four-posture defensive matrix from chapter 06, with per-cell ASR + utility + false-positive rate, a per-cell judge with per-cell human-agreement, per-cell run manifests, cost / throughput accounting, and the pairwise-posture delta table. This is the exercise that turns the AASS from a chain library into a *shippable* coverage report the peer roles and downstream modules (mod-107, mod-108, mod-111) can consume.

The output is the coverage-report section of your AASS — the artefact that answers "how bad is this deployment across the two public benchmarks, and where does the marginal engineering hour go?"

## Prerequisites

- Read chapter 06 (AgentDojo and InjecAgent — shipping coverage under a defensive-posture matrix) end-to-end.
- Read chapters 01–05 for the taxonomy and per-family metrics the coverage matrix records.
- Read **Debenedetti et al. (2024) AgentDojo** end-to-end — the abstract, methodology, suite definitions, utility-security composite, and reported baselines. Note the observed-on date on the release tag you install.
- Read **Zhan et al. (2024) InjecAgent** end-to-end — the abstract, the direct-harm vs. data-stealing distinction, the false-positive framing, and the reported baselines.
- Read mod-104 chapter 07 for the StrongREJECT-style judge methodology this module inherits and extends into chain-aware judgement.
- Complete exercises 01–04 or have equivalent AASS bench cells to combine with the public benchmarks — the coverage matrix is the *union* of public benchmarks and AASS-internal bench cells (chapter 06 is explicit that AgentDojo + InjecAgent do not cover the whole AASS surface).
- Install the pinned versions of AgentDojo and InjecAgent locally. Record the release tag / commit SHA and the observed-on date; these are load-bearing citations in the report.
- Have a target agent whose model + snapshot, framework version, and tool-bus wiring are all pinned.
- Set up the four defensive-posture harnesses per chapter 06:
  - **Raw** (baseline).
  - **Tool-response sanitisation** with a concrete configuration (spotlighting, structured extraction, instruction-stripping, or untrusted-content quarantine — pick one and pin its version).
  - **Capability gates** with a concrete capability-token issuer, per-tool scope, argument constraints, and expiry policy.
  - **Human-in-the-loop** with a concrete approval UX, a realistic approver-per-hour rate, and a defined timeout policy.
- Have an access-controlled harmful-payload store (per mod-103 chapter 06) for any AASS-internal cells that reference authored payloads.

## Requirements

### Part A — Run AgentDojo across the posture matrix

1. **Choose at least three AgentDojo suites** (workspace, banking, travel, Slack — as offered by the release you pinned). Rationale: single-suite coverage is a story; three-suite coverage lets you show which cell each posture affects differently and lets you report cross-suite deltas.
2. **Run the full attack set × the full user-task set of each chosen suite** at your compute budget. If budget forces subsampling, sample stratified across attack IDs and record the sampling in the run manifest.
3. **Under each of the four postures**, record per-cell:
   - **utility_rate** — fraction of benign tasks completed correctly.
   - **attack_success_rate** — fraction of trials the attack action executed.
   - **judge scaffold ID + version** (AgentDojo's default judge or your composed chain-aware judge) and a per-cell human-agreement number on a random-sampled subset.
   - **run manifest** with target model + snapshot, framework version, AgentDojo version, posture config pointer, and observed-on date.

### Part B — Run InjecAgent across the posture matrix

1. **Run every InjecAgent category** (or the full case list at your budget). If subsampling, stratify across categories and across the direct-harm vs. data-stealing split, and record the sampling.
2. **Under each of the four postures**, record per-cell:
   - **attack_success_rate** split by **direct_harm** and **data_stealing**.
   - **refusal_rate** — fraction of trials the agent refused an injected action.
   - **false_positive_rate** on a paired benign task set the agent refused.
   - **judge scaffold ID + version** and per-cell human-agreement number.
   - **run manifest** as in Part A.

### Part C — Fill in the AASS-internal bench cells the public benchmarks miss

Chapter 06 is explicit that AgentDojo + InjecAgent do not cover: chapter 03 persistence / PoisonedRAG / AgentPoison sub-families in depth, chapter 04 planning-subversion at long step budgets, and chapter 05 worm-shaped propagation. Combine at least the following AASS-internal cells into the same coverage report:

- One row per exercise-02 poisoning attack (memory + RAG / AgentPoison), each carrying its persistence horizon and (if applicable) cross-tenant reach.
- One row per exercise-03 planning-subversion attack, each carrying its first-drift-step distribution and parent-goal completion rate.
- One row per exercise-04 multi-agent attack, each carrying propagation hops reached, unique agents affected, and (for worm-shaped) an R0 estimate.

If you have not completed exercises 02–04, cover the cells with authored placeholder findings and `<!-- needs-research: exercise-XX pending -->` markers so the report's incompleteness is legible.

### Part D — Compose the coverage matrix and the delta table

1. **Assemble the five-dimensional matrix** — attack family × sub-family × benchmark source (AgentDojo suite / InjecAgent category / AASS-internal cell) × defensive posture × target — per chapter 06's `aass_coverage_report` schema.
2. **Compute the pairwise-posture delta table** — for each family and each pair of postures, report `asr_delta` and `utility_delta`. This is the prioritisation currency for the peer roles.
3. **Report the utility-security composite** as a plane rather than a single number: for each posture, plot / tabulate `(attack_success_rate, utility_rate)` for the AgentDojo suites, and `(attack_success_rate, false_positive_rate)` for the InjecAgent categories. Chapter 06 develops the Pareto convention.
4. **Report cost, throughput, and shipping envelope** — total trials, total wall-clock time, total token cost (attack + target + judge), per-cell figure and aggregate, and trials-per-hour under the run's rate-limit envelope. mod-111 consumes these numbers to decide the scaled-harness affordability.

### Part E — Route findings to the peer roles and downstream modules

- Populate the `boundary_routing` block per chapter 07: which findings route to `senior-agentic-ai-engineer` (pattern-level), `ai-infra-security` (tool-runtime), mod-107 (containment), mod-108 (monitors), and mod-111 (candidates for scaled coverage). Every route names a proposed remediation class and a re-measurement plan for the AASS to run after the fix ships.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo):

- `coverage-report/aass_coverage_report.yaml` — the full matrix in chapter 06's schema, with every cell populated (or a `needs-research` marker if intentionally deferred).
- `coverage-report/deltas.yaml` — the pairwise-posture delta table for both `asr` and `utility`.
- `coverage-report/utility_security_plane.md` — the utility-vs-ASR (AgentDojo) and refusal-vs-FPR (InjecAgent) plots or tables, with the Pareto frontier annotated per posture.
- `coverage-report/cost_and_throughput.yaml` — trials, wall-clock, token cost, per-cell and aggregate, plus trials-per-hour.
- `coverage-report/judge/` — every judge scaffold used (AgentDojo default, InjecAgent default, and any chain-aware composed judge for AASS-internal cells), each with a version pin and a per-cell human-agreement measurement (`k`, agreement rate).
- `coverage-report/run_manifests/` — one manifest per posture per benchmark, pinning target model + snapshot, framework version, AgentDojo / InjecAgent release tag + observed-on date, posture config pointer, and sampling policy (if any).
- `coverage-report/boundary_routing.yaml` — routing per finding with proposed remediation class and re-measurement plan (chapter 07's schema).
- `coverage-report/README.md` — top-level summary: target, three-line executive finding, matrix summary table, delta table summary, cost summary, and the three highest-priority remediation routes.
- `harmful-payload-store/manifest.yaml` — payload handles for any AASS-internal cells that reference authored payloads.

## Acceptance criteria

- **At least three AgentDojo suites run under all four postures**, per-cell (utility, ASR, judge, manifest) fields populated.
- **All InjecAgent categories run (or stratified-sampled with sampling recorded) under all four postures**, per-cell (ASR split by direct-harm / data-stealing, refusal-rate, false-positive-rate, judge, manifest) fields populated.
- **AASS-internal cells for chapters 03, 04, 05** covered — at least one row per exercise-02 attack, one per exercise-03 attack, one per exercise-04 attack, or explicit `needs-research` markers if intentionally deferred.
- **Judge scaffold(s) versioned and per-cell human-agreement reported** — never a whole-run agreement number.
- **Pairwise-posture delta table** computed for both ASR and utility, across every family.
- **Utility-security composite reported as a plane** — not a single aggregate number.
- **Cost / throughput accounting reported** — per-cell and aggregate, in a format mod-111 can consume.
- **Boundary-routing block populated** — every finding routes to a specific peer role or downstream module with a proposed remediation class and a re-measurement plan.
- **Every run manifest pins**: target agent version, model + snapshot, framework version, benchmark release tag, and observed-on date.
- **No working payload committed to this repo** — defanged shapes only for AASS-internal cells; public benchmark payloads are handled per the benchmark's own release convention and are not re-hosted here.

## Stretch goals

- **Cross-model comparator.** Re-run the AgentDojo posture matrix on a second target model at the same snapshot cadence. Report per-cell deltas across models. This is one of the highest-value comparators for prioritising defence engineering — the same posture that shifts ASR on model A by 0.4 may shift model B by 0.05.
- **Composed postures.** Add rows for `sanitisation + capability_gates` and `sanitisation + capability_gates + hitl`. Report the composed deltas versus each single posture and versus raw. This is the shape most real deployments actually ship.
- **Adaptive-attacker pass.** Pick the two cells where a posture most reduced raw ASR. Re-author the attack *against* that specific posture (per mod-104 chapter 08's adaptive-attack discipline) and report the adaptive ASR. Adaptive-survival is the load-bearing metric at the mod-108 boundary.
- **Judge cross-check.** Run each cell through both the benchmark's default judge and your chain-aware composed judge. Report per-cell disagreements and investigate any category where the disagreement rate exceeds a threshold you commit to (e.g., 10%).
- **False-positive audit on the deployment's real benign traffic.** If you have access to a sample of the deployment's real benign traffic under the postures, run it and report the operational false-positive rate. AgentDojo and InjecAgent's benign controls approximate this; a real-traffic audit grounds it.
- **Approval-fatigue simulation for HITL.** Model the operator's approval-fatigue budget (mod-102 surface 5) as a rate of approvals-per-hour; run the HITL posture at a realistic rate and report the fatigue-degraded ASR against the laboratory unlimited-approvals ASR.
- **Regression harness.** Freeze the matrix as a regression suite — every model snapshot bump / framework upgrade re-runs it and reports the per-cell delta. mod-111 will consume this harness; treat it as the seed of that hand-off.

## Guardrails

- Do not run AgentDojo or InjecAgent attacks against production frontier APIs beyond the model provider's stated evaluation-safe endpoints and without written authorisation. Local target agents and provider-sanctioned evaluation endpoints are the default.
- Do not re-host public benchmark payloads inside this repo; reference them by benchmark release tag. AASS-internal payloads used to fill cells the public benchmarks miss go to the harmful-payload store, not this repo.
- If your coverage report surfaces a novel high-severity failure against a frontier deployment (e.g., a capability-gate bypass that generalises across postures), the finding is a coordinated-disclosure candidate; route through the mod-112 workflow before publishing anything about it — even before publishing the ASR number.
- Report the observed-on date on every AgentDojo / InjecAgent release citation. Frontier APIs re-tune underneath the same endpoint, and benchmarks iterate; a coverage report without dates is un-reproducible six weeks later.
