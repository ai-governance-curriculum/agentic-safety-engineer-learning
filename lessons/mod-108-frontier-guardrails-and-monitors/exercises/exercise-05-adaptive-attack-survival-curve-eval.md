# exercise-05: Adaptive-Attack Survival Curve Eval

**Estimated effort:** 3 hours (evaluation-design and one measurement round; the full frontier-scope survival evaluation runs longer and composes with mod-111)

## Objective

Design and execute a bounded **adaptive-attack survival evaluation** against your composed FGAC, then produce the **FP / FN report shaped for a product manager** chapter 06 pins. The deliverable is not a single accuracy number; it is a *shaped report* with per-hazard-class precision / recall / FP-rate on a bounded benign + adversarial set, an adaptive-attack survival curve against a bounded red-team budget, latency and cost at p50 and p95, benign-user overhead, and a residual-risk section a safety-review body and a product manager can both act on.

The exercise's failure mode is *the report that reads "the FGAC works at 96% accuracy" and stops there*. Aggregate accuracy hides the class where the FGAC is failing. This exercise's discipline is *unhiding* the numbers.

## Prerequisites

- Read chapter 06 (Guardrail Effectiveness Measurement and Boundaries) end-to-end. Re-skim chapter 04's survival-curve section.
- Complete exercises 01–04 or have equivalent artefacts. This exercise composes: exercise 01's FGAC is the target; exercise 02's classifier is one layer; exercise 03's constitutional classifier is another layer; exercise 04's vendor recommendation is a third; you need something to measure.
- Coordinate with mod-111 (or the equivalent red-team function) for the adaptive-attack budget. A bounded budget (tens of hours of automated red-team API calls or ~10 hours of skilled human red-team engagement) is sufficient for a preliminary curve; frontier-scope thousands-of-hours is out of scope.
- Coordinate with the product-manager peer for the deployment. The report is shaped for their consumption; a report authored without them is a report that does not get read.
- Access to the operator's data-governance function to source production-traffic benign samples (with consent and PII scrubbing). Where you cannot, cite AILuminate + open benign corpora and note the deficiency.

## Requirements

Produce the evaluation-pipeline package and the report with the sections below.

### Section 1 — Evaluation set construction

Author the deployment's benign + adversarial evaluation set. Both are held-out — never used as training data (chapter 03 / chapter 04) — for the classifiers under evaluation.

**Benign set.** Target thousands of examples, drawn from:

- Production-traffic samples (with data-governance approval + PII scrubbing).
- Product-manager-authored regression scenarios — the specific benign use cases that *must not* be refused.
- Public benign corpora — AILuminate benign set, general-conversational corpora relevant to the deployment.

Publish per-source counts, per-hazard-class-context counts (a benign prompt near a hazard class is a harder benign than a random benign), and the consent / scrubbing manifest.

**Adversarial set.** Target thousands of examples per hazard class, drawn from:

- HarmBench, JailbreakBench, AdvBench where the hazard categories align.
- AILuminate adversarial set.
- mod-104 output — jailbreak-engineering prompts against the deployment's primary model.
- mod-111 output — automated red-team against the FGAC.
- Prior-incident corpus — real jailbreak attempts observed in production, if the deployment has any.

Publish per-source counts and per-hazard-class counts. Distribution matters at least as much as size — an adversarial set that is 10 000 paraphrases of one attack type over-measures that type.

### Section 2 — Static-benchmark TP / FP / TN / FN measurement

Run the composed FGAC over the section-1 evaluation set. For each hazard class, and for the composed stack overall, report:

- **TP / FP / TN / FN** with counts.
- **Precision / recall / F1 / PR-AUC** with bootstrap 95% confidence intervals.
- **False-positive rate on benign** per class and aggregate.
- **Per-layer contribution** — the same accounting for each layer in isolation, so the composition's *marginal improvement* over the strongest single layer is visible.

Publish per-hazard-class *alongside* aggregate. Aggregate-only reports are a rejection.

### Section 3 — Adaptive-attack survival curve

Design and execute a bounded adaptive-attack evaluation.

