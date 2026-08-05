# exercise-02: Fine-Tune a Safety Classifier Guard

**Estimated effort:** 3 hours (specification and pipeline authoring; the fine-tune compute itself is a peer-role handoff and runs longer)

## Objective

Ship a fine-tuned safety classifier that occupies one specific slot in your FGAC's layer inventory — either input-side (layer 2) or output-side (layer 4), chosen deliberately against the deployment's hazard taxonomy. The deliverable is the *pipeline artefact*, not just the trained model: the hazard taxonomy the classifier learns, the labelling schema, the training-corpus construction (public benchmark + red-team + synthetic augmentation), the base-model choice with justification, the calibration pass, and the evaluation shaped into the FGAC's section-4 fields.

The load-bearing question is *would a safety reviewer sign off shipping this classifier as an FGAC layer?* If your report stops at "96% F1 on the held-out set", the reviewer will not. Chapter 03 pins the discipline; this exercise is you executing it.

## Prerequisites

- Read chapter 03 (Fine-Tuned Safety Classifier Guards) end-to-end. Read chapter 02 for the placement discipline and chapter 06 for the section-4 field shape.
- Complete exercise 01 or have an equivalent FGAC in hand — you need a hazard taxonomy the classifier is being trained against and a placement slot the classifier is filling.
- Coordinate with the `fine-tuning-engineer` peer role (or whoever owns fine-tune compute in your context) for the training run. Understand who owns the compute cost and the model-weight lifecycle.
- Coordinate with mod-111 (or the equivalent red-team function) for harmful-example sourcing under discipline. **Do not free-hand harmful-example collection.** The storage, retention, and access-control posture is mod-111's; this exercise consumes it.
- Access to a small base model (2B–8B parameter range) with a permissive licence and LoRA / QLoRA-friendly tooling. Llama 3 8B, Mistral 7B, Gemma 2B / 9B, or your operator's own base are all reasonable candidates; the choice discipline is section 3.

## Requirements

Produce a fine-tune-pipeline package with the sections below. The pipeline is what the reviewer signs on; the trained model is one artefact of it.

### Section 1 — Hazard taxonomy and placement decision

- **Taxonomy scope.** Pull the specific hazard classes from your FGAC's section-1 for this classifier. Not the whole FGAC's taxonomy — the subset this one classifier is trained to detect. Typically 3–10 classes; more than 15 and the multi-label problem overwhelms the fine-tune.
- **Placement.** Input-side, output-side, or both — with justification against the hazards' characteristic surfaces. A hazard whose signal is stronger in the response (system-prompt-leakage, PII in generated text) lives on the output side; a hazard whose signal is stronger in the input (encoded injection payloads, roleplay wrappers) lives on the input side.
- **Model-size / latency budget.** Pull from your FGAC's section-4 latency budget. This constrains base-model choice in section 3.

### Section 2 — Labelling schema

Author the per-example schema chapter 03 pins. At minimum:

- Hazard-class list (multi-label allowed).
- Severity ordinal (bounded — 3 or 5 levels).
- Labeller confidence (bounded — low / medium / high).
- Evidence-span pointer (optional but strongly recommended for interpretability).
- Provenance tag (`public-benchmark:harmbench`, `red-team-mod111`, `synthetic-paraphrase`, `synthetic-templated`, `judge-labelled`, `incident-corpus`, `benign-production`).
- Benign / adversarial-benign / adversarial-harmful axis.
- Schema version tag.

Publish a labelling handbook — a 1–2 page document a human labeller could read and apply consistently — including at least three worked-example annotations per hazard class.

### Section 3 — Base-model choice

Score at least three candidate base models against:

- **Licence** admissibility for the deployment.
- **Size vs latency budget** (section 1).
- **Base capability** — instruction-following, multilingual coverage if the deployment needs it.
- **Fine-tune ergonomics** — LoRA / QLoRA support, HF Transformers loader maturity.
- **Base-safety-tuning inheritance** — does the base come pre-tuned for refusal?

