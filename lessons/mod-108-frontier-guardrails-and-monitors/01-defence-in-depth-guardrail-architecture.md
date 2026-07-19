# 01 — Defence-in-Depth Guardrail Architecture

## Motivation

A guardrail that catches 99% of adversarial prompts, run alone, is a guardrail that fails one call in a hundred. At a million calls a day that is ten thousand failures. The number is not fixable by making the guardrail 1% better; the number is only fixable by *composing* guardrails so that no single failure lets the harmful call through. The interesting engineering question in this module is not *which classifier is best?* It is *what shape does the composition take, where do the layers live, and how does the runtime prove — to a reviewer, to a monitor, to a safety case — that the composition holds?*

Frontier-scale guardrail engineering is defence-in-depth applied to the LLM call-graph. Where mod-107 made the *runtime* narrower than the *model* through capability gates and monitored wrappers, mod-108 makes the *content surface* narrower than the *world* through a stack of pre-input filters, input classifiers, output classifiers, rule / flow guards, post-tool-response validators, and safety-monitor sidecars. Each layer is fallible in isolation. The stack is defensible when every layer's characteristic failure mode is caught by at least one downstream layer, when no layer is a silent single-point-of-failure, and when the composition itself is a versioned artefact a safety review can sign.

This chapter names the module-level artefact — the **Frontier Guardrail Architecture Contract (FGAC)** — and pins the layered shape the remaining chapters detail. Chapter 02 drills into input / output classifiers (Llama Guard, ShieldGemma, OpenAI Moderation, vendor-published Anthropic classifiers). Chapter 03 covers the fine-tuning loop for a custom safety classifier. Chapter 04 covers Anthropic's Constitutional Classifiers methodology and the adaptive-attack-survival evidence they demand. Chapter 05 covers rule / flow guards and the vendor-vs-build matrix. Chapter 06 covers effectiveness measurement, the FP / FN report, and the boundaries handoff to `ai-risk-engineer` (level 25), mod-109, mod-111, and mod-112.

The `ai-risk-engineer` prerequisite (level 25) carries the base-depth introduction to each of these tools — NeMo Guardrails, Guardrails AI, Presidio, Llama Guard, ShieldGemma, OpenAI Moderation — at the *this is what it is and how you wire it in* level. This module does not re-teach that. What it adds is the frontier-scope composition, the classifier-training loop, the adaptive-attack-survival curve, and the defensibility of the whole against thousands of hours of red-team.

## Primary sources

