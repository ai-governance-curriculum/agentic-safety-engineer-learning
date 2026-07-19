# 06 — Guardrail Effectiveness Measurement and Boundaries

## Motivation

Chapters 01–05 built the FGAC and its layer implementations. This chapter answers the question every safety review, every product manager, and every downstream module will ask: *how effective is the composition, and how do we know?* Effectiveness is not a single number. It is a *shaped report* — precision and recall per hazard class, aggregate F1, adaptive-attack survival, cost and latency at p50 and p95, benign-user overhead, and the specific false-positive / false-negative accounting a product manager can act on.

The chapter's discipline: build the *measurement contract* the FGAC's section-4 fields commit to, run it against the deployment's own benchmark set, and produce an **FP / FN report** that a product manager can read without re-deriving the arithmetic. This chapter also draws the boundaries: `ai-risk-engineer` (prerequisite, level 25) carries the base depth on the tools the FGAC uses; mod-109 consumes the effectiveness numbers as safety-case evidence; mod-111 hammers the FGAC and updates the numbers; mod-112 discloses when the numbers surface a harm.

The failure mode this chapter fights is *the FGAC that reports 95% accuracy and hits production with a benign-user-refusal rate the product manager cannot defend to customers*. Aggregate accuracy hides the numbers that matter. This chapter's discipline is *unhiding* them.

## Primary sources

