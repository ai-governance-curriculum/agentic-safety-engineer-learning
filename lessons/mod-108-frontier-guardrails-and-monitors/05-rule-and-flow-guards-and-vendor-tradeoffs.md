# 05 — Rule and Flow Guards and Vendor Trade-offs

## Motivation

Chapters 02–04 built the model-based classifier layers of the FGAC. Chapter 01 named a distinct layer class — the rule and flow guards — that catches what the classifiers cannot: deterministic policies, structured-output constraints, and *conversation-shape* invariants the model must honour turn-over-turn. This chapter walks that layer class and pairs it with a second question every frontier-scope operator has to answer: *for the layers where multiple vendor products exist, when do we build and when do we buy?*

Rule guards and vendor products are the two chapter-05 topics because they share a discipline. Both are *composable primitives* rather than trained models. Both have vendor ecosystems with real product differentiation. Both are the layers where an unstructured buying decision costs the most: buying the wrong rule engine leaves the FGAC's section-3 data-flow contract with a gap; buying the wrong vendor product leaves the operator with a contract they cannot exit and a layer whose failure modes they cannot inspect.

The chapter's discipline: (a) understand what NeMo Guardrails, Guardrails AI, and Presidio actually do — the `ai-risk-engineer` prerequisite carries the base-depth introduction; (b) understand where their placements sit in the FGAC's section-3 data-flow contract; (c) author a defensible build-vs-buy matrix that names the specific criteria a vendor decision is scored on; (d) structure a vendor evaluation that produces evidence a safety review will sign.

## Primary sources