- **[OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)** — the risk taxonomy the FGAC's layers map to. LLM01 (Prompt Injection), LLM02 (Sensitive Information Disclosure), LLM05 (Improper Output Handling), and LLM09 (Misinformation) are the entries every layer's placement is justified against.
- **[OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)** — Tool Misuse, Repudiation & Untraceability, and Cascading Failures name the failure modes composition catches that a single guardrail does not.
- **[NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology](https://csrc.nist.gov/pubs/ai/100/2/e2025/final)** — the attack-class terminology (evasion, extraction, poisoning) the layers defend against. Reference for the risk-decomposition the FGAC uses. <!-- needs-research: verify the exact final-publication date of NIST AI 100-2 E2025 and the section numbering for the LLM-specific attack taxonomy. -->
- **[Anthropic — Constitutional Classifiers: Defending against universal jailbreaks (Sharma et al., 2025)](https://www.anthropic.com/research/constitutional-classifiers)** — the load-bearing paper for adaptive-attack survival as the effectiveness metric. Chapter 04 unpacks the methodology; read the introduction here for the composition framing.
- **[MLCommons AILuminate benchmark](https://mlcommons.org/benchmarks/ailuminate/)** — the industry benchmark for hazard-category coverage. Used in chapter 06 to size the benign / adversarial evaluation set the FGAC's monitors run against. <!-- needs-research: pin the current AILuminate version and its hazard-category enumeration in the FGAC artefact. -->

## The composition problem

A single guardrail is a filter with a precision, a recall, a latency, and a cost. A stack of guardrails is a filter *graph* with a precision, a recall, a latency, and a cost that are *not simple products of the individual layers*. The failures compose in specific ways:

- **False-negative correlation.** If two classifiers are both trained on the same jailbreak corpus, an adversarial prompt that evades one is disproportionately likely to evade the other. Stacking two copies of Llama Guard is not stacking two independent detectors; it is stacking one detector twice.
- **False-positive amplification.** Each layer that flags conservatively adds a false-positive rate. Naive union — "refuse if any layer refuses" — multiplies the benign-user overhead by the number of layers. The composition needs to be *smarter than union*.
- **Latency addition.** A pre-input filter at 5 ms, an input classifier at 40 ms, an output classifier at 60 ms, a rule guard at 5 ms, and a post-response validator at 20 ms is 130 ms of guardrail latency on top of the model call. The FGAC's section on latency is a *budget*, not a wish.
- **Cost multiplication.** Every layer that runs a separate model is a separate inference. For an agent whose model call costs $0.01, a stack that adds two classifier inferences at $0.001 each is a 20% cost hit before any tool-use overhead.
- **Ordering matters.** A pre-input filter that strips PII before the input classifier changes the *distribution* of what the input classifier sees; a rule guard that rewrites arguments changes what the post-response validator has to reason about. The stack is not commutative.

The composition problem is: given a hazard taxonomy, a latency budget, a cost budget, an FP / FN target, and an adaptive-attack survival requirement, *what layered stack satisfies all five and can be shown to satisfy all five under adversarial pressure?* The FGAC is the artefact that answers that question for one concrete deployment.

## The Frontier Guardrail Architecture Contract (FGAC)

Before we drill into layers, name the artefact. The **FGAC** is the module-level deliverable analogous to mod-107's EACC: a versioned, signed, per-deployment contract that names every guardrail layer, its placement, its configuration, its expected precision / recall / latency / cost, its escalation path on fire, and its evidence-emission for downstream monitors.

An FGAC has eight sections:

1. **Hazard taxonomy and coverage matrix.** The enumerated hazard classes the deployment must not admit (drawn from OWASP LLM Top 10, the operator's own risk register, and any regulator-mandated categories — e.g., AILuminate's hazard taxonomy, EU AI Act Annex III entries where the deployment sits in scope). A matrix maps each hazard class to the layers that catch it — and, importantly, flags hazards no layer catches, which the residual-risk claim (mod-109) must cover.
2. **Layer inventory.** Every guardrail layer, versioned, with a one-line intent, a placement (pre-input / input / rule / model / output / post-response / sidecar), a component identity (Llama Guard 3, ShieldGemma 9B, Presidio, NeMo Guardrails, a fine-tuned constitutional classifier, a vendor product), and a build-vs-buy classification.
3. **Ordering and data-flow contract.** The exact sequence of layers on the input path, on the output path, and on the tool-response path. What each layer sees, what each layer modifies, what each layer emits to the sidecar monitor.
4. **Per-layer performance contract.** Expected precision (recall of harmful) and recall (fraction of harmful caught) per hazard class on the deployment's benchmark set, expected false-positive rate on the benign set, expected p50 / p95 latency, expected cost per call. These are *targets against which mod-108 monitors alert*.
5. **Composition semantics.** What does the stack *do* with per-layer verdicts? Refuse-on-any-fire? Weighted-vote? Escalate-to-monitor-on-disagreement? The rule is explicit; ad-hoc composition is a finding.
6. **Escalation contract.** For each fire mode — high-confidence harmful, low-confidence-but-flagged, disagreement-between-layers, adaptive-attack signature match — what does the runtime do? Refuse silently, refuse with reason, log-and-let-through, escalate to a safety-monitor sidecar, escalate to human review, page on-call.
7. **Evidence-emission contract.** What does each layer emit to the mod-107 audit stream and the mod-108 sidecar monitor? Verdicts, confidences, feature vectors, canonical-input hashes. This is the input to mod-109 safety-case evidence and mod-112 disclosure.
8. **Change-management contract.** Who signs a layer swap? How is a threshold change reviewed? What is the rollback path when a layer's false-positive rate spikes in production? Guardrail configuration drift is the classical incident mode; the contract fights it.

Exercise 01 asks you to author the first six sections of an FGAC for one concrete deployment. Chapters 02–05 build the primitives that populate each section; chapter 06 pins the measurement discipline that makes the section-4 performance contract defensible.

The FGAC is the *engineering answer* to the composition problem. mod-107's EACC narrowed the runtime; the FGAC narrows the content surface. Together they are the two contracts a safety review reads before a frontier-model deployment.

## The six layer classes

The remainder of the chapter walks the six load-bearing layer classes. Each subsection names the class, the hazard categories it primarily addresses, its typical placement in the stack, and the *characteristic failure* the layer downstream must catch.

### Layer 1 — Pre-input filters

*What they are.* Deterministic, low-latency checks on the raw input before any model — including a classifier model — sees it. Deny-list token / regex matching for exact known-bad substrings, PII detectors (Presidio, custom regex packs), URL / attachment allow-lists for retrieval-augmented flows, encoding-and-length normalisation (unicode NFC, control-character stripping, size caps).

*What they mitigate.* OWASP LLM02 (Sensitive Information Disclosure) at ingest, LLM01 (Prompt Injection) via known-signature strings, and the base case of oversize / malformed input that would otherwise DoS a downstream classifier. Also the ingest-side of LLM04 (Data and Model Poisoning) for the retrieval-augmentation loop.

*Where they live.* First in the pipeline, before the input classifier and before any model call. Runtime code the model does not see.

*Characteristic failure.* Pre-input filters are *precise but shallow*. A deny-list catches an exact string; it does not catch a paraphrase. A PII regex catches a US SSN; it does not catch a novel PII shape. The downstream input classifier is what catches what the filter misses; the pre-input filter's job is to remove the *known noise* so the classifier can focus on the *unknown signal*.

### Layer 2 — Input classifiers

*What they are.* Model-based classifiers scoring the input against a hazard taxonomy — Meta's Llama Guard family, Google's ShieldGemma, OpenAI's Moderation API, or a fine-tuned in-house classifier (chapter 03). They read the same input the primary model would read; they emit a verdict per hazard class with a confidence.

*What they mitigate.* OWASP LLM01 (Prompt Injection) — including indirect injection through retrieved content when the retrieval output is treated as an input — and any hazard class the taxonomy covers.

*Where they live.* After the pre-input filter, before the primary model. Run concurrently with the primary model call *only* if the composition semantics (FGAC section 5) admit an eventual-consistency posture; run *before* the primary model when the composition requires a fail-closed pre-check.

*Characteristic failure.* Input classifiers are *trained on distributions*. Novel adversarial phrasings — the ones the Constitutional Classifiers paper's red-team surfaces — fall outside the training distribution and are misclassified. The downstream output classifier catches these when they succeed at eliciting a harmful *response*; the sidecar monitor catches them retrospectively.

Chapter 02 covers this layer class in depth. Cite the base-depth `ai-risk-engineer` treatment as prerequisite; this module adds the frontier-scope failure modes and the composition strategy.

### Layer 3 — Rule and flow guards

*What they are.* Programmable rule engines that constrain *conversation and tool-use flow* rather than individual inputs and outputs. NVIDIA NeMo Guardrails uses Colang to encode input rails, output rails, dialog rails, and execution rails. Guardrails AI provides validators — structured-output guarantees, PII redaction, topical constraints — expressed as declarative specs. The domain is *what the agent may say and do, given the conversation state*.

*What they mitigate.* OWASP LLM05 (Improper Output Handling), OWASP LLM06 (Excessive Agency) at the *response-shape* level, and any policy the deployment expresses as a rule rather than a probability. A rule that says *"never disclose the internal knowledge-base index name"* is deterministic; a classifier that scores the response for information disclosure is probabilistic. Both compose.

*Where they live.* Multiple placements. NeMo's *input rails* live between the pre-input filter and the primary model; its *output rails* between the primary model and the output classifier; its *dialog rails* wrap the whole loop; its *execution rails* sit at the tool-invocation boundary and compose with mod-107's monitored wrappers.

*Characteristic failure.* Rule guards are *brittle by design*. They catch what the rule enumerates; they do not catch the paraphrase, the semantic-equivalent, the multi-turn plan that reaches the same outcome. Output classifiers catch these when the rule misses; the sidecar monitor catches them retrospectively.

Chapter 05 covers this layer class in depth and pairs it with the vendor-vs-build matrix (Lakera Guard, HiddenLayer AIDR, Protect AI Guardian, Robust Intelligence, Calypso AI, HydroX AI).

### Layer 4 — Output classifiers

*What they are.* Model-based classifiers scoring the *model's response* against a hazard taxonomy, either the same one as the input classifier or a different one tuned to output-side hazards. Llama Guard's output-classification mode, ShieldGemma's response-scoring mode, Anthropic's output-side Constitutional Classifier, or a custom fine-tuned classifier.

*What they mitigate.* OWASP LLM05 (Improper Output Handling), LLM02 (Sensitive Information Disclosure) via the model's response, LLM09 (Misinformation), and — critically — the hazards that a successful jailbreak of the primary model would emit even when the input classifier passed the input.

*Where they live.* Between the primary model and the user (for chat) or between the primary model and the tool-invocation gate (for agent flows). The output classifier is the last chance to catch a harmful response before it escapes the perimeter.

*Characteristic failure.* Output classifiers are *response-shape sensitive*. A response that *would* be harmful if executed but is expressed as a plan, a hypothesis, or an in-character monologue can slip past. The rule guard catches those the schema forbids; the sidecar monitor catches those the rule missed.

Chapter 02 covers the vendor-published output classifiers; chapter 03 covers the fine-tuning loop; chapter 04 covers the constitutional-classifier methodology that has produced the strongest published adaptive-attack survival curves to date (Sharma et al., 2025).

### Layer 5 — Post-tool-response validators

*What they are.* Structured validators that inspect the *output of a tool call* before it re-enters the model's context. For an agent that reads a document, this layer validates the document text for injection signatures, exfiltration patterns, and hazard content. For an agent that queries a database, this layer validates the row set for PII and blast-radius. For an agent that reads the web, this layer validates the fetched HTML against the retrieval-poisoning threat class.

*What they mitigate.* OWASP LLM01 (Prompt Injection) via indirect injection through retrieved content, LLM02 (Sensitive Information Disclosure) via tool-returned data, and the mod-107 argument-provenance discipline (tool output is high-scrutiny attacker-controllable context).

*Where they live.* Between the mod-107 monitored wrapper's return and the primary model's next-turn context. This is the layer that stops a retrieved document from acting as an injection payload.

*Characteristic failure.* Post-tool-response validators run *after* the tool has committed side effects; they cannot un-commit. mod-107's outcome-verification loop is the composition point — the wrapper detects the side-effect widening; this layer detects the *content* the tool returned. Both are load-bearing.

### Layer 6 — Safety-monitor sidecars

*What they are.* Asynchronous, retrospective classifiers that consume the audit-log stream (mod-107 wrappers' output) and score full sessions, multi-turn trajectories, and tool-use graphs for hazards that no *inline* layer could catch in one call. A sidecar monitor sees the whole session; an inline layer sees one call.

*What they mitigate.* Multi-turn adversarial-alignment behaviours the mod-110 taxonomy names (deception, sandbagging, sabotage across turns); slow-and-low exfiltration the inline classifiers miss because no single turn crosses a threshold; the *residual* hazard the inline stack admits.

*Where they live.* Off the request path, consuming the audit-log stream mod-107 chapter 03 emits. Their verdicts feed the mod-107 chapter 05 kill-switch fire-vote, the mod-108 effectiveness dashboard (chapter 06), and the mod-109 safety-case evidence stream.

*Characteristic failure.* Sidecars are *retrospective*. They cannot prevent the call that already happened; they can only detect it, alert on it, and inform the next-session policy. This is why the FGAC composes sidecars *with* inline layers, never as their replacement.

## The composition graph

The six layer classes compose into a graph the FGAC's section-3 data-flow contract pins:

```
[user input]
    -> pre-input filter (layer 1)
    -> input classifier (layer 2)         --refuse--> [structured refusal]
    -> NeMo input rails (layer 3)         --refuse--> [structured refusal]
    -> primary model
    -> NeMo output rails (layer 3)        --refuse-or-rewrite--> [structured response]
    -> output classifier (layer 4)        --refuse--> [structured refusal]
    -> [response to user]

[model wants tool call]
    -> mod-107 monitored wrapper
        -> tool executes
        -> tool response
    -> post-tool-response validator (layer 5) --refuse--> [structured tool error]
    -> [tool response re-enters context]

[all layer verdicts + inputs + outputs + tool trace]
    -> tamper-evident audit log (mod-107 chapter 03)
    -> safety-monitor sidecar (layer 6, async)
        -> classifier / rule / trajectory-scorer
        -> fire-vote to kill-switch (mod-107 chapter 05)
        -> evidence to safety-case (mod-109)
        -> evidence to disclosure (mod-112)
```

The composition is not commutative and the order is not accidental. Cheap deterministic checks (layer 1) run first; expensive probabilistic checks (layers 2, 4) run behind them; flow-shaped rules (layer 3) wrap the model; tool-response validation (layer 5) is a specific placement between mod-107's wrapper and the model's context; the sidecar (layer 6) sees everything but does not sit inline.

## Grounding in OWASP LLM Top 10 and OWASP Agentic Threats

Every layer in the FGAC maps back to one or more entries in the OWASP taxonomies. When the FGAC's hazard-coverage matrix (section 1) is authored, this mapping is what makes it *defensible* rather than *asserted*.

- **LLM01 Prompt Injection.** Layers 1 (deny-list on known signatures), 2 (input classifier), 3 (NeMo input rails), 5 (post-tool-response validator for indirect injection), and 6 (sidecar monitor for multi-turn injection sequences).
- **LLM02 Sensitive Information Disclosure.** Layers 1 (PII detector on input), 3 (NeMo output rails), 4 (output classifier), 5 (post-tool-response validator for PII in retrieved content), and 6 (sidecar monitor for slow exfiltration).
- **LLM04 Data and Model Poisoning.** Layer 5 (post-tool-response validator for retrieval-augmentation poisoning) and layer 6 (sidecar monitor for poisoned-input trajectories).
- **LLM05 Improper Output Handling.** Layers 3 (NeMo output rails), 4 (output classifier), and 6 (sidecar monitor).
- **LLM06 Excessive Agency.** Composes with the mod-107 EACC; layer 3 (NeMo execution rails) and layer 5 (post-tool-response validator) are the mod-108 contributions.
- **LLM07 System Prompt Leakage.** Layer 4 (output classifier for prompt-leakage patterns) and layer 6 (sidecar monitor).
- **LLM09 Misinformation.** Layer 4 (output classifier) and layer 6 (sidecar monitor with retrospective fact-checking).

OWASP Agentic Threats — Tool Misuse, Repudiation & Untraceability, Cascading Failures — map primarily to layer 5 (validator) and layer 6 (sidecar), composed with mod-107's monitored-wrapper posture.

<!-- needs-research: pin the exact 2025 published wording of each OWASP LLM Top 10 entry when authoring the FGAC hazard-coverage matrix; OWASP has revised entry numbering between drafts. -->

## The `ai-risk-engineer` prerequisite carries the base-depth introduction

The `ai-risk-engineer` learning track (level 25) covers each of the tools this module composes — **NeMo Guardrails, Guardrails AI, Presidio, Llama Guard, ShieldGemma, OpenAI Moderation** — at the *this is what it is, this is how you wire it in, this is what a starter deployment looks like* level. mod-108 does *not* re-teach any of that. What mod-108 adds is the frontier-scope engineering discipline layered on top:

- The **composition contract** (this chapter's FGAC) that turns a stack of tools into a defensible architecture.
- The **classifier-training loop** (chapter 03) that adapts a base model to the deployment's specific hazard taxonomy.
- The **Constitutional Classifiers methodology** (chapter 04) as the frontier-scope answer to universal jailbreaks.
- The **build-vs-buy matrix** (chapter 05) that turns vendor evaluation from vibes into a defensible decision.
- The **effectiveness measurement discipline** (chapter 06) — TP / FP / TN / FN on bounded adversarial + benign sets, adaptive-attack survival curves, latency and cost accounting, benign-user overhead — and the FP / FN report shaped for a product manager.

When you encounter a reader who has not done the prerequisite, route them to `ai-risk-engineer` for the base-depth material and re-engage them here. Skipping the prerequisite and starting here is a mistake the FGAC's authoring will surface immediately — you cannot write section 2's layer inventory without knowing what each tool is.

## Interfaces to the rest of the module and beyond

- **Chapter 02** — the input / output classifier layer class in depth (Llama Guard, ShieldGemma, OpenAI Moderation, published Anthropic classifiers), including per-class precision / recall / latency / cost accounting and placement discipline.
- **Chapter 03** — the fine-tuning loop for a custom classifier: data authoring, harmful-example sourcing under discipline, synthetic augmentation, distillation, adaptive-attack survival as the load-bearing metric.
- **Chapter 04** — the Constitutional Classifiers methodology (Sharma et al., 2025): specification authoring, synthetic + red-team training data, the adaptive-attack survival curve as evaluation, thousands-of-hours red-team scaling context.
- **Chapter 05** — rule / flow guards and the vendor-vs-build matrix; when to build, when to buy, how to structure a vendor eval defensibly.
- **Chapter 06** — effectiveness measurement, the FP / FN report shaped for a product manager, and the boundary handoffs to `ai-risk-engineer`, mod-109 (guardrails become safety-case evidence), mod-111 (adaptive attacks target the guardrails), and mod-112 (guardrail FP / FN in disclosure).
- **mod-107** — the FGAC composes *on top of* the EACC; layer 5 (post-tool-response validator) plugs into mod-107's monitored wrappers; layer 6 (sidecar monitor) reads the mod-107 tamper-evident audit stream and feeds the mod-107 chapter 05 kill-switch fire-vote.
- **mod-109** — the FGAC's section-4 performance contract and section-7 evidence-emission contract are the primary inputs to the *control-argument* leg of a safety case. mod-109 authors the argument; mod-108 produces the evidence.
- **mod-111** — the automated red-team hammers the FGAC directly; findings flow back as training data for chapter 03's fine-tune loop and as adaptive-attack-survival evidence for chapter 04's evaluation.
- **mod-112** — every fire that reaches a customer is a datum in the disclosure workflow; the FGAC's section-7 evidence-emission is the disclosure-input schema.

## Common misreadings to avoid

- **"Stacking more classifiers is defence in depth."** Not by itself. Two classifiers trained on the same data with the same objective are one classifier duplicated. Defence in depth requires *layers with different failure modes* — deterministic vs probabilistic, input-side vs output-side, inline vs retrospective, in-house vs vendor. The FGAC's section-1 hazard-coverage matrix is what forces you to check that the layers actually compose.
- **"The primary model's own safety tuning is a layer."** It is *training*, not a *runtime layer*. Safety-tuned models make the inline layers' job easier, but they are not themselves layers of the FGAC — a jailbreak defeats them by construction. Treat them as the base-rate the inline layers are compensating for.
- **"We can put the policy in the system prompt and skip the input classifier."** The system prompt is a hint, not a filter. The mod-107 chapter 01 framing generalises: any control that lives in text the model reads is a control the model can be convinced to ignore. Guardrail layers live in code the model does not read.
- **"The vendor product is the layer."** The vendor product is the *implementation* of a layer. The layer is a *placement + intent + performance contract*. Two vendor products with the same intent do not stack usefully; two layers with different intents do. Chapter 05 is where the vendor eval matrix pins this discipline.
- **"Refuse-if-any-layer-fires is the safe composition."** It maximises recall of harmful and destroys the benign-user experience. The FGAC's section-5 composition semantics is where the trade-off is negotiated and documented — usually weighted vote, layer-specific confidence thresholds, and escalation-on-disagreement rather than blanket-refuse.
- **"Sidecar monitors are eventual consistency and therefore not safety-critical."** They are the layer that catches the *residual* hazard the inline stack admits. They are how mod-108 ships evidence to mod-109 safety cases. They are how mod-107's kill-switch gets its fire-vote. They are safety-critical; they are just not on the request path.

## Summary

- Frontier-scale guardrail engineering is defence-in-depth applied to the LLM call-graph. Six layer classes — **pre-input filter**, **input classifier**, **rule / flow guard**, **output classifier**, **post-tool-response validator**, **safety-monitor sidecar** — each with a distinct placement, a distinct hazard-coverage profile, and a distinct characteristic failure the downstream layer catches.
- The module-level artefact is the **Frontier Guardrail Architecture Contract (FGAC)**: eight sections pinning hazard taxonomy, layer inventory, ordering, per-layer performance contract, composition semantics, escalation contract, evidence emission, and change management.
- Ground the FGAC's hazard-coverage matrix in **OWASP LLM Top 10 2025** and **OWASP Agentic Threats**. Cite version-pinned entries; do not paraphrase.
- The composition problem is not solved by stacking. It is solved by picking *layers with different failure modes*, ordering them so cheap deterministic checks precede expensive probabilistic ones, and pinning composition semantics smarter than refuse-on-any-fire.
- The `ai-risk-engineer` prerequisite (level 25) carries base-depth introductions to NeMo Guardrails, Guardrails AI, Presidio, Llama Guard, ShieldGemma, OpenAI Moderation. This module composes them, adapts them, measures them, and defends the composition to a safety review.
- mod-107's EACC narrowed the runtime; mod-108's FGAC narrows the content surface. Together they are what a safety review reads before a frontier deployment ships.