Publish the score matrix and the selected base. A one-paragraph justification for the winner.

### Section 4 — Training-corpus construction

Assemble the corpus with the composition discipline chapter 03 pins:

- **Public benchmarks first.** Pull relevant subsets from HarmBench, JailbreakBench, AdvBench, AILuminate benign / adversarial sets, and any specialised benchmark for your hazard classes. Cite the exact release you pulled and the license.
- **Internal red-team output next.** Pull from mod-111's corpus for your deployment. Coordinate on the storage / access-control posture; do not copy harmful payloads outside the sanctioned storage.
- **Synthetic augmentation.** For each seed example, generate paraphrases and templated adversarial transforms (encoding, roleplay wrappers, context-injection wrappers). Pin the generation-time cost.
- **Benign augmentation from a separate generator.** Do **not** reuse the harmful-side generator. Draw from production traffic (with consent + PII scrubbing), open benign corpora, and separately-authored benign templates.
- **Judge-labelled coverage pass.** Use a larger LM to label an unlabelled pool of prompts against the labelling schema; the judge amplifies coverage but does not substitute for the human-labelled ground-truth.
- **Human-labelled ground-truth pass.** A held-out slice, labelled by two or more humans with inter-rater-agreement measured (Cohen's or Fleiss' kappa). This is the ground-truth anchor for section 6.

Publish per-source counts, per-hazard-class counts, and the train / val / test split. The test set is *held out* — never seen by training or by hyperparameter tuning.

### Section 5 — Fine-tune

Run the fine-tune (SFT baseline; DPO or reinforcement fine-tuning as stretch). Publish:

- Base-model checkpoint hash + version.
- Training hyperparameters (LR schedule, batch size, LoRA rank / alpha, epochs).
- Compute cost (GPU-hours × price + storage). Amortise over expected retrain cadence.
- Training curves (loss + validation F1 per hazard class per epoch).
- The resulting classifier's checkpoint hash + version pin.

### Section 6 — Calibration

Apply and report:

- **Expected Calibration Error (ECE)** per hazard class on the held-out test set.
- **Reliability diagram** per hazard class.
- **Post-hoc calibration** (temperature scaling at minimum; Platt or isotonic where the class supports it) if pre-calibration ECE is above the threshold your FGAC's section-5 composition semantics can lean on. Document the calibration transform as part of the classifier's shipped configuration.
- **Per-class threshold recommendation** keyed off the calibration curve, not a single "confidence > 0.5" heuristic.

### Section 7 — Evaluation → FGAC section-4 fields

Publish the fields chapter 06 pins:

