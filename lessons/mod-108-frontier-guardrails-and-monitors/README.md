# mod-108-frontier-guardrails-and-monitors: Frontier-Scale Guardrails and Safety-Monitor Engineering

> Scaffolded by `aicg org execute-plan`. Lecture chapters and exercise content are authored on subsequent autonomous cycles.

**Estimated effort:** 16 hours

## Learning objectives

- Design a defence-in-depth guardrail architecture for a frontier agent — pre-input filters (deny-list, injection detectors, PII), input classifiers (Llama Guard, ShieldGemma, OpenAI Moderation), output classifiers (Constitutional Classifiers, custom fine-tuned safety classifiers), rule / flow guards (NeMo Guardrails, Guardrails AI), post-tool-response validators, safety-monitor sidecars — composing cleanly without silent single-points-of-failure
- Ship a fine-tuned classifier guard — training-data authoring, harmful-example sourcing under discipline, cost / latency accounting, adaptive-attack survival curve
- Adopt Anthropic Constitutional Classifiers methodology — build a specification, generate training data from red-team + synthetic augmentation, fine-tune a lightweight classifier, and measure adaptive-attack survival across thousands of hours of red-team
- Evaluate commercial guardrail vendors (Lakera Guard, HiddenLayer AIDR, Protect AI Guardian, Robust Intelligence, Calypso AI, HydroX AI) with a defensible build-vs-buy matrix
- Measure guardrail effectiveness — TP / FP / TN / FN on a bounded adversarial + benign set, adaptive-attack survival, cost + latency + p95 accounting, benign-user overhead — and produce an FP/FN report a product manager can read
- Cite the boundary to `ai-risk-engineer` (prerequisite, level 25) — that packet covers NeMo Guardrails / Guardrails AI / Presidio / Llama Guard / ShieldGemma at the general depth; this module adds the classifier-training loop, adaptive-attack survival, and defence-in-depth composition for frontier scope

## Structure

- `01-…md` … `0N-…md`: lecture chapters.
- `exercises/`: per-exercise prompts.
- `labs/`: long-form hands-on labs.
- `quizzes/`: knowledge checks.
- `resources.md`: external references.
