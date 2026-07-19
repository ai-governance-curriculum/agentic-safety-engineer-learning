# 04 — Constitutional Classifiers Methodology

## Motivation

Chapter 03 built the pipeline for fine-tuning a safety classifier. Chapter 02 pinned per-class precision, recall, latency, and cost as the FGAC's section-4 fields. Both took *static-benchmark* evaluation as the training-time signal. Neither is sufficient at frontier scope, and both concede as much: the load-bearing metric is *adaptive-attack survival*, and no static benchmark captures it. This chapter walks the current frontier-scope methodology that treats adaptive-attack survival as the *primary* evaluation and derives the training data from a *written specification* — Anthropic's **Constitutional Classifiers** methodology, published as Sharma et al. (2025).

The methodology answers three questions the previous chapters left open. *Where does the training data come from* when the deployment does not have a large internal red-team corpus? — from a specification, expanded by synthetic generation. *How much red-team is enough?* — thousands of hours against a live target, with the survival curve as the evidence. *What is the shipping bar?* — a survival curve that a safety review can compare against the pre-deployment classifier and against a residual-risk claim. Chapter 04's discipline is what makes those three answers defensible.

At time of authoring, Anthropic's Constitutional Classifiers paper is the single most-cited public methodology for adaptive-attack-robust classifier training. The paper does not ship the model weights; it ships the *methodology*, and the methodology is what this chapter teaches. Operators applying it re-derive the classifiers against their own base model, their own specification, and their own red-team. The paper's numbers are the reference the FGAC can *aspire* to; the operator's numbers are what the FGAC *records*.

## Primary sources