- **[NVIDIA NeMo Guardrails — documentation](https://docs.nvidia.com/nemo/guardrails/latest/)** — the current documentation for the framework, including Colang, the rail types (input / output / dialog / execution / retrieval), and integration patterns. <!-- needs-research: pin the current released NeMo Guardrails version and any changes to the rail taxonomy at authoring time. -->
- **[NVIDIA NeMo Guardrails on GitHub](https://github.com/NVIDIA/NeMo-Guardrails)** — source, sample flows, and community-contributed configurations.
- **[Guardrails AI — documentation](https://www.guardrailsai.com/docs)** and **[Guardrails AI Hub](https://hub.guardrailsai.com/)** — the validator library, the RAIL specification format, and the Hub's community validators. <!-- needs-research: confirm current Hub structure and validator categorisation. -->
- **[Guardrails AI on GitHub](https://github.com/guardrails-ai/guardrails)** — source and integration examples.
- **[Microsoft Presidio — documentation](https://microsoft.github.io/presidio/)** — the open-source PII detection and anonymisation library. Read for the analyser / anonymiser split and the recognisers.
- **[Microsoft Presidio on GitHub](https://github.com/microsoft/presidio)** — source and the current recogniser catalogue.
- **[OWASP GenAI — LLM and Generative AI Security Solutions Landscape](https://genai.owasp.org/resource/llm-and-generative-ai-security-solutions-landscape/)** — the OWASP-maintained landscape of vendor and open-source guardrail products. Cite when comparing vendors. <!-- needs-research: confirm the current OWASP landscape edition and the vendors it lists at authoring time. -->

## What each of the three primary tools is

The `ai-risk-engineer` prerequisite (level 25) covers these tools at introduction depth. The recap here is the *shape* the FGAC's section-2 layer inventory pins on.

### NVIDIA NeMo Guardrails

A framework for programming *dialog-shaped* guardrails around an LLM application. The primitives:

- **Input rails.** Rules that fire on the user's input before the primary model sees it. Pattern-match, invoke a classifier, invoke a fact-check tool, refuse-with-a-canned-message. Placement: FGAC layer 1 or layer 2 wrapper.
- **Output rails.** Rules that fire on the primary model's response before the user sees it. Refuse on regex match, redact PII, invoke a fact-check tool, rewrite. Placement: FGAC layer 4 wrapper.
- **Dialog rails.** Multi-turn conversation-flow rules encoded in Colang, NeMo's domain-specific language. "If the user asks about a competitor, hand off to the topic-refusal flow." Placement: wraps the whole loop.
- **Execution rails.** Rules that fire on tool invocation — pattern-match arguments, refuse categories of calls, log for review. Placement: composes with the mod-107 monitored-wrapper posture at the tool boundary.
- **Retrieval rails.** Rules that fire on retrieval-augmented content before it enters the prompt. Placement: FGAC layer 5 (post-tool-response validator).

NeMo Guardrails is the most fully-featured open-source rule engine for LLM applications. It handles the *composition* of pattern-matched rules, classifier calls, and multi-turn state. It is licensed under Apache-2.0.

### Guardrails AI

A framework for *structured-output validation and constraint* around LLM calls. The primitives:

- **RAIL specification.** A declarative document describing the expected schema of the LLM's output and the validators the output must pass.
- **Validators.** Composable checks — regex, type, range, PII, toxicity, factuality, custom Python. A community Hub adds more.
- **Guarded LLM calls.** The framework wraps the LLM call, applies the validators to the output, and either passes, refuses, or *re-asks* the LLM when the output fails validation.

Guardrails AI's differentiator is the *re-ask* loop and the structured-output focus. It sits well at the FGAC's layer 4 (output-side) when the deployment's output is structured; it composes with model-based classifiers rather than replacing them.

### Microsoft Presidio

An open-source PII detection and anonymisation library. Two primary components:

- **Presidio Analyzer.** Detects PII entities in text using named-entity recognisers, regex patterns, and denylists. Ships with a catalogue of recognisers covering common PII shapes; extensible with custom recognisers.
- **Presidio Anonymizer.** Redacts or replaces detected entities. Multiple anonymisation strategies (redaction, masking, encryption).

Presidio's placement is FGAC layer 1 (pre-input filter) for input-side PII stripping and layer 4 (output classifier / rewriter) for output-side PII redaction. It is licensed under MIT.

## Placement discipline for rule and flow guards

The three tools do not cover the same slot. A defensible FGAC uses them in specific placements the section-3 data-flow contract pins.

### NeMo Guardrails as the flow-shape backbone

NeMo Guardrails is best-in-class for *multi-placement flow orchestration*. When the FGAC needs a single tool that provides input rails, output rails, dialog rails, and execution rails with shared state, NeMo is the default. The trade-off: NeMo's Colang has a learning curve; the flow authoring is code-shaped, not spec-shaped; and the framework's release cadence is set by NVIDIA rather than by the operator.

Use NeMo when the deployment has multi-turn dialog structure the FGAC's section-1 hazard taxonomy is sensitive to (multi-turn injection, cumulative-context exfil, roleplay progressions). Don't use NeMo when the deployment is a single-turn request / response and the FGAC's layers are placed at the individual-call boundary.

### Guardrails AI as the structured-output validator

Guardrails AI's re-ask loop and structured-output focus make it the default when the deployment's output *is* structured (JSON, XML, code, structured markdown) and the validation is on the *schema and content of the output*, not on the conversation shape.

Use Guardrails AI when the primary model's output is structured and the FGAC's section-1 hazard taxonomy includes schema-violation classes (leaked structured PII, secrets in emitted code, out-of-schema tool arguments). Don't use Guardrails AI when the output is free-form prose; the framework's strengths are wasted.

### Presidio as the PII layer

Presidio is a *dedicated* PII layer. When the FGAC's section-1 taxonomy has PII disclosure as a hazard class (it almost always does), Presidio fills the pre-input filter (layer 1) and often the output rewriter portion of layer 4. It is not a rule engine for arbitrary policy; it is a PII engine. Use it as such.

### Composing the three

The three tools are not alternatives; they are *complements*. A frontier-scope FGAC often uses all three:

- Presidio as a dedicated PII layer at the pre-input filter and the output rewriter.
- NeMo Guardrails as the flow-shape backbone, invoking classifiers and rule sets across the dialog.
- Guardrails AI as the structured-output validator when the model's output is structured.

A common mistake is to pick one and stretch it across all three placements. NeMo is a poor PII engine; Guardrails AI is a poor dialog framework; Presidio is not a rule engine. Use them for what they are.

## The vendor ecosystem

Beyond the open-source tools, a set of commercial vendors ship guardrail products at various levels of integration. The following are the vendors the operator's product manager will name in a build-vs-buy conversation. At authoring time, the specific product features and pricing shift frequently; every claim below carries a `needs-research` mark you must resolve when the FGAC's vendor-eval section is authored.

- **[Lakera Guard](https://www.lakera.ai/lakera-guard)** — commercial guardrail product with a focus on prompt-injection detection and content moderation. <!-- needs-research: confirm current product feature set, deployment models (SaaS / self-hosted / hybrid), pricing tiers, and any published third-party evaluations. -->
- **[HiddenLayer AIDR](https://hiddenlayer.com/)** — AI Detection and Response product covering runtime protection against adversarial ML attacks and prompt injection. <!-- needs-research: confirm the current AIDR product's scope, integration model, and any published detections against a standard benchmark. -->
- **[Protect AI Guardian](https://protectai.com/)** — model-scanning and runtime-protection product; Guardian is the runtime layer at authoring time. <!-- needs-research: confirm current Protect AI product line (Guardian / Radar / Layer / others) and their scope. -->
- **[Robust Intelligence](https://www.robustintelligence.com/)** — the AI Firewall product for real-time input / output filtering. <!-- needs-research: confirm current product features and any recent acquisitions / rebranding. -->
- **[CalypsoAI](https://calypsoai.com/)** — LLM security and content moderation platform. <!-- needs-research: confirm current product name, features, and deployment posture. -->
- **[HydroX AI](https://hydrox.ai/)** — LLM security platform with a focus on responsible-AI compliance. <!-- needs-research: confirm current product features and enterprise integrations. -->

The list is not exhaustive; it captures the vendors most frequently proposed to safety-engineering roles. The OWASP GenAI Security Solutions Landscape maintains a broader catalogue; consult it when the FGAC's vendor eval is authored.

## The build-vs-buy matrix

The build-vs-buy decision is not a vibe. It is a scored comparison across a fixed set of criteria. The matrix has a row per layer slot and a column per candidate; the cells hold specific evidence-backed scores.

### The load-bearing criteria

For each layer slot, evaluate every candidate (build option + each vendor option) against:

- **Hazard-coverage fit.** Does the candidate cover the specific hazard classes the FGAC's section-1 taxonomy names? Not "coverage of some hazard categories" — coverage of the specific classes in *your* deployment's taxonomy.
- **Per-class precision / recall on the deployment's benchmark.** The same accounting chapter 02 pins. Vendor-published numbers are inputs; the deployment's own numbers are the field.
- **Adaptive-attack survival.** Whether the vendor has evidence of survival against a public adaptive-attack benchmark (HarmBench, JailbreakBench) or against a red-team the vendor commissioned. If not, the vendor's product is a *static-benchmark* layer only.
- **Latency (p50 / p95).** Measured against the deployment's traffic profile, not the vendor's benchmark.
- **Cost.** Vendor pricing (per-call, per-seat, tier-based). Include integration cost, ongoing maintenance cost, and the amortised cost of any vendor-specific engineering the deployment absorbs.
- **Deployment model.** SaaS-only, self-hosted, hybrid, on-prem. The operator's data-handling posture may forbid SaaS for some categories; if the vendor is SaaS-only for a hazard class the operator cannot exfil, the vendor is not viable.
- **Data-flow implications.** Does the vendor receive user data, prompts, and responses? What jurisdictional flow does that entail (EU→US data transfer, cross-border processing)? What is the vendor's retention posture on the operator's data?
- **Transparency.** Can the operator inspect the vendor's classifier internals, taxonomy, training data, or evaluation set? Commercial vendors typically say no. This is the transparency cost; the FGAC's section-7 evidence-emission contract has to work around it.
- **Vendor viability.** Company stage, funding, customer base, product-support commitments, published SLA. Vendor-availability risk is a real cost.
- **Contract terms.** Termination clauses, data-return clauses, escrow of the vendor's runtime for continuity, indemnification. Legal and procurement are peer-role handoffs; the FGAC records what was agreed.
- **Regulator posture.** Where the deployment's regulator has a view of what constitutes an adequate guardrail (EU AI Act obligations under Articles 55 / 56 for general-purpose AI, industry-specific regulators for financial services or healthcare), the vendor's product may or may not carry standing evidence.

### Weighting

Not all criteria are equal for every deployment. A regulated financial-services deployment weights the data-flow implications and the regulator posture heavily; a research-preview deployment weights adaptive-attack survival and latency heavily; a healthcare deployment weights per-class recall on medical-content hazards.

The weighting is an FGAC section-2 field per layer slot. Publish it; make it defensible; do not obscure it.

### Scoring evidence

Each cell of the matrix carries *evidence*, not an opinion. Numbers come from:

- The operator's own evaluation of the vendor (a paid POC, a benchmark run against the vendor's endpoint, a shadow-mode deployment reading production traffic).
- The vendor's published documentation (with citation).
- Third-party evaluations (with citation).
- Published benchmarks where the vendor has entered (HarmBench, JailbreakBench, AILuminate; where the vendor has *not* entered a public benchmark, that absence is itself a score).

An unscored cell is not zero; it is *unknown*. A defensible matrix has few unknowns; the FGAC records which unknowns the deployment shipped with and which the residual-risk claim (mod-109) covers.

## Rule-guard authoring discipline

Rule guards look deceptively simple: a regex, a Colang flow, a validator spec, and you have a layer. The failure modes are less visible than the model-based classifiers' and often surface only under adversarial pressure.

- **Rule-set drift.** Rule guards accumulate. A rule shipped for one incident stays after the incident is fixed; a rule shipped for a competitor mention stays after the competitor pivots. Un-culled rule sets grow into an unauditable list. Cadence a review — quarterly at minimum — where the safety engineer sits with the rule set and prunes.
- **Regex overreach.** A regex written to catch a specific harm phrase catches paraphrases in production nobody anticipated. The FP rate on benign traffic is the check; a regex whose benign-FP rate is above a threshold is a regex that has to be re-scoped.
- **Colang flow-state explosion.** NeMo's dialog rails have state; complex conversation trees produce state explosions the safety engineer cannot reason about at review time. Prefer flat rule sets and let the composition semantics (FGAC section 5) do the multi-rule reasoning; complex dialog trees are an anti-pattern.
- **PII recogniser gaps.** Presidio's default recogniser catalogue does not cover jurisdiction-specific PII shapes (e.g., specific national ID formats, industry-specific record identifiers). The FGAC's section-1 taxonomy names the PII classes the deployment must catch; the missing recognisers are a build item.
- **Rule provenance.** Every rule has an owner and a justification. A rule without both is a rule that gets stale, drifts, and is never removed. The FGAC's section-2 layer inventory records rule provenance per rule set.

## Structuring a defensible vendor evaluation

The vendor eval is the pass that populates the matrix's cells. Structure:

1. **Define the evaluation's benchmark set** before the vendor conversation. A benign set drawn from the deployment's own production traffic (PII-scrubbed, consented); an adversarial set drawn from HarmBench + JailbreakBench + mod-111 output. The benchmark set is the operator's, not the vendor's.
2. **Give the vendor a private benchmark subset** to configure against. This mirrors a real deployment; every vendor tunes their product to the operator's specific corpus.
3. **Run the *held-out* benchmark yourself** through the vendor's product. The results are the FGAC's section-4 fields for the vendor row.
4. **Run an adaptive-attack pass.** mod-111's red-team engages the vendor's endpoint. The survival curve is a matrix cell.
5. **Measure latency and cost on the operator's traffic profile.** Not on a synthetic load-test.
6. **Interview the vendor on transparency, data flow, and support.** Non-numeric criteria; capture the answers with citations to the vendor's written commitments.
7. **Compare to a build baseline.** The chapter-03 fine-tuned classifier is the build option; its performance on the same benchmark set is the build-baseline row.
8. **Publish the matrix as an FGAC section-2 attachment.** Sign it. Route it to peer review (procurement, legal, security architecture) before shipping.

A vendor eval that stops at step 3 is not defensible; the vendor's endpoint on the operator's benchmark is one column of the matrix, not the whole matrix.

## Common misreadings to avoid

- **"NeMo Guardrails is a classifier alternative."** No. NeMo is a *flow orchestrator*; it invokes classifiers as one of its primitives. A NeMo-shaped stack that does not include a classifier layer is missing chapter 02's discipline.
- **"Guardrails AI covers PII, so we don't need Presidio."** Guardrails AI's PII validators are, at authoring time, wrappers around library-provided detectors — often Presidio itself. Adopting Guardrails AI does not obviate the PII-layer engineering; it shifts where the configuration lives.
- **"The vendor's benchmark numbers are our numbers."** No. The vendor's numbers are on the vendor's benchmark. The FGAC's numbers are on the deployment's benchmark. Every row of the matrix has a *deployment-specific* number, not a vendor-published one.
- **"Buy is always faster than build."** Buy is faster to *ship the first version*. Over the classifier's lifecycle (retraining cadence, adaptive-attack survival, vendor-lock-in cost), the build option may be cheaper. The matrix scores the *lifecycle* cost, not just the sticker.
- **"We can adopt a vendor and skip the FGAC's section-4 numbers."** The vendor is a *layer implementation*; the layer's performance contract is the FGAC's, not the vendor's marketing collateral. mod-108's monitors alert on the FGAC's numbers.
- **"Vendor lock-in is a legal problem, not a safety-engineering problem."** It becomes a safety-engineering problem when the vendor changes their taxonomy, deprecates a category, or shuts down. The FGAC's section-8 change-management contract has to include a *vendor-exit path*; not having one is a residual-risk claim mod-109 has to accept.

## Interfaces to the rest of the module

- **Chapter 01** — this chapter's tools fill layer 1 (Presidio), layer 3 (NeMo, Guardrails AI), layer 4 (Guardrails AI, Presidio output-side), layer 5 (NeMo retrieval rails) of the FGAC's layer inventory.
- **Chapter 02** — vendor products often bundle a classifier. When they do, chapter 02's per-class accounting is the evaluation lens for the vendor's classifier row.
- **Chapter 03** — the build baseline for the matrix is a fine-tuned classifier authored per chapter 03. The matrix compares vendor rows against this baseline.
- **Chapter 04** — the vendor's adaptive-attack survival row (where it exists) is compared against the constitutional-classifier methodology's numbers.
- **Chapter 06** — the FP / FN report cites the matrix's per-vendor numbers when explaining the FGAC's composition to a product manager.
- **mod-107** — NeMo execution rails compose with mod-107's monitored-wrapper posture at the tool-invocation boundary; the FGAC's section-3 data-flow contract pins the interface.
- **mod-109** — the vendor matrix's unknowns and vendor-lock-in risks are inputs to the residual-risk claim in the safety case.
- **mod-112** — vendor termination and vendor-side incidents are disclosure events; mod-112 owns the workflow.

## Summary

- Rule and flow guards are the FGAC's non-model layer class. **NeMo Guardrails**, **Guardrails AI**, and **Presidio** are the load-bearing open-source primitives, each with a specific placement. Use all three for what they are; do not stretch one across the others' placements.
- The `ai-risk-engineer` prerequisite (level 25) covers *what* each tool is; this module covers *how to compose them at frontier scope* and *how to evaluate vendor alternatives*.
- The vendor ecosystem is real and moving. Commercial products from **Lakera**, **HiddenLayer**, **Protect AI**, **Robust Intelligence**, **CalypsoAI**, **HydroX AI**, and others compete in the same slots. Every product feature claim at authoring time is a `needs-research` mark.
- The **build-vs-buy matrix** is the artefact. Rows are layer slots; columns are candidates (build + vendors). Cells are evidence-backed scores against hazard-coverage, per-class precision / recall, adaptive-attack survival, latency, cost, deployment model, data-flow, transparency, vendor viability, contract terms, and regulator posture. Weightings are FGAC fields.
- Evidence for the matrix comes from the *deployment's own benchmark*, not the vendor's. Static-benchmark performance is a floor; adaptive-attack survival (chapter 04) is the load-bearing metric.
- A defensible vendor eval runs the vendor's product on the operator's benchmark, mod-111's red-team, and the operator's traffic profile, and publishes the results as an FGAC section-2 attachment. Stopping at the vendor's endpoint-on-vendor-benchmark is a finding.
