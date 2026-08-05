# exercise-01: Defence-in-Depth Guardrail Architecture for One Agent

**Estimated effort:** 3 hours

## Objective

Author sections 1–6 of a **Frontier Guardrail Architecture Contract (FGAC)** end-to-end for one concrete agent deployment, and *review it as though you were a peer safety reviewer*. The output is a versioned FGAC artefact a reviewer at level 40 would sign, plus a short review memo naming three specific places the composition has a silent single-point-of-failure and one specific layer that is over-provisioned relative to the hazard it addresses.

This exercise anchors chapter 01 in practice. Exercises 02 and 03 populate specific layer implementations; exercise 04 populates the vendor rows of section 2; exercise 05 populates the section-4 numbers under adversarial pressure.

## Prerequisites

- Read chapter 01 (Defence-in-Depth Guardrail Architecture) end-to-end. Skim chapters 02–06 for the shape of the layer inventory and the section-4 fields.
- Skim [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/) and the [OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/). Cite specific 2025-pinned entries in your hazard-coverage matrix.
- Complete the `ai-risk-engineer` prerequisite (level 25) — or at minimum, be able to name what Llama Guard, ShieldGemma, OpenAI Moderation, NeMo Guardrails, Guardrails AI, and Presidio each do without re-reading their docs.
- If you have completed mod-107 exercise 01, pick the same deployment. The FGAC and the EACC compose; a shared target makes the composition tangible.
- Otherwise pick a target deployment. Either:
  - A real deployment you have documented access to (coordinate with the owning team before submitting internally), *or*
  - A **paper deployment** — a written description of the agent's purpose, tools, principals, retrieval sources, and downstream systems with enough detail that another engineer could review the FGAC without asking questions.
- Identify the peer artefacts you would cite: the mod-107 EACC (for the mod-108 layer-5 / layer-6 composition), the `senior-agentic-ai-engineer` tool inventory, the `fine-tuning-engineer` peer for any custom classifier compute, the `ai-infra-security` peer for the audit-log substrate.

## Requirements

Author `FGAC.md` (or `FGAC.yaml`) with the following sections. Every field must be filled — either with a concrete choice, a specific "waived because …" reason, or a specific "requires peer-role input" cite. TBDs are not accepted.

### Section 1 — Hazard taxonomy and coverage matrix

For every hazard class the deployment must not admit, record:

- **Hazard-class ID** and one-line intent statement in the deployment's own vocabulary (not "LLM01" — "attempts to make the assistant reveal customer records via injected instructions in retrieved documents").
- **Source anchor.** OWASP LLM Top 10 2025 entry, OWASP GenAI Agentic-Threats entry, MLCommons AILuminate hazard category, operator's own risk register, or regulator-mandated category. Cite specifically.
- **Severity band** (low / medium / high / critical). Composition semantics keys on this.
- **Coverage matrix.** A row per hazard class × a column per FGAC layer (pre-input filter, input classifier, rule / flow guard, output classifier, post-tool-response validator, sidecar). Each cell records the layer's expected contribution: primary detector, backstop, redundant coverage, or not applicable. Hazards with an empty row are the residual-risk claim (mod-109); flag them.

Include at least the following classes if the deployment's surface admits them: prompt injection (direct + indirect), sensitive-information disclosure at input and at output, PII on retrieved content, improper output handling (structured-output leakage, secrets in code), excessive agency at the tool-invocation boundary, system-prompt leakage, misinformation, retrieval poisoning.

### Section 2 — Layer inventory

For every layer the FGAC ships, record:

- **Layer ID** (`L1-preinput-presidio`, `L2-input-llamaguard3-8b`, `L3-nemo-input-rails`, etc.), version pin, and build-vs-buy classification (in-house build / open-source adopted / commercial vendor / hybrid).
- **Placement** in the composition graph (pre-input / input / rule / output / post-tool-response / sidecar), matching chapter 01's layer numbering.
- **One-line intent statement** — what this layer contributes that no other layer contributes.
- **Component identity** — the specific tool, model, or vendor product (e.g., "Presidio Analyzer 2.x with US SSN + custom `internal-case-id` recognisers", "Llama Guard 3 8B on the input side at threshold 0.4", "NeMo Guardrails Colang input rails at commit `<sha>`"). Cite the specific version.
- **Ownership** — the team or peer role that owns the layer's implementation, its retraining cadence (if a classifier), and its on-call.
- **Fallback posture** — what happens when the layer is unavailable (fail-closed / fail-open / degrade-with-alert). Reference section 5.

