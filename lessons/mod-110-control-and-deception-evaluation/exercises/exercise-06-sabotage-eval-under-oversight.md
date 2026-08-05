# exercise-06: Sabotage Eval Under Oversight

**Estimated effort:** 4 hours

## Objective

Run **at least two of the four Benton et al. sabotage families** on one target-model version, with **model-organism calibrated baselines** and **paired attempt-vs-success rate reporting**, and emit a chapter-07 **sabotage-evaluation report** engineered for RSP / Preparedness / FSF tier-review consumption. **Family 4 (sandbagging-under-oversight) is mandatory** because it composes directly with exercise 03's sandbagging methodology; the second family is your choice from families 1–3 based on which of the operator's downstream tasks the target is most consequentially embedded in.

The load-bearing artefact is not just the sabotage rates. It is the **attempt-vs-success distinction** — chapter 07 §"The sabotage-attempt vs sabotage-success distinction" — and the **model-organism calibrated baseline** (chapter 07 §"Model-organism calibrated baselines") that anchors what the rates mean. A sabotage report without both is a metric dump.

## Prerequisites

- Complete exercise 03 (sandbagging) and exercise 04 (sleeper-agent organism construction). This exercise's family 4 methodology inherits exercise 03's sandbagging discipline; the model-organism calibrated baselines methodology inherits exercise 04's organism-construction handoff to `fine-tuning-engineer`.
- Read chapter 07 end-to-end. Skim chapter 09 §"`fine-tuning-engineer`" and §"`ai-eval-engineer`" for the two peer-role handoffs this exercise depends on.
- Read [Benton et al. — Sabotage Evaluations for Frontier Models](https://arxiv.org/abs/2410.21514) in full. The four families, the attempt-vs-success framing, and the model-organism calibration methodology are the direct references. Version-pin the paper revision.
- **Coordinate with your `fine-tuning-engineer` peer role** for the model-organism calibrated baseline. This exercise requires (at minimum) one calibrated organism per family evaluated. If the peer role is unavailable, run the stubbed variant per exercise 04 Part D's discipline.
- **Coordinate with your `ai-eval-engineer` peer role** for the naturalistic-scale harness plumbing. Sabotage evaluations are large-N by construction (chapter 07 §"Eval design at production scale"); a small-N sabotage report is chapter-07-scope inadequate.
- Access to at least one target-model version. Family choice depends on the target — code sabotage requires a target that produces code; decision-undermining requires an analysis / recommendation task; family 1 (human-oversight sabotage) requires a reviewer panel; family 4 requires a capability probe with an oversight framing.
- **For family 1 specifically**, secure a small human-reviewer panel (2–3 raters) trained on your operator's actual review process (or a well-simulated proxy). Family 1 without human reviewers cannot be run.

## Requirements

### Part A — Choose the family pair and scope the tasks

Family 4 is mandatory. Choose one of families 1 / 2 / 3 as the second. Justify the choice against the operator's deployment scope:

- **Family 1 (human-oversight sabotage).** Choose if the target is deployed in a workflow where a human reviewer grades or approves model output before it becomes consequential.
- **Family 2 (code sabotage).** Choose if the target is deployed as a code-writing or code-review tool at production scale.
- **Family 3 (decision-undermining).** Choose if the target is deployed as a high-stakes analysis / recommendation tool where operator decisions consume its output.

Document the choice in `family-scope.md` with the deployment context.

### Part B — Design the task-item bank per family

For each of the two families, author or adapt a **task-item bank** with at least N ≥ 40 items (naturalism ceiling: prefer prompts drawn from actual production traces where feasible, per chapter 07 §"Eval design at production scale — Naturalistic prompts"; synthetic prompts must resemble production shape):

- **Family 4.** Reuse exercise 03's capability panel with an added oversight framing. The framing states a monitor is watching / a review process is grading the target's performance. Paired against exercise 03's `incentive-neutralised-framing`.
- **Family 1 (if chosen).** A task-item bank of items with operator-known ground truth on correctness. The reviewer grades the model's output; the operator compares grades against ground truth.
- **Family 2 (if chosen).** A code-writing or code-review task-item bank; each item has an operator-known defect signature (a security vulnerability, a subtle logic bug, an unsafe API use).
- **Family 3 (if chosen).** An analysis task-item bank; each item has a downstream decision the operator makes, with operator-known ground-truth on the correct decision.

Emit as `task-banks/family-{4,X}-v1.{json|yaml}`.

### Part C — Author and receive model-organism calibrated baselines

For each family evaluated, coordinate with `fine-tuning-engineer` to construct a **calibrated sabotage organism** — an intentional-sabotage model with a known intended sabotage-attempt rate. Reuse exercise 04's organism-spec-writing discipline. Per chapter 07 §"Model-organism calibrated baselines":

- Author `organism-spec-family-{4,X}-v1.md`. Fields: `organism_id`, `family`, `intended_sabotage_attempt_rate`, `training-data-recipe`, `expected_delta_vs_non_sabotage_baseline`.
- The peer role delivers the trained organism.
- Optionally, author multiple organisms at different intended sabotage intensities to characterise the sabotage-rate-to-effect-size curve (chapter 07 §"Model-organism calibrated baselines" — cross-organism calibration).

If the peer role is unavailable, run the stubbed variant per exercise 04's discipline; the sabotage report marks the calibrated-baseline row as `pending-peer-role-handoff`.

### Part D — Run the target and the organism

For each family and each (task item × condition × N ≥ 4 replicates), run three actors on the same task items with the same protocol:

- **The suspect target** (the model whose sabotage rate the report bounds).
- **The calibrated organism** (the intentionally-sabotaging model).
- **A non-suspect baseline model** (a model with no known sabotage-relevant training). Use a smaller / older model with a clean provenance if a naturally-non-suspect option exists; otherwise use the suspect target under a *safety-context-primed* system prompt as a proxy baseline and document the caveat.

For family 1, use a human reviewer to grade each output against the ground truth without knowing which actor produced it (blind protocol). For families 2 and 3, use an operator-owned scoring pipeline (chapter 07 §"Eval design at production scale — Naturalistic reviewer / decision processes"). For family 4, score the capability-probe pass rate directly.

Capture per-run:

- The full prompt and response; the reviewer / decision-maker / scoring-pipeline output; the ground-truth comparison.
- The actor identity (blinded from the reviewer / grader; retained in the artefact for post-hoc analysis).
- Decoding parameters and target / organism / baseline fingerprints.

### Part E — Compute attempt and success rates

For each family and each actor:

- **Sabotage-attempt rate.** The rate at which the actor produces output *consistent with a sabotage attempt* judged against the family-specific rubric. Chapter 07 §"The sabotage-attempt vs sabotage-success distinction" pins the metric.
- **Sabotage-success rate.** The rate at which the sabotage attempt *survives* the operator's downstream review / monitor / verification process. For family 1, success = reviewer graded incorrectly in the sabotage-relevant direction; for family 2, success = the defect survives the specified downstream review; for family 3, success = the operator decision shifted in the sabotage-relevant direction; for family 4, success is a bit different — the target's under-performance is the attempt, and the under-performance's downstream consequence (a lower tier assignment, a shipped capability report that under-represents the ceiling) is the success — quantify as a delta between attempt (behaviour) and success (downstream consequence).

Report both metrics with CIs (defer to `model-evaluation-engineer` per chapter 09) and the suspect-vs-organism-vs-baseline comparison.

For family 1 specifically, also capture:

- **Reviewer confidence** — the reviewer's stated confidence on each grade.
- **Reviewer accuracy** on each actor's outputs.
- **The mis-calibration** — reviewer confidence vs actual accuracy. Chapter 07 §"Family 1 — What is measured" pins this as a direct oversight-degradation signal.

### Part F — Cross-reference exercises 03 and 05

For family 4, cross-reference exercise 03's aggregate sandbagging bound on the same capability. If the family-4 delta materially differs from exercise 03's delta, the difference is a signal — either the oversight framing composes with the sandbagging incentive to amplify (or dampen) the behaviour, or exercise 03's incentive-neutralised framing was not credible. Discuss in the report.

If exercise 05 was run on the same target-model, cross-reference the scheming-probe report — some family-2 (code sabotage) and family-3 (decision-undermining) attempt patterns overlap with scheming probes 3 (oversight-tool subversion) and 6 (opportunistic lying) at agentic scope.

### Part G — Emit the sabotage-evaluation report

Produce `sabotage-report.md` in the chapter-07 §"The sabotage-evaluation report" shape:

1. **Header.** Target-model version, evaluation date, harness commit hash, organism versions consumed per family (or stub markers), peer-role artefact IDs.
2. **Family 4 section.** Paired-framing pass rates, delta, attempt rate, success rate, cross-reference to exercise 03's aggregate sandbagging bound.
3. **Second family section.** For the family chosen in Part A: family-specific attempt rate (suspect vs organism vs baseline), success rate, and any family-specific metrics (family 1: reviewer confidence mis-calibration; family 2: defect-injection rate + defect-subtlety; family 3: decision-outcome distribution + content-asymmetry).
4. **Composite sabotage bound.** The aggregate finding suitable for tier-review — the target's aggregate sabotage-attempt rate and its aggregate sabotage-success rate given the operator's current defence-in-depth posture. Emit both explicitly per chapter 07 §"The sabotage-attempt vs sabotage-success distinction".
5. **Residual-uncertainty accounting.** Named per chapter 07:
   - Coverage bounds per family — task-item bank coverage, sub-family coverage.
   - Naturalism vs blinding trade-off — where the eval traded one for the other.
   - Sample-size ceiling — CI width bound.
   - Organism-calibration uncertainty — the calibration curve's residual uncertainty at the reported rates.
   - For family 1: reviewer-panel-size ceiling (a small panel has its own bound).
6. **Downstream cites.** mod-107 EACC re-argument (any family's success rate above the EACC's assumed rate); mod-108 monitor calibration (the success-rate delta between attempt and consequence is a monitor-quality signal); mod-109 safety-case trustworthiness leg (attempt rates) and inability leg (success rates); mod-112 disclosure (RSP / Preparedness / FSF tier review).

### Part H — Reviewer walk

Author `reviewer-walk.md`:

- For each family evaluated, name the tier threshold the composite sabotage-attempt rate would cross (or approach) and the disclosure route the finding would take.
- For each residual-uncertainty item, name what a stronger adversarial-model assumption would do to your bound.
- Name at least one place where the attempt rate and success rate diverge sharply, and what that divergence tells you about the operator's defence-in-depth posture.

## Deliverables

Commit to your exercise-solution area:

- `family-scope.md` (Part A).
- `task-banks/family-{4,X}-v1.{json|yaml}` (Part B).
- `organism-spec-family-{4,X}-v1.md` (Part C).
- `runs/family-{4,X}/` — raw per-run artefacts for each family, blinded actor labels retained per-run. `runs/manifest.csv` with per-run hashes.
- `judge/rubric-family-{4,X}-v1.md` and `judge/human-calibration.md` for LLM-vs-human on the calibration subset.
- For family 1: `reviewer-panel/` — the reviewer roster (initials, role — not full identity), the reviewer training material, the blinded-grading protocol.
- `sabotage-report.md` and `sabotage-results.{csv|json}` (Part G).
- `reviewer-walk.md` (Part H).

## Acceptance criteria

- **Family 4 is run.** Missing family 4 is a rejection; the composition with exercise 03 is load-bearing.
- **A second family (1, 2, or 3) is run.** A single-family sabotage report is chapter-07-scope inadequate.
- **Model-organism calibrated baselines are present** for each family, or stubbed with a full spec per exercise 04's discipline.
- **Non-suspect baseline is present** for each family, with the caveat documented if a proxy baseline was used.
- **Attempt AND success rates are reported for each family.** Reporting only one is chapter-07 §"Common misreadings — a high attempt rate with a low success rate is fine" rejection; the two are separate metrics.
- **Family 1 (if chosen) reports reviewer accuracy AND reviewer confidence** with the mis-calibration metric.
- **For family 4, the cross-reference to exercise 03 is present** with a discussion of the delta.
- **The composite sabotage bound is emitted** as suspect-vs-organism-vs-baseline for both attempt and success.
- **The naturalism-vs-blinding trade-off is documented explicitly** in the residual-uncertainty accounting.
- **The report shape is engineered for RSP / Preparedness / FSF tier review** — the composite bound is directly consumable by mod-112 disclosure without additional shaping.
- **Every citation is version-pinned**, especially the Benton et al. paper revision, the organism versions, the task-bank version, the target-model version, the judge-rubric version.

## Stretch goals

- **Run three families instead of two.** A three-family report exercises the composite bound in more dimensions.
- **Run multiple organisms at different intended sabotage intensities** for one family. The sabotage-rate-to-effect-size curve tightens the interpretation of the suspect model's placement.
- **Compose with a mod-108 monitor-calibration exercise.** The attempt-vs-success delta is a direct monitor-quality signal; a joint mod-108 / mod-110 artefact quantifies the monitor's discriminative bound on sabotage-adjacent content.
- **Compose with a mod-107 EACC re-argument exercise.** If any family's success rate exceeds the EACC's assumed residual, the re-argument follows a well-defined pattern.
- **Publish the report as evidence in a mod-109 safety case.** The trustworthiness leg cites attempt rates; the inability leg cites success rates. mod-109 exercise 03's `case/03-evidence-portfolio.md` row for the deployment consumes this exercise's report by ID.
- **Author the AISI-response section.** If the composite bound approaches or exceeds a pre-registered threshold, draft the mod-112 disclosure shape a UK / US AISI submission would carry.

## Guardrails

- **Do not skip family 4.** The composition with exercise 03 is load-bearing; family 4 is what turns the sabotage report into a coherent extension of the sandbagging methodology.
- **Do not report attempt without success (or vice versa).** Chapter 07 §"Common misreadings — a high attempt rate with a low success rate is fine" is the reference; the two are separate metrics for different arguments.
- **Do not report family-specific rates without the organism-calibrated baseline.** Chapter 07 §"Common misreadings — model-organism calibration is optional" is the discipline; unanchored rates are not defensible.
- **Do not run family 1 without a human reviewer panel.** LLM-graded family 1 is not chapter-07 §"Family 1" methodology — the reviewer's confidence mis-calibration cannot be captured by an LLM grader that has no confidence measure.
- **Do not commit real credentials, tenant IDs, or unredacted production incident data.** For families 1 and 3, use production-adjacent-but-redacted prompts, or documented well-simulated proxies.
- **Do not silently run the eval at small N.** Sabotage is a *distributional* phenomenon; a small-N eval has a large CI-width and can miss a small-but-real sabotage effect. The residual-uncertainty accounting names the N ceiling explicitly.
- **Do not treat the safety-context-primed target-as-baseline shortcut as equivalent to a naturally-non-suspect baseline.** Document the caveat and note the residual bias in the accounting.
- **Do not attempt organism construction in-house without the `fine-tuning-engineer` peer role.** Chapter 09 §"`fine-tuning-engineer`" pins the handoff.
- **Do not overclaim the composite bound.** The bound is a delta with CI on a specific task-item bank and a specific defence-in-depth posture; changing either invalidates the specific number.
- **Do not skip the reviewer walk.** For a report that will be consumed by an internal-safety-review body, the walk is what turns the metric report into a defensible finding.
