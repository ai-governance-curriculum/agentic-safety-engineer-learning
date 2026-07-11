# exercise-01 — RSP × Preparedness × FSF Comparative Read

**Estimated effort:** 3 hours
**Prerequisite chapters:** 02, 03, 04, 05.

## Objective

Read the currently-published Anthropic Responsible Scaling Policy, OpenAI Preparedness Framework, and Google DeepMind Frontier Safety Framework front-to-back and produce a **single side-by-side comparison artifact** that maps them onto the common shape (capability → threshold → elicitation → tripwire → mitigation → rollback → review → evidence → disclosure).

The goal is that after this exercise you can produce an evaluation artifact whose evidence is defensible against all three frameworks simultaneously, and you can spot at a glance where the three frameworks diverge in a way that would change your artifact's shape.

## Problem statement

You are the safety engineer at a hypothetical frontier lab that is a signatory to the Seoul Frontier AI Safety Commitments and whose model is used enough in the EU to be a candidate for GPAI-SR designation. Your director has asked you to produce a **cross-framework reading brief** the entire safety team can use as a reference when authoring evaluation artifacts. The brief has to answer:

- What is the "unit of decision" in each framework? (ASL / Preparedness category / CCL.)
- What are the current thresholds each framework names, in each framework's own language?
- How do the three frameworks describe pre- and post-mitigation reasoning?
- What is the review body / decision authority in each? Name the current body per framework.
- What is the current cadence and update history for each?
- Where do the three frameworks materially diverge (a change to your artifact would be required)?
- Where do they materially align (a shared substrate is safe)?

## Requirements

1. **Read the primary sources.** Not summaries, not blog posts about the frameworks, not this course's chapters as a substitute — the actual published RSP, Preparedness, and FSF documents. Use the links in [`resources.md`](../resources.md) as the entry point.
2. **Produce one artifact.** A single Markdown document, `cross-framework-reading-brief.md`, ~2000–3500 words, structured with a side-by-side comparison table plus per-axis prose.
3. **Cite the primary source for each claim.** Every specific claim about a framework's language must be cited to the specific document and (where the document has section numbers or headings) the specific section. If the framework has been updated during your reading window, cite the version.
4. **Include a "current signatories / current tier assignments" section** where each framework names something publicly (Seoul commitments signatories, published system-card tier decisions per model). Cite public sources.
5. **Include an engineer's cross-framework artifact plan** — a section that says: "if I run one dangerous-capability evaluation, here is how I slice it to populate an ASL decision, a Preparedness scorecard entry, and one or more CCL decisions, in one report."
6. **Flag every place your reading is uncertain** with an inline `<!-- needs-research: ... -->` marker, so the reader can act on your uncertainty rather than mistake it for confidence.

## Starter guidance

- Read once for the sweep, then once for the details. Do not annotate on the first pass — the goal of the first pass is to see the *shape*. Annotate on the second pass.
- Build the comparison table first, then the prose. The table forces you to be specific; the prose lets you explain the trade-offs.
- Do not try to reconcile the three frameworks into one — the point is to see where they align and where they diverge, not to invent a unified super-framework.
- Note the "in-force version" at the top of the brief. The three frameworks update on different cadences; the version you are reading may not be the version your reader is reading.
- If a specific numerical threshold or list of categories has changed between framework versions, note both — the safety team will need to reason about which version the evidence artifact was authored against.

## Acceptance criteria

- ✅ A single Markdown artifact named `cross-framework-reading-brief.md`, ~2000–3500 words.
- ✅ Side-by-side comparison table covering *at least* these axes: unit of decision, threshold labels, misuse/misalignment split, pre/post-mitigation split, elicitation discipline, review body, update cadence, publication style.
- ✅ Per-framework "in-force version" noted at the top, with observation date.
- ✅ Every specific claim cited to a primary-source URL and (where available) section anchor.
- ✅ A "current signatories / current tier assignments" section grounded in public system cards or public commitments.
- ✅ An "engineer's cross-framework artifact plan" section that names how one evaluation is sliced into the three frameworks' evidence.
- ✅ A "where the frameworks materially diverge" section with at least three specific divergences.
- ✅ A "where the frameworks materially align" section with at least three specific alignments.
- ✅ Inline `<!-- needs-research: ... -->` markers where your reading is uncertain, with enough detail that the next reader can act on the flag.
- ✅ No paraphrases of numerical thresholds — cite them exactly or do not cite them.

## Stretch goals

- Produce a *machine-readable* companion artifact (`cross-framework.yaml`) with the comparison table as structured data. This is the first step toward the cross-lab language card described in chapter 05.
- Add a fourth column for a specific *jurisdictional* framework you cover elsewhere (EU GPAI Code of Practice, Chinese Interim Measures for Generative AI, Korean AI Basic Act, etc.). Cross-check the shape.
- Add a fifth column for a specific *sector* framework (US Financial services model risk management, EU medical device MDR, etc.) and see whether the frontier-safety shape maps into it cleanly.
- Prototype the "one evaluation, three slices" flow using a small Inspect task and note where the slicing is clean and where the three frameworks force separate reporting.

## Deliverable location

Place your deliverable in your personal notes or a private repo, not in this course repo. The paired solutions repo carries the reference brief; this course repo is the exercise prompt only.