### Section 3 — Ordering and data-flow contract

Draw the composition graph explicitly. For the input path, output path, and tool-response path, pin:

- **Sequence** — which layer runs first, second, third; parallel vs serial execution; short-circuit conditions.
- **Input each layer sees** — canonicalised text, structured envelope, tool arguments, retrieved document body. If a layer sees a transformed version, name the transformation.
- **Output each layer emits** — a per-hazard verdict, a confidence, a redacted/rewritten payload, a canonical-input hash for downstream evidence.
- **Data flowing to the sidecar** (section 7) — full verdict envelope per turn, per tool call, per session.
- **Where composition semantics apply** — section 5 rules on which layer's verdict wins in disagreement, which combination triggers a refusal, which combination escalates.

Follow the chapter-01 composition-graph shape as the reference; deviations from the reference are called out and justified.

### Section 4 — Per-layer performance contract (targets)

For each layer in section 2, per hazard class in section 1, record the *targets* the layer commits to. Numbers you have not yet measured are `target: X` with the source (vendor-published, chapter-02 base rate, mod-108 fine-tune target). Exercise 05 fills the *measured* numbers; this exercise fills the targets.

- **Precision** and **recall** per hazard class.
- **False-positive rate on benign** per hazard class or aggregate.
- **Latency p50 and p95** at the layer's own batch size and hardware.
- **Cost per 1 000 calls** (inference + vendor per-call charges + amortised training).
- **Confidence-calibration commitment** (Expected Calibration Error target, or "not calibrated — waived because …").
- **Adaptive-attack survival target** — either a red-team-hours-until-recall-degrades-by-X target or an explicit "no adaptive-attack evaluation planned — residual-risk claim covers".

### Section 5 — Composition semantics

Pin the rule that combines layer verdicts.

- **Per-layer weighting.** For each hazard class, how much weight does each layer's verdict carry? Weights are numeric or explicitly "veto".
- **Refusal rule.** Which combinations of verdicts produce a hard refusal, a soft refusal, a rewrite, an escalation, or a log-and-let-through? Enumerate; no ad-hoc composition.
- **Disagreement rule.** When two layers disagree (input classifier passes, output classifier flags; input classifier flags, rule guard passes), which wins? What is logged? Which case escalates to sidecar review?
- **Refusal-message contract.** The structured refusal shape the model / user receives. Consistent across layers; includes a reason code that maps to section 1's hazard classes and a caller-safe explanation.
- **Fallback composition.** When a layer is unavailable per section-2 fallback posture, does the composition change? Enumerate the degraded-mode composition for each layer's failure.

### Section 6 — Escalation contract

For each fire mode the composition semantics produce (high-confidence harmful, low-confidence-flagged, layer-disagreement, adaptive-attack signature match, unknown-hazard sidecar detection), pin:

- **The destination.** Silent log, structured refusal to user, safety-monitor sidecar, human-review queue, on-call page, kill-switch fire-vote (mod-107 chapter 05).
- **The SLA.** How fast the destination has to act (real-time refusal, minutes-to-review, hours-to-triage).
- **The escalation UI or channel.** Where the reviewer or on-call operator sees the fire and its evidence.
- **The bypass-prevention posture.** What stops an attacker from flooding the escalation queue to force fail-open (approval-bombing analog)?
- **Composition with mod-107 chapter 05 kill-switch.** Which fire modes vote on the kill switch and with what weight.

### Section 7 review-outline (deferred)

Add a placeholder for the evidence-emission contract (what each layer emits into the audit-log stream for mod-109 safety-case evidence and mod-112 disclosure). Do not author section 7 here.

### Section 8 review-outline (deferred)

