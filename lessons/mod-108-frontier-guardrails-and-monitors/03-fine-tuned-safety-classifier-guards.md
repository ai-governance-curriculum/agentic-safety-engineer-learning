# 03 — Fine-Tuned Safety Classifier Guards

## Motivation

The published input / output classifiers chapter 02 walked — Llama Guard, ShieldGemma, OpenAI Moderation — cover the *common* hazard taxonomy well. They do not cover, and cannot be expected to cover, the *deployment-specific* hazard taxonomy that a frontier-scope FGAC's section 1 will inevitably surface. A support-agent deployment for a regulated financial-services operator has hazard classes ("unauthorised investment advice," "regulator-triggering statements about specific clients," "PII shapes that are not in Presidio's default list") that no vendor-shipped classifier is trained on. A code-generation deployment has hazard classes ("secrets exfiltration through generated code," "prompts encoded as source-code comments") that the general-purpose classifiers detect only accidentally.

The frontier-scope answer is to *fine-tune*. Take a small, capable base model (a 2B to 8B parameter LM), author a labelled dataset covering the deployment's hazard taxonomy, apply supervised fine-tuning with the load-bearing-metric being *adaptive-attack survival* rather than benchmark accuracy, and ship the resulting classifier as a layer in the FGAC. This chapter is the engineering discipline for that loop.

The failure mode this chapter fights is *the classifier that ships with 99% F1 on its training distribution and falls apart the first time an adversarial prompt reaches production*. F1 on the training distribution is a *necessary* metric; it is not the *sufficient* metric. Chapter 04 pins adaptive-attack survival as the sufficient metric and the Constitutional Classifiers methodology as the frontier-scope way to get there. This chapter builds the base capability the constitutional methodology extends.

## Primary sources