- **[OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)** — the hazard taxonomy the measurement report groups by. Cite the specific entries the FGAC's section-1 taxonomy maps to.
- **[MLCommons AILuminate benchmark](https://mlcommons.org/benchmarks/ailuminate/)** — the industry benchmark for hazard-and-benign accounting. Use as an anchor when the FGAC's own benchmark set is authored.
- **[HarmBench (Mazeika et al., 2024)](https://arxiv.org/abs/2402.04249)** and **[JailbreakBench (Chao et al., 2024)](https://arxiv.org/abs/2404.01318)** — standardised adversarial-prompt benchmarks the survival-curve evaluation runs against.
- **[Anthropic — Constitutional Classifiers (Sharma et al., 2025)](https://www.anthropic.com/research/constitutional-classifiers)** — the load-bearing reference for adaptive-attack survival as the primary metric.
- **[NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)** — the reference for attack-class terminology the FP / FN report uses. <!-- needs-research: pin the final publication reference for NIST AI 100-2 E2025 and its section numbering. -->

## The evaluation set — benign and adversarial

The FGAC's section-4 numbers are measured against the deployment's own evaluation set. The set is authored, not inherited. Two components:

### The benign set

Ordinary, legitimate user traffic against the deployment. Constructed from:

- **Production traffic samples** — real user requests, sampled with the operator's data-governance approval, PII-scrubbed, consent-cleared. This is the ground truth for *what the deployment's users actually do*.
- **Product-manager-authored scenarios** — the specific benign use cases the product manager needs the deployment to handle. Regression-style prompts that must pass; if a layer regresses on these the product manager gets paged.
- **Public benign corpora** — AILuminate's benign set, MMLU-style academic benchmarks where they intersect with the deployment's scope, general-conversational corpora. Anchors the benign distribution against a common reference.

The benign set is the FP-rate denominator. It has to be *representative* of the deployment's users; a benign set drawn only from developer prompts under-represents the customer's distribution.

### The adversarial set

Prompts constructed to test the FGAC's ability to catch the FGAC's section-1 hazards. Constructed from:

- **HarmBench and JailbreakBench** — standardised adversarial-prompt benchmarks with a documented taxonomy.
- **AILuminate's adversarial set** — hazard-category-labelled adversarial prompts aligned to MLCommons taxonomy.
- **mod-104 output** — jailbreak-engineering prompts specific to the deployment's primary model.
- **mod-111 output** — automated red-team prompts against the deployment's FGAC. The load-bearing tail of the adversarial set.
- **Prior-incident corpus** — real jailbreak attempts observed in production. The distribution the deployment actually faces.

The adversarial set is the FN-rate denominator. Its *distribution* matters at least as much as its *size*: an adversarial set that is 10 000 paraphrases of one attack type over-represents that type and under-measures the rest.

### The size and refresh cadence

- **Size.** Rule of thumb for frontier-scope FGACs: thousands of benign examples per hazard class, thousands of adversarial examples per hazard class. Below hundreds per class the confidence intervals on the reported numbers are too wide to defend to a reviewer.
- **Refresh cadence.** Monthly or on-major-change (specification update, classifier retrain, vendor swap). The adversary's distribution shifts; the evaluation set has to follow.
- **Held-out discipline.** A subset of the benign and adversarial sets is *never* used for training data (chapter 03 or chapter 04). The held-out subset is the FGAC's section-4 evaluation; using it as training data collapses the number to a training-error report.

## Per-hazard-class TP / FP / TN / FN accounting

For each hazard class in the FGAC's section-1 taxonomy, for each layer or for the composed FGAC, the accounting reports:

- **True Positives (TP).** Adversarial-harmful examples the layer / stack flagged correctly.
- **False Positives (FP).** Benign examples the layer / stack incorrectly flagged. The number the product manager reads first.
- **True Negatives (TN).** Benign examples the layer / stack correctly did not flag.
- **False Negatives (FN).** Adversarial-harmful examples the layer / stack did not flag. The number the safety review reads first.

From these:

- **Precision (positive predictive value).** TP / (TP + FP). *Of what the classifier flagged, what fraction was actually harmful?* Low precision drives user complaints.
- **Recall (sensitivity, TPR).** TP / (TP + FN). *Of the actually-harmful, what fraction did the classifier catch?* Low recall drives safety-case failures.
- **False-positive rate on benign.** FP / (FP + TN). *Of the benign population, what fraction gets flagged?* This is the product-manager's benign-user-overhead metric.
- **F1.** Harmonic mean of precision and recall. Useful summary; not sufficient alone.
- **PR-AUC.** Area under the precision-recall curve as the threshold varies. The threshold-tuning input.

The report shape reports these per-hazard-class *and* aggregated across classes. Aggregate F1 without per-class breakdown hides the class where the FGAC is failing.

### Reporting confidence intervals

Numbers without confidence intervals are opinions. Report the bootstrap 95% CI on each precision / recall number; when the CI's lower bound is unacceptable, the number itself is not the story — the sample size is. The product manager reads the CI; the safety review requires it.

## The adaptive-attack survival curve

Chapter 04 pinned this as the load-bearing metric. Here is the reporting shape:

- **X-axis.** Red-team effort in a documented unit (hours, prompts submitted, API calls). Include both automated-red-team volume (mod-111) and human-red-team hours; keep them separable.
- **Y-axis.** The FGAC's recall on the successful attacks the red-team discovered — *conditional* on the primary model producing a harmful response. The FGAC's recall on successful attacks is the number the safety review reads.
- **Baseline curve.** The primary model without the FGAC, or with a baseline classifier only, evaluated against the same red-team effort. The gap between the FGAC curve and the baseline curve is the FGAC's *marginal contribution* — the number mod-109's safety case cites.
- **Per-hazard-class curves.** Aggregated survival hides class-specific regressions. Report per-class curves alongside the aggregate.
- **Regression alerts.** When a specification update, classifier retrain, or vendor swap shifts the curve downward on any class, mod-108's monitors alert. The alert is an FGAC section-4 event.

The curve is the primary evidence in the FP / FN report for the *how does the FGAC hold under pressure?* question. Static-benchmark F1 answers *how does the FGAC hold under known attacks?* Both are reported; the survival curve is the load-bearing one.

## Latency, cost, and benign-user overhead

Effectiveness has a cost. The FGAC's section-4 numbers include:

### Latency

- **Per-layer p50 and p95.** Measured on the deployment's traffic profile, on the deployment's own hardware, at the deployment's own batch size. Vendor-published numbers are inputs; the operator's own numbers are the field.
- **End-to-end p50 and p95.** The sum of layer latencies plus composition overhead. This is what the user experiences.
- **Latency budget.** The FGAC's section-4 pins a target; monitors alert on breach. Common targets: 200–500 ms end-to-end guardrail overhead for chat, less for streaming.
- **Streaming-vs-blocking.** For streaming responses, latency accounting is per-token or per-utterance-boundary. The composition semantics may require buffering; buffering is a latency cost.

### Cost

- **Per-call cost.** The sum of per-layer inference costs plus any vendor per-call charges. Reported per 1 000 calls to match the operator's cost accounting.
- **Amortised training and re-training cost.** Chapter 03's fine-tune cost and chapter 04's specification-driven-generation cost, amortised per call over the retrain cadence.
- **Amortised human labelling and red-team cost.** The costs of the human work that keeps the classifier defensible. Real dollars; not overhead.
- **Cost budget.** The FGAC's section-4 pins a target; monitors alert on breach. Common shapes: guardrail cost as a percentage of primary-model cost (10–20% is typical at frontier scope), or as an absolute cost per call.

### Benign-user overhead

The product-manager-facing number. What fraction of legitimate calls are:

- **Refused entirely.** The FGAC blocked; the user did not get their answer.
- **Rewritten or degraded.** A rule guard or output classifier rewrote the response; the user got a lesser answer.
- **Latency-affected.** The call took longer than the ungarded baseline; the user waited.
- **Escalated.** The call routed to a safety-monitor sidecar or human review; the user waited longer.

Benign-user overhead is what makes the FGAC's cost feel real to the customer. A 2% refusal rate on benign traffic is 20 000 refused users per million requests — the product manager will hear about it.

## The FP / FN report shaped for a product manager

The report is the deliverable of the effectiveness-measurement discipline. It is *shaped for a product manager*, meaning:

- **Grouped by hazard class the product manager understands.** Not "LLM01 Prompt Injection" — "attempts to make the assistant reveal customer data via injected instructions." Translate the taxonomy.
- **FP examples the product manager can read.** For each hazard class, ten to twenty specific *benign* prompts the FGAC incorrectly flagged. The product manager needs to see whose experience is hurt.
- **FN examples the product manager can act on.** For each hazard class, ten to twenty specific *adversarial* prompts the FGAC missed. Redacted if the payload is sensitive; the *pattern* is the actionable field.
- **Precision / recall / FP-rate / benign-refusal-rate per class.** With confidence intervals. Without the CI, the numbers are unactionable.
- **Trend over releases.** How this quarter's FGAC compares to last quarter's. Regressions on any class are called out.
- **Cost and latency deltas.** How much the FGAC costs to run; how much latency it adds; how those numbers compare to targets.
- **The residual-risk statement.** Which hazard classes the FGAC does not fully catch, at what recall, and what the residual-risk claim (mod-109) says about them.
- **Actionable levers.** The specific configuration changes — threshold adjustments, layer swaps, specification updates — that would shift a number and their cost. This is what the product manager can trade.

The report is signed by the safety engineer and reviewed by the product manager, the safety-review body, and (where relevant) legal / compliance. It is a versioned artefact; each release ships one.

## Common evaluation pitfalls to avoid

- **Reporting only aggregate F1.** Aggregate hides the failing class. Always report per-class alongside aggregate.
- **Reporting only static-benchmark performance.** Static is the training-time signal. The deployed-scope signal is the adaptive-attack survival curve. Report both.
- **Reusing training data as evaluation data.** The number is training error, not generalisation error. The held-out discipline exists for a reason.
- **Reporting numbers without confidence intervals.** Sample size below hundreds per class produces uninterpretable numbers. Compute the CI; report it.
- **Reporting the vendor's benchmark as the deployment's benchmark.** Vendor benchmarks are one input; the deployment's own is the field. Do not conflate.
- **Reporting only accept / refuse rate without cost and latency.** Effectiveness has a cost; the report includes both sides.
- **Reporting only the FGAC's numbers without the baseline.** The FGAC's *marginal* contribution — over the primary model's own safety-tuning — is the number the safety case cites. Without the baseline, the marginal is unknowable.

## Boundaries

The mod-108 discipline has clear boundaries to the modules and roles around it.

### Boundary to `ai-risk-engineer` (prerequisite, level 25)

The `ai-risk-engineer` learning track carries the base-depth introduction to **NeMo Guardrails**, **Guardrails AI**, **Presidio**, **Llama Guard**, **ShieldGemma**, and **OpenAI Moderation**. It teaches *what each tool is, how to wire it in, what a starter deployment looks like, what the vendor's own documentation says*. mod-108 assumes that depth and does not re-teach it. What mod-108 adds — the *frontier-scope* extension — is:

- The FGAC as the composition contract.
- The classifier-training loop and the adaptive-attack survival curve.
- The Constitutional Classifiers methodology as the frontier-scope adaptive-attack answer.
- The build-vs-buy matrix as the vendor-decision discipline.
- The effectiveness-measurement report shaped for a product manager.

A safety-engineer role that skips the `ai-risk-engineer` prerequisite and starts at mod-108 will struggle to author the FGAC's section-2 layer inventory (they will not know what the tools are). Route the reader back to the prerequisite when this comes up.

### Boundary to mod-107 (Excessive-Agency Containment)

mod-107's EACC narrows the runtime; mod-108's FGAC narrows the content surface. They compose:

- Layer 5 (post-tool-response validator) plugs into mod-107's monitored-wrapper posture.
- Layer 6 (safety-monitor sidecar) consumes mod-107's tamper-evident audit stream.
- Layer 6 verdicts feed the mod-107 chapter-05 kill-switch fire-vote.

The two contracts are joint deliverables of the safety-engineering role. A frontier-scope deployment ships both; neither alone is sufficient.

### Handoff to mod-109 (Safety Cases)

The FGAC's section-4 performance contract and section-7 evidence-emission contract are the primary inputs to the *control-argument* leg of a safety case. mod-109 authors the argument that reads *"the deployment is unable to admit [class] because the FGAC's layers catch it at [recall] survival across [effort]."* mod-108 produces the numbers; mod-109 authors the argument.

The FP / FN report is one of the artefacts the safety case cites. The adaptive-attack survival curve is another. Both are FGAC section-4 fields shaped for mod-109 consumption.

### Handoff to mod-111 (Automated and Scaled Red Team)

mod-111 hammers the FGAC directly. Every classifier layer, every rule guard, every vendor product is a target. mod-111's output flows back to mod-108 as:

- Training data for chapter 03's fine-tune loop.
- The adaptive-attack survival curve's red-team-effort axis.
- The specification-update input for chapter 04's methodology.
- The FN examples in the product-manager report.

The two modules are joint owners of the iteration loop. mod-111 owns the attack side; mod-108 owns the defence side; the shared artefact is the FGAC's section-4 numbers.

### Handoff to mod-112 (Program and Disclosure)

When the FGAC fails a user — a benign user refused, an adversarial user gets through and elicits a harm — the incident enters mod-112's disclosure workflow. The FGAC's section-7 evidence-emission is the disclosure-input schema:

- Which layer verdicts fired on the incident session.
- Which classifier confidences on the specific input / output.
- Which rule-guard rules matched.
- Which sidecar-monitor retrospective classification applied.

mod-112 shapes the disclosure; mod-108 emits the evidence. FP / FN numbers from this chapter's report are inputs to the regulator-facing disclosure narrative when the FGAC's failure crosses a disclosure threshold (EU AI Act Article 73, industry-specific reporting obligations).

## Common misreadings to avoid

- **"The measurement is a one-off."** It is a lifecycle discipline. The FGAC's numbers are re-measured on each release; the report is versioned; the trend is what a safety review reads.
- **"The product manager just wants a number."** The product manager wants an *actionable* report. A single number is not actionable; per-class numbers with example FPs and FNs and cost / latency deltas are.
- **"Static benchmarks are enough for the shipping decision."** They are enough for the *shipping-the-first-version* decision. They are not enough for the *keeping-the-version-shipped* decision. Adaptive-attack survival is what tells you when the version has to be replaced.
- **"Adaptive-attack survival is only relevant at frontier scale."** It is *most* important at frontier scale, but any deployment at meaningful risk should have some adaptive-attack accounting. mod-111's automated-red-team harness scales down; the survival curve at smaller scale is still informative.
- **"The FGAC's number is the vendor's number times the classifier's number."** No. Composition is not multiplication of independent numbers; layers correlate. The FGAC's number is measured on the *composed stack*, not derived from layer-wise numbers.
- **"Boundaries to other modules are documentation; they don't affect what I build."** The boundaries are what tell you what *not* to build. mod-108 does not build the sandbox, the kill switch, the safety case, or the disclosure runbook. Knowing the boundaries is what keeps the module's scope defensible.

## Summary

- Guardrail effectiveness is a *shaped report*, not a single number. Per-hazard-class **TP / FP / TN / FN**, **precision / recall**, **F1**, and **PR-AUC** against a deployment-specific benign + adversarial set, with confidence intervals; **adaptive-attack survival curves** against mod-111's red-team; **latency (p50 / p95)** and **cost per 1 000 calls**; **benign-user overhead** (refused, rewritten, escalated, latency-affected).
- The **FP / FN report** for the product manager translates the taxonomy, shows specific FP and FN examples, shows trend over releases, shows cost and latency deltas, states the residual risk, and names actionable levers. It is versioned and signed.
- Boundaries: `ai-risk-engineer` (level 25) carries base-depth on the tools; mod-107 and mod-108 are joint safety-engineering contracts (EACC + FGAC); mod-109 cites the FGAC as safety-case evidence; mod-111 hammers the FGAC and feeds its output back; mod-112 discloses when the FGAC's failure crosses a threshold.
- The measurement is a lifecycle discipline. Refresh the evaluation set monthly or on-major-change; retrain the classifiers on that cadence; re-measure the survival curve; ship a new FP / FN report each release.
- Publish confidence intervals. Report per-class alongside aggregate. Compare against a baseline. Report both static benchmarks and adaptive-attack survival. Cite deployment-specific numbers, not vendor-published ones. Every one of these is a specific finding a reviewer will catch if omitted.