Add a placeholder for the change-management contract (who signs a threshold change, a layer swap, a specification update; the rollback path when a layer's FP rate spikes).

### Reviewer memo

Then put on your alter-ego reviewer hat. Author a 1–2 page memo that:

- Names **three specific silent single-points-of-failure** in the composition — a hazard class where the section-1 matrix has only one covering layer, a pair of layers whose training data or vendor is common (correlated failure), a layer whose fallback posture would fail-open on an unavailability the composition does not currently expect. For each, propose the specific remediation.
- Names **one specific layer that is over-provisioned** — a redundant classifier where two cheaper deterministic checks would cover the hazard, or an output-side classifier duplicating the input-side classifier's verdict class without adding independent signal. Propose the tightening (drop the layer, downgrade to sidecar, reduce cadence).
- Confirms whether the FGAC could be signed off. If not, list the remediations required.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo):

- `FGAC.md` or `FGAC.yaml` — sections 1–6 filled in, sections 7–8 outlined.
- `composition-graph.md` (or an inline diagram in FGAC.md) — the section-3 data-flow contract rendered as a graph, with layer IDs and the paths through it.
- `review-memo.md` — the alter-ego reviewer memo.
- `README.md` in the exercise directory naming the target deployment, its primary hazards, and a one-paragraph summary of the review outcome.

## Acceptance criteria

- **Every hazard class has at least two covering layers** — or an explicit residual-risk statement naming the missing coverage. Single-covered hazards without a residual-risk note are a rejection.
- **Every layer has a version pin, an ownership row, and a fallback posture.** "Latest" is a rejection; "TBD owner" is a rejection.
- **Composition semantics is explicit.** "Refuse if any layer fires" without justification is a finding — chapter 01 names this as an anti-pattern; if the FGAC picks it anyway, name the FP-rate cost accepted.
- **Layer ordering is drawn as a graph, not a bulleted list.** The graph shows what each layer sees, what it modifies, what it emits.
- **Section-4 targets carry a source for each number.** Vendor-published, chapter-02 base rate, chapter-03 fine-tune target, or "unmeasured — exercise 05 to populate".
- **Escalation contract composes with mod-107.** Even if you have not authored an EACC, the FGAC's fire modes name which ones would fan into a kill-switch vote.
- **The reviewer memo names three concrete SPOFs and one concrete over-provisioned layer.** Each finding has a remediation.
- **The FGAC is versioned.** The hash of the artefact matches a runtime start-up log signature the deployment would emit.
- **Peer-role handoffs are IDs or explicit TODOs**, not "the peer role" or "someone else".

## Stretch goals

- **Cross-reference against the OWASP LLM Top 10 2025 entries in full.** Every layer's inclusion is justified against a specific OWASP entry; every OWASP entry the deployment is exposed to appears in section 1.
- **Author the section-7 evidence-emission contract.** For each layer, name the fields it emits to the mod-107 tamper-evident audit stream and the mod-108 sidecar. Cite mod-109's safety-case-evidence needs as the consumer contract.
- **Author the section-8 change-management contract.** Name the review body, the required test suite, the shadow-deployment window, and the rollback path for each of: threshold change, layer swap, specification update, vendor swap.
- **Compose with the mod-107 EACC.** For each of the EACC's tool-argument-provenance rules that admits `retrieved_document`, name the FGAC layer-5 post-tool-response validator that catches the injection risk.
- **Publish the section-4 measurement plan.** Which benchmark set, which red-team budget, which cadence — this is the shape exercise 05 executes.
- **Framework cross-reference table.** For each layer, produce a table mapping (OWASP LLM Top 10 entry × NIST AI 100-2 attack class × MITRE ATLAS technique). This is what mod-109 will re-use for the safety-case argument.

## Guardrails

- Do not implement the layers; author the contract. Exercise 02 implements a classifier layer; exercise 04 evaluates vendor layers; exercise 05 measures the composed FGAC.
- Do not commit any real credentials, hostnames, tenant IDs, PII samples, or production-benchmark payloads. Use redacted or paper-deployment values.
- If the FGAC references a real deployment, coordinate with the owning team before submitting the review memo internally.
- Do not paper over missing peer-role components with a hand-wave — the "requires peer-role input" TODOs are what make the FGAC honest.
- Do not cite vendor benchmark numbers as the deployment's section-4 numbers. Vendor numbers are targets or floors; the deployment's own numbers are the field.