- **[Meta AI — Llama Guard: LLM-based Input-Output Safeguard (Inan et al., 2023)](https://arxiv.org/abs/2312.06674)** — the reference paper for the fine-tune shape (base model → structured-verdict fine-tune → categorical harm taxonomy). Read the training-data section for the sourcing discipline.
- **[Google — ShieldGemma (Zeng et al., 2024)](https://arxiv.org/abs/2407.21772)** — the training-data-generation discussion, particularly the synthetic-augmentation and distillation-from-a-judge techniques.
- **[Anthropic — Constitutional Classifiers (Sharma et al., 2025)](https://www.anthropic.com/research/constitutional-classifiers)** — the frontier-scope adaptive-attack survival methodology. Chapter 04 unpacks this in full; the training-data-generation discussion is the reference for this chapter's harmful-example sourcing.
- **[HarmBench (Mazeika et al., 2024)](https://arxiv.org/abs/2402.04249)** — a standardised evaluation set for automated red-team on refusal-trained models. Used as a *benchmark* (not the deployment's own benchmark) for the trained classifier.
- **[JailbreakBench (Chao et al., 2024)](https://arxiv.org/abs/2404.01318)** — a standardised adversarial-prompt benchmark and leaderboard. Also a benchmark input, not a substitute for the deployment's own.
- **[MLCommons AILuminate benchmark](https://mlcommons.org/benchmarks/ailuminate/)** — the industry-standard hazard-category-and-benign evaluation set. Used to check the classifier's coverage against a taxonomy the operator's product manager and regulator will recognise.

## The classifier fine-tune pipeline in outline

At a high level:

1. **Author the hazard taxonomy** — the FGAC's section-1 output, drawn from OWASP LLM Top 10, the operator's risk register, and MLCommons AILuminate categories where they align.
2. **Author the labelling schema** — a per-example annotation shape that captures class labels, severity, confidence, and evidence spans. Used both for supervised training and for evaluation.
3. **Source training data** — public benchmarks, red-team output from mod-104 / mod-111, in-house incident corpora, and synthetic augmentation.
4. **Curate and label** — human labellers plus a judge model (distillation from a larger LM) as an amplifier, not a replacement.
5. **Pick a base model** — a small, capable LM whose licence, size, and inference profile fit the FGAC's section-4 latency / cost budget.
6. **Fine-tune** — supervised fine-tune (SFT) as the base capability; direct preference optimisation (DPO) or reinforcement fine-tuning where the deployment's evaluation supports it.
7. **Evaluate** — against static benchmarks *and* an adaptive-attack survival curve (chapter 04); publish the numbers as FGAC section-4 fields.
8. **Ship as a layer** — deploy behind the FGAC's section-3 data-flow contract, wire to the mod-107 audit stream, wire to the mod-108 sidecar monitor.

Each step has a discipline the following sections pin.

## Training-data authoring

### The labelling schema

A defensible labelling schema captures more than a single-label verdict. For each example, the schema records:

- **Hazard class** (one or many; the FGAC's section-1 taxonomy). Multi-label is preferred; single-label collapses to multi-label when the deployment's composition semantics require it.
- **Severity** (bounded ordinal; typically 3–5 levels). The composition semantics leans on this — high-severity fires escalate to a human, low-severity fires log-and-let-through.
- **Confidence** (the labeller's own confidence). Low-confidence labels are training-data for calibration; not treated the same as high-confidence labels in loss weighting.
- **Evidence spans** (the tokens or lines that carry the hazard). Used for training a *spanning* classifier when the deployment needs to redact rather than refuse; used for interpretability when a reviewer asks *why did the classifier fire?*
- **Provenance** (where the example came from — human red-team, synthetic generator, judge-labelled, mined from incident corpus). Provenance shapes the label's weight and is a debugging tool when the classifier misbehaves.
- **Benign / adversarial** (whether the example is a legitimate benign input, an adversarial-benign — a benign-looking prompt engineered to slip past — or an adversarial-harmful).

The labelling schema is itself a versioned artefact. Change the schema and every downstream number (the FGAC's section-4 performance contract, chapter 06's FP / FN report) has to be re-baselined.

### Harmful-example sourcing under discipline

Fine-tuning a safety classifier requires the classifier to see harmful examples. Sourcing those examples is the class of work where operator policy, regulatory boundaries, and mod-111's red-team discipline all bind. Details of the sourcing posture — how to safely collect, store, and handle high-severity payloads — are deferred to **mod-111** (Automated and Scaled Red-Teaming), which owns the discipline. This chapter names the shape:

- **Public benchmarks first.** HarmBench, JailbreakBench, AdvBench, and the MLCommons AILuminate benign / adversarial sets are the starting corpus. They give a baseline the trained classifier can be evaluated against and cited from.
- **Internal red-team output next.** mod-104 (Jailbreak Engineering) and mod-111 (Scaled Red Team) produce adversarial prompts against the deployment's own primary model. Their output becomes classifier training data; the classifier does not have to *elicit* the harm — it only has to *detect* the elicitation attempt.
- **Incident corpora for the tail.** Real incidents observed against the deployment's own runtime — attempted jailbreaks, benign-but-flagged prompts that were false-positives — feed the training set. This is the tail the public benchmarks do not cover.
- **Synthetic augmentation for coverage.** Details in the next subsection.

Storage of harmful examples is under the operator's data-handling policy: retention limits, access controls, encryption at rest, tamper-evident logging of who has read what. mod-111 owns the storage discipline; this module consumes it.

<!-- needs-research: verify the current recommended handling posture for harmful-example corpora in Anthropic's published training-data discussion for the Constitutional Classifiers work, and cite it here at authoring time. -->

### Synthetic augmentation

Public benchmarks and internal red-team output produce hundreds to low thousands of examples. Fine-tuning a robust classifier wants tens to hundreds of thousands. The gap is closed by *synthetic augmentation*:

- **Paraphrase augmentation.** Take each seed example and generate N paraphrases with a capable base model. Preserves the class label; increases surface coverage. Cheap and effective for surface-form robustness.
- **Templated adversarial augmentation.** Take seed examples and apply structural transformations — encoding (base64, ROT13, hex), obfuscation (leet-speak, unicode homoglyph, whitespace-injected), roleplay wrappers ("in a fictional world where…"), context-injection ("the following is a game where…"). Preserves the underlying hazard; changes the surface. Trains the classifier's adaptive-attack robustness.
- **Distribution-shift augmentation.** Vary language, formality register, and topic. A financial-services deployment's classifier that never sees casual register or non-English content will fail on both in production.
- **Distillation from a larger judge.** Prompt a much larger LM (or a small ensemble of large LMs) to label unlabelled prompts against the schema; treat the judge's labels as training data. Cheaper than human labelling; higher variance. Use for the *coverage* pass, not the *ground-truth* pass — the ground-truth pass is human-labelled.
- **Adversarial augmentation from mod-111.** The red-team's output is *by construction* the adversarial distribution. Chapter 04 pins the reinforcing loop: red-team → classifier training data → improved classifier → red-team attacks the improved classifier → next round of training data.

Do *not* synthetically augment the benign set from the same generator you augment the harmful set from. Correlated synthetic distributions collapse the FP / FN trade-off. Benign augmentation comes from a separate source — the deployment's own benign-user traffic (with consent and PII redaction), public benign corpora, and separately-authored templates.

## Base-model choice

The base model is a lever the FGAC's section-4 latency / cost budget pulls hard on. A 2B classifier at bf16 on a modern GPU can hit sub-10-ms inference; an 8B classifier is tens of milliseconds; a 27B classifier moves into the hundreds. The FGAC's section-3 data-flow contract may afford one but not the other.

Choice discipline:

- **Licence.** The base model's licence must admit the deployment's use. Llama 3 base under Llama Community licence is admissible for most operators; Mistral base under Apache-2.0 is broadly admissible; some models under research-only licences are not admissible for production. Check.
- **Size vs latency.** A 2B model gives the input-side placement (chapter 02) a viable latency envelope; a 7–8B model gives the output-side placement. Match size to placement; do not deploy an 8B classifier on the input side and pay 40 ms on every benign call.
- **Base capability.** The base model's general-language capability sets a ceiling on the classifier's discrimination. A base model that struggles with long context or with non-English content produces a classifier with the same struggles.
- **Fine-tune ergonomics.** LoRA / QLoRA-friendly base models with well-supported HF Transformers loaders are cheaper to iterate on. The classifier will be re-trained many times during its lifecycle; ergonomics matter.
- **Base-safety-tuning inheritance.** A base model that has undergone safety-tuning already inherits some hazard-class discrimination. This is a starting point; it does not obviate the fine-tune.

For most frontier-scope deployments, a 2B classifier on the input side and a 7–8B classifier on the output side is a defensible default. Larger models (Gemma 27B, Llama 70B) are reserved for the sidecar-monitor placement (chapter 01 layer 6), where the retrospective posture admits the higher latency and cost.

## Distillation from a larger judge

The techniques ShieldGemma and Llama Guard 3 rely on include distillation from a larger judge model. The classifier being trained is small (2–9B); the judge is larger (a 70B open model, or a state-of-the-art hosted LM). The judge labels a large corpus; the small classifier is trained on the judge's labels.

Distillation is a *coverage* tool, not a *ground-truth* tool:

- **Ground-truth labels are human.** The evaluation set the FGAC's section-4 numbers are measured against uses human-labelled examples. The judge is not the ground truth.
- **Judge disagreement is signal.** When the judge and the human labellers disagree on an example, the disagreement is a training signal — either the schema is ambiguous, or the judge is mis-labelling. Both are worth debugging.
- **Judge failure inherits.** If the judge has a blind spot (a hazard class it under-detects), the small classifier trained on its labels inherits the blind spot. The evaluation set catches this only if the evaluation set is separate.
- **Cost accounting.** The judge is expensive at inference; distillation moves that cost into training-time (paid once per fine-tune round) rather than serving-time (paid on every call). This is the operational reason to distil.

Anthropic's Constitutional Classifiers methodology (chapter 04) formalises a related technique: a larger LM generates training data from a written specification. Chapter 04 pins the discipline.

## Calibration and threshold discipline

A safety classifier that reports high confidence when it is wrong is worse than one that reports moderate confidence and defers. The FGAC's composition semantics (section 5) leans on the classifier's *confidence* being informative. Confidence that is uninformative — the classifier reports 0.99 on both correct and incorrect predictions — is confidence that has to be re-calibrated post-training.

- **Expected Calibration Error (ECE).** The gap between the classifier's stated confidence and its observed accuracy, binned by confidence range. Report ECE per hazard class in the FGAC's section-4 fields. A low-ECE classifier is the one composition semantics can lean on; a high-ECE classifier requires re-calibration (temperature scaling, Platt scaling, isotonic regression) before shipping.
- **Reliability diagrams.** Publish the reliability diagram (predicted-confidence vs actual-accuracy) as an FGAC section-4 artefact. Product managers reading the FP / FN report understand *"the classifier says 90% but is actually right 60% of the time on this class"* immediately.
- **Threshold as a calibration input.** The threshold tuning discussion in chapter 02 applies here. Different per-class thresholds keyed off the calibration curve, not off a single "confidence > 0.5" heuristic.

Calibration is easy to defer and expensive to add back. Author the calibration pass as the closing step of the fine-tune loop, before the classifier ships as a layer.

## Cost and latency accounting

The FGAC's section-4 performance contract requires numbers per-classifier, per-deployment. For a fine-tuned classifier the specific fields:

- **Training cost.** GPU-hours × price + storage + labeller-hours × rate. Amortised over the classifier's expected retraining cadence (typically monthly to quarterly at frontier scope) to give a per-call training cost that composes with the per-call inference cost.
- **Inference cost.** Cost per 1 000 calls at the deployment's batch size and hardware. Includes the marginal GPU-hour, the memory footprint (a 2B model at bf16 is ~4 GB; the deployment's GPU inventory has to admit it), and the amortised platform cost.
- **Latency p50 and p95.** Measured against the deployment's own traffic distribution. Batch size 1 (for interactive calls) and batch size N (for batched sidecar-monitor calls) are separate measurements.
- **Throughput ceiling.** Requests-per-second the deployment's fleet supports. When the classifier is the bottleneck, the primary model call queues.
- **Cost of failure.** When the classifier is down, what does the FGAC's section-5 composition semantics do? Fail-closed (no calls admitted) or fail-open (calls admitted without the classifier's verdict, logged as such)? Both are choices; the operational cost of each is a real number.

Publish these numbers as FGAC fields, not as a deck. They are what mod-108 monitors alert on and what mod-109's safety case cites.

## Adaptive-attack survival as the load-bearing metric

Static F1 on the training distribution is a *training-time* metric. A classifier can hit 96% F1 and be trivially bypassed by a novel adversarial phrasing not in the training set. The *deployed-scope* metric is **adaptive-attack survival**: how many red-team hours (or how many red-team API calls, or how many adaptive-attack iterations) does the classifier survive before its per-hazard-class recall drops below a threshold?

Chapter 04 unpacks the constitutional-classifiers methodology and the specific evaluation Anthropic's paper pins. This chapter's discipline is: *do not ship a fine-tuned classifier whose evaluation stops at static F1*. The static-F1 evaluation is the necessary first pass; the adaptive-attack survival evaluation is the load-bearing pass. If the deployment cannot afford to run the adaptive-attack evaluation (mod-111 is the discipline that scales it), the FGAC's section-4 field records the deficiency and the deployment's residual-risk claim (mod-109) accepts the burden.

The measurement discipline in chapter 06 pins the specific TP / FP / TN / FN accounting and the FP / FN report shaped for a product manager.

## The iteration loop with red-team

The classifier is not a one-shot deliverable. It sits in a loop with mod-111's red-team:

```
[classifier v0] -> deploy behind FGAC
                -> mod-111 red-team hammers
                    -> new adversarial examples
                    -> added to training corpus
                -> classifier v1 fine-tuned on expanded corpus
                    -> re-evaluated on adaptive-attack survival
                    -> if survival improved: ship
                    -> if survival regressed on a class: debug
                -> deploy v1
                -> repeat
```

The loop is what pushes the adaptive-attack survival curve upward. A classifier shipped once and left alone regresses relative to the adversary; the loop is the discipline that fights the regression. mod-111 owns the red-team side; mod-108 owns the classifier side; the shared artefact is the training corpus.

## Interfaces to the rest of the module

- **Chapter 01** — a fine-tuned classifier is a layer-2 or layer-4 entry in the FGAC's inventory (section 2), with its own row in the section-4 performance contract.
- **Chapter 02** — the placement discipline for the fine-tuned classifier is the same as for the published classifiers: input-side, output-side, or both. The choice keys off the classifier's characteristic failure mode.
- **Chapter 04** — the Constitutional Classifiers methodology extends this chapter's fine-tune loop with a *specification-driven* training-data generator and the adaptive-attack survival curve as the primary evaluation. Read chapter 04 as the frontier-scope extension of this chapter.
- **Chapter 05** — the build-vs-buy matrix uses this chapter's cost-and-latency-and-adaptive-survival accounting as the *build* row. The *buy* row uses the same accounting on vendor products.
- **Chapter 06** — the FP / FN report consumes the per-hazard-class precision / recall this chapter's evaluation produces.
- **mod-104 / mod-111** — the red-team side of the iteration loop. mod-111 owns harmful-example sourcing discipline; this chapter consumes it.
- **mod-112** — when a fine-tuned classifier's FP hits a customer or FN misses a harm that discloses, mod-112's disclosure workflow consumes the classifier's own evidence trail.

## Common misreadings to avoid

- **"96% F1 on the benchmark means the classifier is ready to ship."** F1 on the benchmark is a training-time signal. The deployed-scope signal is adaptive-attack survival across a red-team budget. Ship on the second; use the first as a floor.
- **"We can synthetically augment the benign set from the same generator as the harmful set."** No. Correlated synthetic distributions collapse the FP / FN trade-off. Benign augmentation comes from a separate source — real benign traffic, separate templates, separate generators.
- **"A larger judge model as an amplifier is a labeller substitute."** No. The judge amplifies coverage; the human ground-truth labels the evaluation set. Blurring the two is a finding — the classifier trained on judge labels evaluated against judge labels reports a number that means nothing.
- **"We can skip the retraining cadence; the classifier will hold."** The adversary does not. mod-111's red-team output is the pressure that requires retraining; a classifier that ships once and never retrains regresses relative to the adversary within weeks at frontier scope.
- **"Bigger base models make better classifiers."** They make more expensive classifiers. The 2B classifier at 10 ms is often the correct input-side layer; the 8B classifier at 40 ms is often the correct output-side layer. Size follows placement and budget, not the other way around.
- **"Fine-tuning is peer-role work; the safety engineer just consumes the model."** No. The safety engineer *authors the training-data policy*, *authors the labelling schema*, and *owns the evaluation*. The peer-role that runs the training infrastructure (see the `fine-tuning-engineer` peer role) is a handoff for the *compute*, not for the *content*.

## Summary

- Fine-tuned safety classifiers are the FGAC layer that covers the deployment-specific hazard taxonomy the vendor-shipped classifiers do not.
- The pipeline: hazard taxonomy → labelling schema → training-data sourcing (public benchmarks + internal red-team + synthetic augmentation + distillation from a judge) → base-model choice → fine-tune → evaluate → ship as a layer.
- Harmful-example sourcing is under mod-111's discipline; this chapter consumes it. Do not free-hand the discipline.
- The load-bearing metric is **adaptive-attack survival**, not static F1. Chapter 04's Constitutional Classifiers methodology is the frontier-scope extension.
- Publish per-class precision, per-class recall, latency p50 / p95, and cost as FGAC section-4 fields. Vendor-paper numbers do not substitute.
- The classifier sits in a loop with mod-111 red-team; the loop is what pushes adaptive-attack survival upward.
- The safety engineer owns the labelling schema, the training-data policy, and the evaluation. The peer fine-tuning role owns the compute infrastructure.
