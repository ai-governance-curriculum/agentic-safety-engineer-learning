# mod-108-frontier-guardrails-and-monitors: Frontier-Scale Guardrails and Safety-Monitor Engineering

**Estimated effort:** 16 hours

## What this module is

Frontier-scale guardrail engineering is defence-in-depth applied to the LLM call-graph. Where mod-107 made the *runtime* narrower than the *model*, this module makes the *content surface* narrower than the *world* by composing a stack of pre-input filters, input classifiers, rule / flow guards, output classifiers, post-tool-response validators, and safety-monitor sidecars — none of which is a silent single-point-of-failure. The module-level deliverable is a **Frontier Guardrail Architecture Contract (FGAC)**: a versioned, signed, per-deployment contract that names every layer, its placement, its performance contract, its composition semantics, and its evidence-emission for downstream monitors.

The `ai-risk-engineer` prerequisite (level 25) carries the base-depth introduction to NeMo Guardrails, Guardrails AI, Presidio, Llama Guard, ShieldGemma, and OpenAI Moderation. This module does not re-teach that. What it adds is the composition contract (the FGAC), the classifier-training loop, the Constitutional Classifiers methodology, the vendor build-vs-buy matrix, and the FP / FN report shaped for a product manager.

## Learning objectives

- Design a defence-in-depth guardrail architecture for a frontier agent — pre-input filters (deny-list, injection detectors, PII), input classifiers (Llama Guard, ShieldGemma, OpenAI Moderation), output classifiers (Constitutional Classifiers, custom fine-tuned safety classifiers), rule / flow guards (NeMo Guardrails, Guardrails AI), post-tool-response validators, safety-monitor sidecars — composing cleanly without silent single-points-of-failure.
- Ship a fine-tuned classifier guard — training-data authoring, harmful-example sourcing under discipline, cost / latency accounting, adaptive-attack survival curve.
- Adopt Anthropic Constitutional Classifiers methodology — build a specification, generate training data from red-team + synthetic augmentation, fine-tune a lightweight classifier, and measure adaptive-attack survival across thousands of hours of red-team.
- Evaluate commercial guardrail vendors (Lakera Guard, HiddenLayer AIDR, Protect AI Guardian, Robust Intelligence, CalypsoAI, HydroX AI) with a defensible build-vs-buy matrix.
- Measure guardrail effectiveness — TP / FP / TN / FN on a bounded adversarial + benign set, adaptive-attack survival, cost + latency + p95 accounting, benign-user overhead — and produce an FP / FN report a product manager can read.
- Cite the boundary to `ai-risk-engineer` (prerequisite, level 25): that packet covers NeMo Guardrails / Guardrails AI / Presidio / Llama Guard / ShieldGemma at the general depth; this module adds the classifier-training loop, adaptive-attack survival, and defence-in-depth composition for frontier scope.

## Chapters

1. [`01-defence-in-depth-guardrail-architecture.md`](01-defence-in-depth-guardrail-architecture.md) — the FGAC, the six load-bearing layer classes, the composition graph, and the OWASP / NIST grounding.
2. [`02-input-and-output-classifiers.md`](02-input-and-output-classifiers.md) — Llama Guard, ShieldGemma, OpenAI Moderation and vendor-published Anthropic classifiers; placement, per-class accounting, threshold discipline.
3. [`03-fine-tuned-safety-classifier-guards.md`](03-fine-tuned-safety-classifier-guards.md) — training-data authoring, harmful-example sourcing under discipline, synthetic augmentation, distillation, calibration, adaptive-attack survival as the load-bearing metric.
4. [`04-constitutional-classifiers-methodology.md`](04-constitutional-classifiers-methodology.md) — Anthropic's Constitutional Classifiers methodology: specification, synthetic-corpus generation, adaptive-attack survival curve, the iteration loop.
5. [`05-rule-and-flow-guards-and-vendor-tradeoffs.md`](05-rule-and-flow-guards-and-vendor-tradeoffs.md) — NeMo Guardrails, Guardrails AI, Presidio placements; the commercial vendor ecosystem and the build-vs-buy matrix.
6. [`06-guardrail-effectiveness-measurement-and-boundaries.md`](06-guardrail-effectiveness-measurement-and-boundaries.md) — TP / FP / TN / FN accounting, adaptive-attack survival curves, latency / cost / benign-user overhead, the FP / FN report shaped for a product manager, boundaries to `ai-risk-engineer`, mod-107, mod-109, mod-111, mod-112.

## Exercises

Each exercise builds one section (or one measurement artefact) of the FGAC for a concrete deployment of your choice. Together they compose the same shape as the mod-107 EACC exercises — the two contracts are joint deliverables of the safety-engineering role.

1. [`exercises/exercise-01-defence-in-depth-guardrail-architecture-for-one-agent.md`](exercises/exercise-01-defence-in-depth-guardrail-architecture-for-one-agent.md) — Author FGAC sections 1–6 for one agent deployment, plus a reviewer memo naming under- and over-gated layers.
2. [`exercises/exercise-02-fine-tune-a-safety-classifier-guard.md`](exercises/exercise-02-fine-tune-a-safety-classifier-guard.md) — Author the hazard taxonomy and labelling schema, source and augment training data, fine-tune a small base model, and publish FGAC section-4 fields.
3. [`exercises/exercise-03-constitutional-classifier-methodology-hands-on.md`](exercises/exercise-03-constitutional-classifier-methodology-hands-on.md) — Author a specification (positive / negative / disambiguation rules), generate synthetic training data from it, fine-tune, and report a preliminary survival curve against a bounded red-team budget.
4. [`exercises/exercise-04-commercial-guardrail-build-vs-buy-matrix.md`](exercises/exercise-04-commercial-guardrail-build-vs-buy-matrix.md) — Score at least four vendor candidates plus the exercise-02 build baseline on the FGAC's own criteria and publish a recommendation memo.
5. [`exercises/exercise-05-adaptive-attack-survival-curve-eval.md`](exercises/exercise-05-adaptive-attack-survival-curve-eval.md) — Design and run a bounded adaptive-attack survival evaluation against a composed FGAC and ship the FP / FN report shaped for a product manager.

## Structure

- `01-…md` … `06-…md`: lecture chapters.
- `exercises/`: per-exercise prompts (this repo). Solutions live in the paired `agentic-safety-engineer-solutions` repo.
- `labs/`: long-form hands-on labs (placeholder).
- `quizzes/`: knowledge checks (placeholder).
- `resources.md`: external references.
