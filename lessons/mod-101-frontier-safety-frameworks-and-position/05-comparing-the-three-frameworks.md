# 05 — Comparing the Three Frameworks

## Motivation

Reading Anthropic RSP, OpenAI Preparedness, and Google DeepMind FSF separately is necessary — reading them side-by-side is what makes you fluent. The comparison is the muscle you use when:

- You move from one employer to another and have to re-map artifacts to the new framework's shape.
- You are asked to produce a **single** dangerous-capability evaluation whose evidence is defensible against **all three** frameworks (which is often the case for a shared benchmark, a Frontier Model Forum working paper, or an AISI-submitted evaluation).
- You draft a Frontier Model Forum publication or an EU GPAI Code of Practice implementation note whose language must be legible to reviewers from all three organisations.
- You author a public-facing system card whose safety-story references frameworks from more than one lab.

This chapter cross-reads the three frameworks on the axes an engineer cares about.

## The common shape

Underneath the naming differences, the three frameworks share a common shape:

```
capability                       — the behaviour the framework watches
    ↓
threshold                        — the pre-registered level of concern
    ↓
elicitation protocol             — how the eval measures capability against the threshold
    ↓
tripwire                         — the signal that fires when the threshold is crossed
    ↓
mitigation contract              — the safeguards required at the tier
    ↓
rollback contract                — what "roll back" concretely means if mitigations fail
    ↓
review body                      — the internal authority that decides the tier
    ↓
evidence artifact                — what the safety engineer authors as the record
    ↓
disclosure                       — the system card / AISI report / regulator filing
```

An engineer builds each artifact to fit this shape. The framework tells you what to *call* each box; the shape does not change.

## Axis-by-axis comparison

<!-- needs-research: some cells below cite historical / initial-version terminology because current-version language may have evolved. Cross-check each cell against the currently published version of each framework and update. -->

| Axis | Anthropic RSP | OpenAI Preparedness | Google DeepMind FSF |
|---|---|---|---|
| **Unit of decision** | AI Safety Level (ASL-N), model-level. | Capability threshold per Tracked Category, category-level, aggregated to a scorecard. | Critical Capability Level (CCL), per capability. |
| **Threshold labels** | ASL-1 / ASL-2 / ASL-3 / ASL-4+ (initial). | Low / Medium / High / Critical (initial); tracked / research categories reorganisation in 2025. | Named CCLs per dimension; escalating mitigation levels. |
| **Misuse vs. misalignment separation** | Implicit — both folded into ASL tier reasoning. | Categories cover misuse; autonomy category touches misalignment adjacent risks. | Explicit — misuse CCLs and misalignment CCLs are distinct. |
| **Pre/post-mitigation split** | Named in RSP text (capability vs. required safeguards). | Explicit scorecard split: pre-mitigation *and* post-mitigation placement per category. | Named as capability vs. mitigation level; less scorecard-shaped than Preparedness. |
| **Elicitation discipline** | Aggressive elicitation required — best-of-N, tool-use, scaffolds, fine-tuning where permitted. | Aggressive elicitation required — pre/post-mitigation split forces this. | Aggressive elicitation required — early-warning evals anticipate the *next* CCL. |
| **Review body** | Responsible Scaling Officer + internal governance. | Preparedness team + Safety Advisory Group (SAG) + executive / Board Safety and Security Committee. | Internal review process (evolved between versions). |
| **Update cadence** | Public updates on defined cadence; version history published. | Public updates; substantial 2025 reorganisation. | Public updates; May 2024 v1, Feb 2025 v2, subsequent iterations. |
| **Frontier Model Forum posture** | Founding member. | Founding member. | Founding member. |
| **Publication style** | Rich prose plus explicit safeguard lists. | Scorecard format plus category thresholds; leans heavily on the pre/post-mitigation table. | Framework text plus separate technical report per version. |

### Where they materially diverge for an engineer

- **Category structure.** Preparedness is explicitly category-shaped (cyber, CBRN, persuasion, autonomy…); RSP tiers the *model*; FSF tiers *capabilities*. When you produce a single eval, plan to slice its output multiple ways so it fits all three shapes.
- **Misalignment weight.** FSF puts the most explicit weight on misalignment CCLs. If you are producing evidence for a safety case whose FSF-shaped review lane is the primary one, make sure you have deception / sandbagging / alignment-faking / sleeper-agent evidence.
- **Pre/post-mitigation reporting.** Preparedness makes this the *primary* artifact shape. The RSP and FSF do not require the split as explicitly, but authoring both numbers is defensible under all three.
- **Review-body vocabulary.** Different names, same shape. Author your report so it labels the review body it is addressed to.

### Where they materially align

- All three commit to **pre-registration** of thresholds. Moving a threshold after seeing the eval result violates all three.
- All three commit to **aggressive elicitation**, elicitation-gap accounting, and refusal to under-elicit.
- All three name a **review body / decision authority** and constrain deployment on that body's decision.
- All three commit to **regular public updates** of the framework and its results (system cards, published evaluations).

## An engineer's cross-framework artifact plan

If you are running a **single** frontier-safety evaluation whose evidence should be defensible against all three frameworks, plan to author:

1. **Elicitation-methodology defence** — one document, all three frameworks read it: sampling, decoding, tool-use, scaffolds, fine-tuning-on-eval where permitted, external graders, elicitation-gap.
2. **Per-tier evaluation result** — parameterise the result by the framework's tier vocabulary. One eval result can populate an ASL decision, a Preparedness scorecard entry, and one or more CCL decisions.
3. **Pre/post-mitigation split** — always. Even if only the Preparedness Framework requires it explicitly, it makes the RSP and FSF cases more defensible.
4. **Misalignment evidence** — attach deception / sandbagging / alignment-faking / sleeper-agent / in-context-scheming evidence to the report, so the FSF misalignment-CCL side is populated and so the RSP and Preparedness reviewers can see the alignment-fingerprint of the model.
5. **Cross-lab language card** — a short doc that reads: "this artifact populates ASL-X evidence for the RSP, category-Y-tier-Z for Preparedness, CCL-W for FSF, and the framework-agnostic evidence for AISI / EU AI Office review." This card is the deferral contract's public face.

## Common misreadings to avoid

- **Assuming the three frameworks are interchangeable.** They are shape-equivalent but different in labels, review bodies, and update history. Read the current version of each before you author to it.
- **Assuming an artifact needs three different eval runs.** Usually one eval, sliced multiple ways, is defensible — as long as elicitation methodology and thresholds are pre-registered against each framework's vocabulary.
- **Skipping the misalignment side because "the model isn't smart enough yet".** The point of pre-CCL evaluation is to *have* the evidence before it is load-bearing. Backfilling a safety case with hurried misalignment evidence is the failure mode.

## Summary

- Anthropic RSP, OpenAI Preparedness, and Google DeepMind FSF are shape-equivalent — capability, threshold, elicitation, tripwire, mitigation, rollback, review, evidence, disclosure — but differ in unit of decision (ASL / category-scorecard / CCL), misalignment-weight, and review-body vocabulary.
- Produce evaluation evidence *once*, then slice it into each framework's vocabulary; carry a cross-lab language card so the deferral contract for the artifact is explicit.
- Always author pre/post-mitigation split, elicitation-methodology defence, and misalignment evidence — even where a specific framework does not explicitly require them.