- Per-hazard-class TP / FP / TN / FN, precision, recall, F1, PR-AUC on the held-out test set. **With bootstrap 95% confidence intervals.**
- False-positive rate on the benign set (aggregate + per class).
- Latency p50 and p95 at inference batch-size-1 (interactive) and batch-size-N (batched sidecar).
- Cost per 1 000 calls including amortised training.
- Comparison against a baseline — the vendor-shipped classifier this classifier would replace or complement (Llama Guard 3, ShieldGemma at appropriate size, or the primary model's own safety-tuning as a floor). The *marginal* improvement is the number the FGAC's residual-risk analysis cites.

### Section 8 — Adaptive-attack readiness note

Static-benchmark performance is the training-time signal. Adaptive-attack survival is the deployed-scope signal. Exercise 05 executes the full survival evaluation; this section commits to it:

- Which red-team unit (hours / prompts / iterations) will be used.
- What the target survival threshold is.
- What the retraining cadence commitment is if the classifier's survival regresses on any hazard class.

## Deliverables

Commit to the paired solutions repo:

- `taxonomy.md` and `labelling-schema.md` — sections 1 + 2.
- `labelling-handbook.md` — the human-labeller guide.
- `base-model-selection.md` — section 3, with the score matrix.
- `corpus-manifest.md` — section 4, per-source counts, split shapes, provenance tags. **Do not commit the harmful examples themselves; commit the manifest that references them under the mod-111 storage posture.**
- `training-config.yaml` and `training-run.md` — section 5.
- `calibration.md` and the reliability-diagram plots.
- `evaluation-report.md` — section 7 with the FGAC section-4 field values.
- `adaptive-attack-plan.md` — section 8 commitments.
- The classifier's version-pinned checkpoint or a pointer to it under your model-registry posture.

## Acceptance criteria

- **The hazard taxonomy trained against matches an FGAC's section-1 hazard classes.** Free-standing taxonomies without an FGAC anchor are a rejection.
- **The labelling schema captures multi-label, severity, confidence, and provenance.** A single-label-only schema is a rejection.
- **Harmful examples are sourced under mod-111's discipline** — under the operator's data-handling policy, with retention limits and access controls. Free-handed sourcing is a rejection.
- **Benign augmentation uses a separate generator from harmful augmentation.** Correlated synthetic distributions are a rejection.
- **Judge-labelling amplifies coverage but the ground-truth is human-labelled.** Judge labels evaluated against judge labels is a rejection.
- **Per-hazard-class evaluation with bootstrap 95% CIs is published.** Aggregate-only reports are a rejection.
- **Calibration is measured and (where necessary) applied.** An uncalibrated classifier whose confidences the composition semantics leans on is a rejection.
- **The baseline is present in the evaluation.** Marginal improvement over vendor-shipped or primary-model-safety-tuning is the load-bearing number.
- **The classifier's section-4 fields are ready for FGAC insertion.** No TBDs.
- **A commitment to adaptive-attack survival evaluation exists.** Static-benchmark-only shipping is a rejection.

## Stretch goals

- **Train two classifiers, one at each side.** A 2B classifier on the input side and a 7–8B classifier on the output side, with distinct fine-tunes; report the composition's FP / FN under both refuse-on-any-fire and weighted-vote composition semantics.
- **Multi-turn corpus construction.** Include multi-turn adversarial trajectories in the training data (chapter 04 pattern) and evaluate on a multi-turn held-out set. Report whether recall on multi-turn attacks differs from single-turn.
- **Distillation from a larger judge.** Use a 70B open model as the judge for a coverage-pass corpus; compare classifier F1 on judge-only labels vs human-anchored labels.
- **DPO or reinforcement fine-tuning.** Extend the SFT baseline with preference optimisation on pairwise (harmful-response, benign-response) preferences. Report whether the FP / FN trade-off improves.
- **Inter-labeller-agreement report.** Publish Cohen's or Fleiss' kappa on the human-labelled subset per hazard class. Low agreement is a specification-editing signal, not just a training-data quality signal — feeds into exercise 03.
- **Retraining-cadence sim.** Simulate one retraining cycle: hold aside 20% of mod-111's most recent corpus, retrain, re-evaluate, and report whether the survival-curve targets from section 8 would be met. This is the shape of the loop chapter 03 pins.

## Guardrails

- **Do not collect, store, or handle harmful-example payloads outside mod-111's storage discipline.** The classifier can be trained without a copy of the corpus living in your working tree; the corpus lives under mod-111's controlled storage and the fine-tune reads from it.
- **Do not commit any harmful payloads, real customer PII, or production traffic samples to the solutions repo.** Commit manifests and hashes; not payloads.
- **Do not use closed-source frontier models to generate harmful synthetic training data** unless their terms of service explicitly admit it and your operator's data-flow policy admits sending the seed prompts. Confirm both before generating.
- **Do not evaluate on the training set.** The held-out discipline is what separates a training-time number from a deployed-scope number.
- **Do not report only aggregate F1.** Per-class numbers with CIs are the reviewer contract.
- **Do not compare against a synthetic baseline.** The baseline is a real, well-known classifier (Llama Guard 3, ShieldGemma at appropriate size, OpenAI Moderation, or the primary model's own safety-tuning) on the same benchmark.
- **Do not ship without a calibration pass** — an over-confident classifier destroys the FGAC's composition semantics.
