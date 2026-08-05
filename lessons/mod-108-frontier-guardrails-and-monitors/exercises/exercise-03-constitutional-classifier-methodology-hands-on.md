# exercise-03: Constitutional Classifier Methodology Hands-On

**Estimated effort:** 3 hours (specification authoring, synthetic-corpus generation config, one training + evaluation round; iterating the specification through multiple rounds is a stretch)

## Objective

Apply Anthropic's **Constitutional Classifiers** methodology (Sharma et al., 2025) to one hazard class in your FGAC — the frontier-scope extension of exercise 02's fine-tune loop. Author a **specification** (the "constitution"), generate synthetic training data from it, fine-tune a lightweight classifier, evaluate it against a **preliminary adaptive-attack survival curve** on a bounded red-team budget, and iterate the specification at least once to close a visible failure.

The load-bearing artefact is the *specification* — the natural-language document that pins what the classifier must catch, what it must not catch, and the specific distinctions it must draw. A defensible submission ships specification v0, specification v1 (the iteration), and the survival-curve delta between the two. If your report is only the trained classifier, you have executed chapter 03; the constitutional discipline lives in the specification-driven training-data generation and the survival-curve-driven iteration.

## Prerequisites

- Read chapter 04 (Constitutional Classifiers Methodology) end-to-end. Read Sharma et al. (2025) — the [Anthropic research page](https://www.anthropic.com/research/constitutional-classifiers) is the starting point; pin the arXiv identifier and the current supplement.
- Complete exercise 02 (or an equivalent chapter-03 fine-tune loop) — this exercise extends that pipeline with the specification-driven generator and the survival-curve evaluation. It does not replace the pipeline.
- Coordinate with mod-111 (or the equivalent red-team function) for the adaptive-attack evaluation budget. A bounded red-team session (10s of hours, or a bounded automated-red-team-API budget) is enough to produce a preliminary curve; the full thousands-of-hours is out of scope for a course exercise.
- Access to a capable synthetic-data-generating LM (a large frontier model or an operator-hosted open model at ≥70B scale). Confirm the generator's terms of service admit safety-classifier training-data generation before running.
- Access to a small base classifier model (2B–9B) — the same base you used in exercise 02 is fine.

## Requirements

Produce the specification-driven pipeline with the sections below. The specification and the iteration are the constitutional discipline; the training and evaluation compose on top of exercise 02's pipeline.

### Section 1 — Scope decision

Pick **one hazard class** from your FGAC's section-1 taxonomy to constitutionalise for this exercise. The class must:

- Have both benign edge cases and adversarial-harmful cases (a class where the disambiguation is trivial does not exercise the methodology).
- Sit within an FGAC layer 2 or layer 4 slot the classifier can fill.
- Be scoped narrowly enough that a 3-hour specification-authoring pass can enumerate the positive, negative, and disambiguation rules.

Typical strong candidates: "prompts eliciting instructions for [narrowly-scoped hazard]", "prompts extracting system-prompt content via reformulation", "prompts eliciting personal-data disclosure about specific individuals via retrieved-document paths". Publish the scope decision and the reasoning.

### Section 2 — Specification (constitution) v0

Author the specification with the three parts chapter 04 pins. The specification is a natural-language document, not a schema; the generator's ability to use it is the quality signal.

**Positive rules — what the classifier must catch.** Enumerate at least five sub-cases for the hazard class, each with:

- A one-sentence rule statement.
- Two or three worked examples.
- Two or three "adversarial-obfuscation" variants (encoding, roleplay wrapper, indirect-question form, code-comment form).

**Negative rules — what the classifier must *not* catch.** Enumerate at least five adversarial-benign sub-cases — prompts that look like they might elicit the hazard but that the negative rules require the classifier to admit. Same shape: rule statement + worked examples + variants. This section is where safety engineers most commonly under-invest; commit to at least parity with the positive-rule count.

**Disambiguation rules — the specific distinctions the classifier must draw.** Enumerate at least five pairwise contrasts, each with:

- The two prompts (benign side and harmful side).
- The specific feature that distinguishes them (target-of-instruction specificity, presence of protective framing, actor/intent, request scope).
- A one-sentence rule the classifier and the human labeller should apply.

Version-tag the specification (`spec-v0`) and pin the commit hash.

### Section 3 — Synthetic-corpus generation from the specification

Prompt the generating LM with the specification (as the *entire* system prompt, or with a per-generation-template header) and produce:

- **Adversarial-harmful examples.** Target several thousand, distributed across the positive-rule sub-cases and the adversarial-obfuscation variants. Diversity of phrasing, encoding, and framing is the pass criterion — not raw count.
- **Adversarial-benign examples.** Target several thousand, distributed across the negative-rule sub-cases. The generator's job here is *harder* — it must produce prompts that a naively-trained classifier would over-flag but that the specification requires to pass. Track the generator's cooperation rate.
- **Clearly benign examples.** From a separate benign generator or a benign corpus, mixed in to balance the corpus.
- **Multi-turn examples.** At least one hundred multi-turn trajectories where individual turns look benign but the final turn reaches the hazard. Chapter 04 pins multi-turn as a required class; the generator has to produce it.

Apply the filtering-and-dedup pass chapter 04 pins:

- Canonicalised-text-hash and near-neighbour-embedding-distance de-duplication.
- Schema filtering — drop off-schema outputs, not silently accept.
- Judge re-labelling on a sample; disagreement between generator and judge is a debugging signal.
- Human sample-review on a smaller slice (~ 5% of corpus) using the exercise-02 labelling handbook; this is the ground-truth anchor.

Publish per-source counts, cost of the generation pass (per-token cost × corpus size), and the judge / human sample-review results.

### Section 4 — Classifier fine-tune

Run the fine-tune on the synthetic corpus + any exercise-02 red-team / public-benchmark corpus for the same hazard class. Same discipline as exercise 02 section 5. Publish base-model choice, hyperparameters, cost, and the version-pinned classifier checkpoint (`classifier-spec-v0`).

Where you can, run *two* variants: one trained on the constitutional corpus alone, one trained on the constitutional corpus + exercise-02's non-constitutional corpus. Reporting both isolates the methodology's contribution.

### Section 5 — Preliminary adaptive-attack survival curve

Chapter 04 pins this as the load-bearing metric. Preliminary curves at course-exercise scale (10s of hours, bounded automated-red-team budget) are the deliverable; the frontier-scope thousands-of-hours curve is out of scope but the *shape* of the report matches.

- **X-axis.** Red-team effort, in your documented unit (red-teamer-minutes, automated-red-team-API-calls, adaptive-attack-iterations). Include both human and automated volumes and keep them separable.
- **Y-axis.** Classifier recall on *successful attacks against the primary system* discovered by the red-team. A red-team-submitted prompt that the primary system refuses is not a data point on this curve; a prompt the primary system helps with, and that the classifier under evaluation caught or missed, is.
- **Baseline curve.** The primary system without this classifier (or with a chapter-02 baseline classifier). The gap between the constitutional-classifier curve and the baseline curve is the marginal contribution.
- **Per-sub-case breakdown.** For your hazard class's positive-rule sub-cases, report per-sub-case survival. Aggregate hides sub-case regressions.
- **Preliminary interpretation.** With 10s of hours of red-team you cannot claim the frontier-scope survival; you can claim *directional evidence*. State this honestly.

### Section 6 — Failure categorisation

Categorise the red-team's *successful bypasses* — the prompts that produced a harmful response and passed the classifier:

- **Which positive-rule sub-case did the bypass belong to?** If a category the specification enumerates, the specification is under-precise for it.
- **Was the bypass an *unenumerated adversarial-obfuscation variant*?** If yes, the generation pass under-covered that variant class.
- **Was the bypass an *out-of-specification hazard-adjacent request*?** If yes, the specification's scope may be under-drawn.

For each bypass class, propose the specific specification edit (new positive-rule sub-case, new negative-rule sub-case to guard the boundary, new disambiguation rule) or the specific corpus edit.

### Section 7 — Specification (constitution) v1 and re-training

Ship specification v1 with the edits section 6 produced. Re-run sections 3 and 4 (synthetic corpus regen + classifier fine-tune) to produce `classifier-spec-v1`. Re-run section 5 on the same red-team budget (or a fresh one from the same red-team source) to produce the v1 survival curve.

- **v0 vs v1 survival delta.** Per hazard sub-case and aggregate. Publish the delta plot.
- **Regression check.** Any sub-case where v1 regresses relative to v0 is a specification-editing bug. Debug; do not ship a regressed classifier.
- **FP-rate delta.** The specification changes may have raised the false-positive rate; check on the benign set.

### Section 8 — FGAC section-4 fields for the constitutional classifier

Publish the classifier's contribution to your FGAC's section-4 fields: precision / recall / FP rate per hazard sub-case, latency p50 / p95 at the section-1 placement, cost per 1 000 calls, calibration (ECE + reliability), and the preliminary survival curve. Note the specification version pin — the FGAC's section-8 change-management contract signs on specification versions, not just classifier versions.

## Deliverables

Commit to the paired solutions repo:

- `spec-v0.md` and `spec-v1.md` — the specifications with commit hashes.
- `generation-config.md` — the generator's prompt template, corpus sizing, filtering config, generation cost.
- `corpus-manifest.md` — per-source counts, per-sub-case counts, judge and human-review results. **Do not commit harmful payloads.**
- `training-run-v0.md` and `training-run-v1.md` — the fine-tune configurations and results.
- `survival-curve-v0.md` and `survival-curve-v1.md` — the preliminary survival curves and the delta plot.
- `failure-categorisation.md` — section 6.
- `evaluation-report.md` — section 8 with FGAC section-4 fields for both classifier versions.
- `README.md` in the exercise directory — the hazard-class scope, a one-paragraph summary of the v0 → v1 delta, and the specific specification edits that produced the delta.

## Acceptance criteria

- **The specification is authored before the training-data corpus.** Corpus-first, specification-second is a rejection — it collapses the methodology into a rebranded chapter-03 loop.
- **Positive, negative, and disambiguation rules are all present.** A specification with a rich positive-rule set and a thin negative-rule set is a rejection; the negative rules are as load-bearing.
- **The generator produced adversarial-benign examples at parity with adversarial-harmful.** A corpus without the adversarial-benign class does not exercise the discipline.
- **Multi-turn examples are present in the training corpus.** Single-turn-only is a rejection.
- **The synthetic corpus was filtered, de-duplicated, judge-relabelled on a sample, and human-reviewed on a smaller sample.** Skipping any of the four is a finding.
- **A preliminary survival curve is produced with a documented red-team unit and effort.** Static F1 alone is a rejection.
- **A baseline curve is present.** The marginal contribution of the constitutional classifier over the baseline is the number that matters.
- **The specification was iterated at least once.** v0 → v1 with a documented failure-categorisation-and-edit trace is the constitutional discipline.
- **The v1 survival curve reports its delta against v0 per sub-case.** Aggregate-only is a finding; sub-case regressions have to be visible.
- **No harmful payloads are committed to the solutions repo.** Manifests and version pins only.

## Stretch goals

- **Iterate three or more spec versions.** Show that the survival curve continues to shift on new sub-cases as the red-team engages fresh categories.
- **Two-sided constitutional stack.** Author a separate specification for the output side of the same hazard class and train a paired output-side classifier. Compare (input-only) vs (output-only) vs (both) composition semantics on the same red-team.
- **Cross-hazard specification test.** After shipping v1 for one hazard class, run the classifier against a *different* hazard class's adversarial set. A classifier that generalises well was over-specified; a classifier that generalises poorly is doing what the specification asked. Report and interpret.
- **Specification-agreement study.** Give the specification to two humans and have them label the same held-out slice. Report inter-labeller kappa; disagreement above a threshold is a specification-editing signal. This composes with exercise 02 section 8.
- **Automated-red-team volume.** Run a mod-111-style automated red-team harness for a bounded API budget against `classifier-spec-v0` and produce a higher-volume version of the section-5 curve. Compare shape against the human-red-team curve.
- **Compare against Anthropic's paper.** For the specific hazard class you chose, contrast the shape (not the absolute numbers) of your preliminary curve against the paper's reported curve. Are you seeing the same qualitative gap-vs-baseline? If not, hypothesise why (scope narrower / broader, base-model weaker, red-team methodology different).

## Guardrails

- **Do not use the specification-driven generator to elicit uplift outside the classifier-training context.** The generator produces *prompts* the classifier is trained to detect; it does not produce *answers* to those prompts. Confirm your generation prompt template requests prompt-generation only, and that the generator's outputs are labelled inputs, never model-produced harmful content.
- **Do not commit the generator's harmful-prompt outputs to the solutions repo.** They live under the same mod-111 storage posture exercise 02 pins.
- **Do not run the adaptive-attack evaluation without red-team-function approval** — the evaluation is by construction an attempt to make the primary system produce harm. mod-111 owns the sanctioning; this exercise consumes it.
- **Do not report thousands-of-hours-equivalent survival on a 10s-of-hours red-team.** State your effort budget honestly; the shape is directional, not defensible for shipping.
- **Do not treat the specification as a policy document.** It is a *specification for classifier behaviour*. Policy-level statements about what the deployment must not do live in the FGAC and in the operator's own policy catalogue, and their review cadence differs from the specification's.
- **Do not skip the human-review sample.** Judge labels evaluated against judge labels report a number that means nothing; the human sample-review is the anchor.
- **Do not ship classifier-spec-v1 without a regression check** on the sub-cases classifier-spec-v0 covered. Specification edits can silently regress; the check is what surfaces the regression.