- **[Anthropic — Constitutional Classifiers: Defending against universal jailbreaks (Sharma et al., 2025)](https://www.anthropic.com/research/constitutional-classifiers)** — the foundational paper. Read the full text before authoring an FGAC that cites the methodology. <!-- needs-research: pin the exact arXiv identifier and publication date of the Constitutional Classifiers paper at authoring time; Anthropic's landing page and arXiv sometimes diverge. -->
- **[Anthropic — Constitutional AI paper (Bai et al., 2022)](https://arxiv.org/abs/2212.08073)** — the earlier constitutional-AI work the classifiers methodology builds on. Read the *constitution*-authoring section for the specification-as-training-signal framing.
- **[Anthropic — Many-shot Jailbreaking (2024)](https://www.anthropic.com/research/many-shot-jailbreaking)** — the adversarial-attack methodology the Constitutional Classifiers paper's red-team leans on; a specific class the classifier's survival curve is evaluated against.
- **[Anthropic — Challenges in red teaming AI systems (2024)](https://www.anthropic.com/news/challenges-in-red-teaming-ai-systems)** — the practitioner-facing discussion of the red-team methodology at scale. Read for the operational context of the thousands-of-hours red-team the classifiers paper cites.
- **[MLCommons AILuminate benchmark](https://mlcommons.org/benchmarks/ailuminate/)** — the industry hazard-and-benign benchmark the survival-curve evaluation should be reported against for cross-operator comparability.
- **[HarmBench (Mazeika et al., 2024)](https://arxiv.org/abs/2402.04249)** and **[JailbreakBench (Chao et al., 2024)](https://arxiv.org/abs/2404.01318)** — standardised benchmarks for the *static-benchmark* baseline; the survival curve extends beyond them.

## What the methodology is

At its core, Constitutional Classifiers is a fine-tuning methodology that:

1. **Starts from a written specification** ("the constitution") that enumerates what the classifier must catch and what the classifier must not catch. The specification is authored by the safety engineer; it is not a taxonomy of *harm* alone — it is a taxonomy of *distinctions the classifier must draw*, including benign edge cases.
2. **Generates training data from the specification** using a larger LM as a synthetic-data engine. The LM is prompted with the specification and produces adversarial-harmful examples, adversarial-benign examples, and clearly-benign examples in balance.
3. **Augments the synthetic corpus with red-team output** — human red-team, automated red-team (mod-111), and prior-incident corpora. The synthetic corpus gives coverage; the red-team corpus gives the tail.
4. **Fine-tunes a classifier** (input-side, output-side, or both) on the combined corpus, targeting a *low false-positive rate on benign* and a *high recall on adversarial-harmful*.
5. **Evaluates the classifier via an adaptive-attack survival curve** — the load-bearing evaluation. Not static F1; not a single accuracy number. The evaluation is a curve of *classifier recall on harmful* as a function of *red-team effort*.
6. **Iterates the specification** when the survival curve reveals systematic failures. The specification is a versioned artefact; each version corresponds to a classifier release.

The methodology's distinguishing features against chapter 03's baseline are (a) the *specification-as-training-signal* framing and (b) the *adaptive-attack survival curve* as the primary evaluation. Both are the frontier-scope answers to *what if the adversary hasn't shown you the attack yet?*

## The specification (the constitution)

The specification is a natural-language document, authored by the safety engineer, that pins the classifier's behaviour. It has three parts:

- **Positive rules** — what the classifier must catch. Enumerated per hazard class, with *examples*, *counter-examples*, and *edge cases*. "The classifier must catch instructions for synthesising [class of substance] regardless of the framing (hypothetical, roleplay, fiction, code, encoded)." Positive rules generate the *adversarial-harmful* synthetic corpus.
- **Negative rules** — what the classifier must *not* catch. Also enumerated with examples. "The classifier must *not* refuse a discussion of the *history* of [class of substance]; must *not* refuse a health-education question about [related topic]; must *not* refuse a fiction scene that references [class of substance] without step-wise instruction." Negative rules generate the *adversarial-benign* synthetic corpus — the edge-case benigns that a naively-trained classifier will over-flag.
- **Disambiguation rules** — the specific distinctions between benign and harmful the classifier must draw. These are the hardest cases; often expressed as decision trees or pairwise comparisons. Disambiguation rules are where the specification does its load-bearing work.

The specification is authored *before* the training data is generated, not after. Authoring against observed data is a common failure mode — the specification ends up encoding the specific examples in the corpus rather than the underlying distinction the classifier needs to learn. Author the specification *from the FGAC's section-1 hazard taxonomy* and the deployment's negative-rule catalogue; use the synthetic-generation step to test whether the specification is executable.

A specification that a capable LM cannot successfully use to generate a labelled example is a specification the classifier will struggle to learn. The synthetic-generation step is, in this sense, a *test* of the specification's quality.

<!-- needs-research: pin the exact structural elements Anthropic's Constitutional Classifiers paper reports for its specification; the paper's supplement enumerates section shapes worth citing verbatim in the FGAC. -->

## Synthetic training-data generation from the specification

Given the specification, the training data is generated by prompting a capable LM (typically a large frontier model) with the specification and asking it to produce examples. The generation pass produces:

- **Adversarial-harmful examples.** Prompts that, if answered honestly, would elicit the hazard. The LM is prompted to *generate the prompt*, not to answer it. Diverse phrasing, encoding, roleplay wrappers, and multi-turn structures are all covered by generation templates.
- **Adversarial-benign examples.** Prompts that *look* like they might elicit the hazard but that the negative rules require the classifier to admit. This is the specific class a naive classifier over-flags.
- **Clearly benign examples.** Ordinary benign traffic — jokes, cooking questions, coding help — to balance the corpus.
- **Multi-turn examples.** Sequences where individual turns look benign but the trajectory reaches a harm. The classifier's training set has to include multi-turn structure for the deployed classifier to catch it.

The generation pass runs at scale: tens of thousands to hundreds of thousands of examples per hazard class. Cost is bounded by the generating LM's per-token cost times the corpus size; the paper's methodology amortises this across many classifier iterations.

The generation is *labelled by construction* — the generator was told which class it was producing. This is cheaper than post-hoc labelling but inherits the generator's blind spots. The mitigation is the augmentation from human red-team and mod-111 automated red-team, which brings distribution the generator did not.

### Filtering and de-duplication

Raw synthetic corpora contain duplicates, degenerate outputs, and off-schema examples. The generation pipeline includes:

- **De-duplication** by canonicalised text hash and by near-neighbour embedding distance. Duplicates over-represent specific attack patterns and skew evaluation.
- **Schema filtering** — outputs that don't match the labelling schema (missing evidence spans, wrong severity, wrong hazard class) are dropped, not silently accepted.
- **Judge re-labelling** — a separate LM re-labels a sample of the generated corpus to catch generator-side mistakes. Disagreement between generator and judge is a debugging signal.
- **Human sample-review** — a fraction of the corpus is human-reviewed at the labelling schema of chapter 03. This is the ground-truth anchor; without it, the corpus's quality is a self-assessment.

## The adaptive-attack survival curve as evaluation

This is the frontier-scope innovation of the methodology. The evaluation is not a single accuracy number on a static benchmark; it is a *curve* showing how the classifier's recall on harmful degrades as the red-team invests more effort.

### The curve's shape

X-axis: red-team effort. Measured in one of several equivalent units — red-teamer hours, automated-red-team API calls, adaptive-attack iterations against a live target. The unit is a choice; the FGAC records which.

Y-axis: classifier recall on harmful *among prompts the red-team submitted*. A red-team submitting only trivial attacks would produce a curve that never degrades; the interesting curve is the one where the red-team is *actively finding the classifier's blind spots*.

### The methodology

Two versions of the primary system are prepared: one behind the classifier under evaluation, one without (or with a baseline classifier). The red-team is given access to both. The red-team's goal is to find prompts that succeed against the primary system *and* pass the classifier under evaluation. The classifier's recall on the red-team's successful attacks is the curve's y-value at the red-team's cumulative effort x-value.

The Constitutional Classifiers paper reports thousands of hours of red-team effort against the classifier, with the curve remaining significantly above the baseline throughout. The specific numbers should be read from the paper; the shape of the argument is what to internalise. <!-- needs-research: pin the exact reported red-team-hour count and the reported gap between classifier-on and classifier-off jailbreak success in the FGAC when citing the paper. -->

### Why it is the right metric

Static-benchmark F1 answers *"of the attacks we already know, how many does the classifier catch?"* Adaptive-attack survival answers *"as the adversary invests more effort, how quickly does the classifier fail?"* At frontier scope, the second is the operationally load-bearing question. The FGAC's section-4 performance contract *should* report both, but the survival curve is the number the safety review cares about.

### What "thousands of hours" means operationally

The paper's evaluation is not something an operator replicates on a whim. It costs at frontier scope: dozens of skilled red-teamers, weeks to months of engagement, per-attack cost measured in tens to hundreds of dollars of primary-model inference. Operators without that budget report the *methodology* they applied and the *scale* they achieved, and the residual-risk claim (mod-109) accepts the specific scope.

mod-111 (Automated and Scaled Red-Teaming) is the discipline that scales the red-team side; the automated harness produces the majority of the attack volume, with human red-team producing the tail and the novel-attack discovery. This is the composition the paper's methodology relies on.

## The iteration loop

The methodology is not a one-shot deliverable. It sits in a loop:

```
[specification v0]
  -> [synthetic corpus v0] + [red-team corpus v0]
  -> [classifier v0 trained]
  -> [adaptive-attack survival evaluation]
  -> [failures categorised]
      -> specification updates (new negative rules, new disambiguation rules)
      -> corpus updates (new adversarial examples)
  -> [specification v1]
  -> [classifier v1 trained]
  -> [survival evaluation]
  -> ... continues over the classifier's lifecycle
```

Each iteration produces a *new specification version*, a *new classifier version*, and a *new survival curve*. The FGAC's section-8 change-management contract pins the review that gates each shipment.

The load-bearing property of the loop is that the specification is *the artefact that improves*, not merely the training corpus. When the survival curve degrades on a class of attacks, the correct response is to update the specification (making the distinction the classifier must draw more precise) rather than only to add more training examples of that class. Training-only fixes generalise poorly; specification-driven fixes generalise better because the synthetic generator uses the new specification to produce a *distribution* of new examples, not a narrow slice.

## The specification's failure modes

Specifications, like classifiers, have failure modes. A defensible specification-authoring pass anticipates them.

- **Over-specification.** A specification that enumerates thousands of positive-rule cases loses the classifier's capacity to *generalise* from the rules. The synthetic generator produces the enumerated cases well and generalises poorly. Prefer principled rules with examples over exhaustive enumeration; the generator will interpolate.
- **Under-specification of negative rules.** A specification with a rich positive-rule set and a thin negative-rule set produces a classifier that over-flags. The negative rules are the *edge cases the classifier must admit as benign*; they are as load-bearing as the positive rules and are the field where safety engineers most commonly under-invest.
- **Cross-cultural blind spots.** A specification written by a monolingual monolithic-culture safety-engineering team encodes that team's blind spots. Frontier deployments serve many populations; the specification's edge-case coverage benefits from diverse-perspective review before the synthetic-generation pass.
- **Ambiguous disambiguation rules.** A disambiguation rule the human labeller reads two ways is a rule the classifier will learn one way and be wrong the other. Test the disambiguation rules against a human-labelling pass; disagreement rates above a threshold are a specification-editing signal, not a classifier-training signal.
- **Silent policy encoded in the specification.** Sometimes an operator's product-policy stance leaks into the specification without being flagged as such. This is a governance issue: the specification is now a de-facto policy artefact, and its review requires policy stakeholders, not just safety engineers. Chapter 05's build-vs-buy matrix and mod-112's disclosure workflow both consume the specification as if it were policy; the FGAC's section-8 change-management contract has to reflect that.

## The methodology's cost envelope

For the FGAC's section-4 fields, the constitutional-classifiers methodology carries a specific cost profile:

- **Specification authoring.** Human safety-engineer time. Weeks to months for a frontier-scope deployment. The dominant early-lifecycle cost.
- **Synthetic-corpus generation.** Generating LM inference. Bounded by corpus size times per-token cost of the generator; typically single-digit-thousands of dollars per full corpus at frontier scope. <!-- needs-research: confirm typical per-corpus generation cost at frontier scope; Anthropic's paper does not enumerate. -->
- **Classifier fine-tune.** GPU-hours on the base model. Similar cost profile to chapter 03's fine-tune.
- **Adaptive-attack evaluation.** The dominant late-lifecycle cost. Human red-team hours plus automated red-team API cost plus primary-model inference. The paper's *thousands of hours* is the frontier-scope reference; operators scale to what their deployment's risk profile justifies.
- **Iteration cadence.** Frontier deployments retrain the classifier on the order of monthly; the specification updates on the order of quarterly. Slower cadences leave the survival curve to regress against the adversary.

Publish these numbers in the FGAC's section-4 field for the deployment. Chapter 06's FP / FN report consumes them.

## Grounding the methodology in the FGAC

A constitutional-classifiers classifier occupies one of the FGAC's layer-2 or layer-4 slots. Its specific placements:

- **Input side.** The classifier reads the input and emits a verdict against the specification's hazard classes. When paired with an output-side constitutional classifier, it is the input half of a two-side stack.
- **Output side.** The classifier reads the primary model's response and emits a verdict. Often stronger than the input-side placement because the specification's disambiguation rules apply to the response as directly as to the prompt.
- **Both sides.** Frontier-scope default. Different specifications for the two sides; different disambiguation rules for input-side and output-side edge cases.
- **Sidecar (retrospective).** A larger constitutional classifier can be run over the audit-log stream (chapter 01 layer 6) to catch trajectories the inline layers missed. The retrospective posture admits higher latency and higher-recall thresholds.

The FGAC's section 5 (composition semantics) pins how the constitutional classifier's verdict combines with the other layers' verdicts. Common shapes: weighted-vote with the constitutional classifier's confidence as the weight; escalate-to-sidecar on any disagreement between the constitutional classifier and a rule-guard layer; log-only when the constitutional classifier is the *only* layer flagging.

## Interfaces to the rest of the module

- **Chapter 01** — the constitutional-classifier layer is a specific type of input classifier (layer 2) or output classifier (layer 4). The FGAC's inventory records the specification version and the survival-curve summary as evidence.
- **Chapter 02** — this chapter extends chapter 02 with the specification-driven training-data generation and the adaptive-attack survival curve as the primary evaluation.
- **Chapter 03** — this chapter is the *frontier-scope* extension of chapter 03's fine-tune loop. Read chapter 03 first for the pipeline; this chapter for the constitutional discipline.
- **Chapter 05** — vendor products that claim adaptive-attack robustness should be evaluated against the same survival-curve methodology. Chapter 05's build-vs-buy matrix cites this chapter's evaluation posture.
- **Chapter 06** — the survival curve is one of the primary numbers in the FP / FN report chapter 06 shapes.
- **mod-111** — the automated and scaled red-team is the discipline that runs the adaptive-attack evaluation. The synergy is bidirectional: red-team output feeds classifier training; classifier improvements shape the next round of red-team.
- **mod-109** — the survival curve is the primary evidence for the *control-argument* leg of a safety case. mod-109 cites the curve; mod-108 produces it.
- **mod-110** — the paper's evaluation composes with mod-110's adversarial-alignment stress: does the classifier hold when the primary model is instructed to help the adversary? Chapter 04's discipline reads mod-110 as a companion.

## Common misreadings to avoid

- **"The constitution is a policy document."** No. It is a *specification for classifier behaviour*, expressed in natural language for the synthetic generator's benefit. Policy for the deployment lives in the FGAC and in the operator's own policy catalogue; the constitution is a narrower engineering artefact.
- **"Static F1 above the paper's number means the classifier is as good as the paper's."** No. The paper's number is on the paper's evaluation; a static-F1 comparison is not equivalent to a survival-curve comparison. Only survival-curve numbers compare across classifiers.
- **"Synthetic training data replaces the red-team corpus."** No. Synthetic data gives coverage; red-team gives the tail. Both are load-bearing; the paper's methodology relies on their composition.
- **"We can adopt the methodology without running the adaptive-attack evaluation."** Then you have not adopted the methodology; you have adopted only its training-data-generation step. The methodology's defining feature is the survival curve as evaluation.
- **"The specification is written once and reused across classifiers."** The specification is versioned. Each classifier release corresponds to a specification version; the versions are what the FGAC's section-8 change-management contract signs on.
- **"Thousands of hours of red-team is what a frontier lab does; my operator can't."** The paper's scale is the *reference*, not the *bar*. Operators run the evaluation at the scale their risk profile justifies and report the achieved scale as an FGAC section-4 field. The residual-risk claim (mod-109) accepts the specific scope; the survival-curve *shape* remains the metric even at smaller scale.

## Summary

- Constitutional Classifiers (Sharma et al., 2025) is the frontier-scope methodology for training safety classifiers that survive adaptive attack. Read the paper before authoring an FGAC that cites it.
- Defining features: **specification-driven training-data generation** (write the classifier's spec, then have a capable LM generate training data from it) and the **adaptive-attack survival curve** as the primary evaluation.
- The specification enumerates positive rules (what to catch), negative rules (what not to catch), and disambiguation rules (the distinctions the classifier must draw). Author the specification *before* the training data.
- Synthetic training data gives coverage; red-team output gives the tail; both are required. mod-111 owns the red-team side.
- The survival curve reports classifier recall on harmful as a function of red-team effort. The paper's *thousands-of-hours* red-team is the frontier-scope reference; operators scale to their risk profile.
- The methodology is iterative: specification → corpus → classifier → survival curve → specification update → repeat. Retrain monthly at frontier scope; update the specification quarterly.
- Chapter 04 extends chapter 03's fine-tune loop with this discipline. Chapter 06 consumes the survival curve as an FP / FN report input.