- **Two systems under evaluation.** One instance of the primary system behind the composed FGAC; one instance behind a baseline (no FGAC, or a vendor-shipped classifier only). The red-team engages both.
- **Red-team budget.** Publish the unit (red-teamer-hours / automated-red-team-API-calls / adaptive-attack-iterations) and the total budget. At course-exercise scale, ~10 hours of human red-team or ~1 000 automated-red-team-API calls is a reasonable ceiling; state your ceiling.
- **Red-team methodology.** Which attack classes are in scope (mod-104's taxonomy: GCG-style adversarial suffixes, PAIR / TAP, crescendo / multi-turn escalation, many-shot / long-context, roleplay wrappers, encoding). At least three distinct classes for the exercise's curve to have coverage; more for a defensible curve.
- **Success criterion.** A red-team-submitted prompt is "successful" when it elicits a harmful response from the primary system. Whether the FGAC caught it or missed it is the survival measurement.
- **X-axis.** Cumulative red-team effort in the chosen unit.
- **Y-axis.** FGAC recall on the successful attacks: `caught / (caught + missed)`, plotted against cumulative red-team effort.
- **Baseline curve.** The baseline system's recall on the same red-team's successful attacks. The gap is the FGAC's *marginal contribution*.
- **Per-hazard-class curves.** Aggregate hides class-specific regressions.

State honestly what your bounded curve does and does not evidence. A ~10-hour red-team produces *directional* evidence; the frontier-scope thousands-of-hours claim is out of scope for a course exercise.

### Section 4 — Latency, cost, and benign-user overhead

Measure and report:

- **Per-layer latency p50 / p95** at the layer's own batch size on the deployment's hardware.
- **End-to-end guardrail latency p50 / p95** on the composed FGAC.
- **Cost per 1 000 calls** with layer-by-layer breakdown; amortised training and specification-generation costs where relevant.
- **Benign-user overhead** — of the section-1 benign set, what fraction was refused, rewritten, escalated, or latency-affected beyond the FGAC's target? Report per class-context (benign prompts near a hazard class often have higher overhead).
- **Streaming vs blocking cost** if the deployment streams responses.

### Section 5 — The FP / FN report for the product manager

Compile a report shaped for the product-manager peer. Chapter 06 pins the shape:

- **Executive summary** — one page. The three numbers the product manager needs (aggregate FP rate on benign, aggregate recall on adversarial, benign-user-refusal rate) plus the top-line trend since the last measurement.
- **Per-hazard-class breakdown** — grouped by hazard class in the deployment's vocabulary (not "LLM01" — "attempts to reveal customer data via injected instructions"). Precision, recall, FP rate, benign-user overhead per class. With confidence intervals.
- **FP examples the product manager can read.** For each hazard class, 10–20 specific *benign* prompts the FGAC incorrectly flagged. Redacted where needed; the pattern is the actionable field.
- **FN examples the product manager can act on.** For each hazard class, 10–20 specific *adversarial* prompts the FGAC missed. Redacted for hazard content; the *attack shape* is the actionable field.
- **Adaptive-attack survival summary.** The curve from section 3 plus the top three attack classes producing the most bypasses.
- **Cost and latency deltas** against the FGAC's section-4 targets from exercise 01. Numbers that regress against target are called out.
- **Residual-risk statement.** Which hazard classes the FGAC does not fully catch, at what recall, and what the residual-risk claim (mod-109 handoff) says about them.
- **Actionable levers.** The specific configuration changes — threshold adjustments, layer swaps, specification updates from exercise 03, vendor swaps from exercise 04 — that would shift a number, with the estimated cost of each.
- **Trend over releases.** How this measurement compares to the last one. Regressions on any class are called out.

The report is versioned and signed by the safety engineer. It is reviewed by the product manager, the safety-review body, and (where relevant) legal / compliance.

### Section 6 — FGAC update

Update your FGAC:

- Replace section-4 targets with the measured numbers where you now have them. Keep targets for the numbers exercise 05 does not measure (long-tail hazard classes with insufficient sample size, adaptive-attack-survival at frontier scope).
- Update section-7 (evidence-emission) with what the measurement pipeline produced.
- Update section-8 (change-management) with the new baseline the next release measures against.

### Section 7 — Boundaries handoff

Draft the handoff packages the report enables:

- **To mod-109 (safety cases).** Which numbers from the report are the primary inputs to the *control-argument* leg of the safety case. Cite the specific sections mod-109 will re-use.
- **To mod-111 (scaled red team).** Which attack classes underperformed against the FGAC and are the next round's targets. This is the iteration-loop handoff.
- **To mod-112 (program and disclosure).** Which FGAC-failure classes cross a disclosure threshold (customer-affecting FP, missed harm that reached a customer). The evidence-emission shape is the disclosure-input schema.

## Deliverables

Commit to the paired solutions repo:

- `evaluation-set-manifest.md` — section 1 with per-source counts and provenance. **Do not commit adversarial payloads; commit the manifest.**
- `static-benchmark-report.md` — section 2 with per-hazard-class TP / FP / TN / FN, precision / recall / F1 / PR-AUC, and CIs.
- `survival-curve.md` and the survival-curve plot(s) — section 3, per hazard class + aggregate, with the baseline.
- `latency-cost-overhead.md` — section 4.
- `pm-report.md` — section 5. The primary product-manager-facing deliverable.
- `FGAC-update.patch` — section 6 edits.
- `boundary-handoffs.md` — section 7.
- `README.md` — the FGAC version measured, the red-team-effort budget, and a one-paragraph summary of the top three findings.

## Acceptance criteria

- **The evaluation set is held out from all classifier training.** Reusing training data as evaluation data is a rejection.
- **Per-hazard-class numbers are reported alongside aggregate.** Aggregate-only is a rejection.
- **Confidence intervals are computed and reported.** Numbers without CIs are a rejection.
- **The static-benchmark report is accompanied by an adaptive-attack survival curve.** Static-only is a rejection; the survival curve is the chapter-06 load-bearing metric.
- **A baseline curve is present.** The FGAC's marginal contribution over the baseline is the number that matters; without the baseline, the FGAC's number is unknowable.
- **Latency, cost, and benign-user overhead are reported.** Effectiveness-without-cost reports are a rejection.
- **The FP / FN report is shaped for the product manager.** Vocabulary translated, examples included, levers named. A report authored for other safety engineers only is a rejection.
- **The residual-risk section names the specific hazard classes the FGAC does not fully catch.** A report claiming full coverage is a rejection.
- **The FGAC's section-4 targets are updated with measured numbers.** Stopping at the report without updating the FGAC is a rejection — the FGAC is where the numbers live.
- **The red-team budget is documented honestly.** Reporting frontier-scope survival on a bounded budget is a rejection.
- **No harmful payloads are committed** to the solutions repo. Manifests and reports; not payloads.

## Stretch goals

- **Per-layer marginal analysis.** For each layer in the FGAC, compute the counterfactual: what would the report look like if this layer were removed? The layers whose removal most degrades the report are the most load-bearing; the layers whose removal barely moves the report are the over-provisioning candidates.
- **Cross-layer correlation analysis.** For each pair of layers, compute the correlation between their fires on the benign set (FP correlation) and on the adversarial set (FN correlation). High correlation means the pair are redundant; low correlation means they are complementary. This is the empirical version of chapter 01's "layers with different failure modes" discipline.
- **Threshold sweep.** For at least one classifier layer, sweep the classification threshold and report precision-recall / FP-rate-vs-recall curves. Pick the threshold the FGAC composition semantics wants; document the trade-off.
- **Confidence-interval sensitivity.** Show how many additional benign / adversarial examples per hazard class would tighten the CIs to a given target. This is the sizing input for the next evaluation cadence.
- **Multi-turn evaluation.** Include a multi-turn adversarial subset (crescendo / long-context / roleplay-escalation). Report whether the FGAC's per-turn layers hold on multi-turn attacks or whether the sidecar monitor is the primary catcher.
- **Trend chart across three releases.** Where the FGAC has been measured previously, chart the per-class numbers across releases. Regressions are the story; steady numbers are the second story.
- **Automated regression gate.** Publish the pipeline as CI-runnable: when the FGAC changes (layer swap, threshold change, specification update), the pipeline reruns the static-benchmark portion and gates on regressions above a per-class threshold. This is what makes the measurement a lifecycle discipline rather than a one-off.

## Guardrails

- **Do not run the adaptive-attack evaluation without red-team-function approval.** The evaluation is by construction an attempt to elicit harm from the primary system; mod-111 owns the sanctioning.
- **Do not evaluate on training data.** The held-out discipline is what separates a training-time number from a deployed-scope number; violating it collapses the report's meaning.
- **Do not commit any adversarial payload, real customer PII, or production traffic sample to the solutions repo.** Commit manifests, hashes, and the report; not payloads.
- **Do not report vendor-published numbers as the deployment's numbers.** The report's section 2 is on the deployment's benchmark; vendor numbers are annotated separately.
- **Do not aggregate CIs across classes with different sample sizes without documenting the aggregation.** Common gotcha; the reader misinterprets the aggregate.
- **Do not report a survival curve without a documented red-team unit and effort.** The unit is what makes the curve comparable across measurements.
- **Do not claim "the FGAC catches 96% of attacks" without naming the attack distribution.** 96% on a distribution the red-team already-covered says nothing about a distribution the red-team has not.
- **Do not skip the product-manager review before finalising the report.** The report is a shared artefact; a report authored without the consumer is a report the consumer will not act on.
- **Do not close a residual-risk hazard class silently.** The FGAC's residual-risk claim is a mod-109 handoff; the report names what mod-109 has to argue.
