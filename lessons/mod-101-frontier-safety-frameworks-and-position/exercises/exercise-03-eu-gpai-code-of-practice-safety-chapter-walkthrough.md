# exercise-03 — EU GPAI Code of Practice Safety Chapter Walkthrough

**Estimated effort:** 2 hours
**Prerequisite chapters:** 06 (helpful: 02, 03, 04, 08).

## Objective

Read the currently-published EU General-Purpose AI Code of Practice **Safety and Security chapter** end-to-end and produce a **measure-to-artifact walkthrough**: for each measure in the chapter, name the engineering artifact you would author to evidence it, the module in this track that owns the artifact's craft, and any dependencies on peer / next-up roles.

## Problem statement

Your hypothetical frontier lab has decided to sign the EU General-Purpose AI Code of Practice. Signature commits your organisation to the specific commitments and measures in the Code. Your director asks you to produce the internal **measure-to-artifact walkthrough** the safety team will use as the shared reference for what evidence populates which commitment.

## Requirements

1. **Read the Safety and Security chapter of the Code end-to-end.** Not the Transparency chapter, not the Copyright chapter — the Safety and Security chapter specifically. Use the primary source in [`resources.md`](../resources.md).
2. **Produce a single Markdown walkthrough document.** Structured as a walkthrough per **commitment**, and a sub-walkthrough per **measure** inside each commitment.
3. **For every measure**, name:
   - The measure ID / label from the Code.
   - The measure's own text (a short verbatim excerpt is fine; do not paraphrase).
   - The engineering artifact you would author to evidence the measure — as specific as possible (e.g., "the pre-registered elicitation-methodology defence, cross-signed by the Safety Board").
   - The module in this track that owns the artifact's craft (mod-102 through mod-112).
   - The peer / next-up role that co-authors or consumes the artifact (using the level ladder from chapter 01).
   - A dependency note if the measure depends on an artifact authored under a different framework (RSP / Preparedness / FSF).
4. **Cross-reference every measure to the AI Act article it operationalises** — typically Article 55, but note where a measure also operationalises Article 53, Article 56, or an Annex.
5. **Note the signatory status.** Which frontier labs are currently public signatories? Cite the Commission's published signatory list. Where a signatory has a *stated deviation* from a specific measure, note it.
6. **Flag uncertain readings.** The Code is a young document; use `<!-- needs-research: ... -->` liberally where you cannot cross-check a claim against the Code text.

## Starter guidance

- Read once for the sweep, once for the details. The Safety and Security chapter is dense; do not annotate the first pass.
- Build a two-column table (measure → artifact) before writing prose. The table forces specificity; the prose lets you explain dependencies.
- Do not try to invent a mapping the Code does not support. If a measure does not obviously map to an artifact you know how to author, mark it `<!-- needs-research: ... -->` and move on.
- Prefer measure text that mentions **adversarial testing**, **serious incidents**, or **cybersecurity** — these are the load-bearing ones for a safety engineer and the ones your evidence directly populates.
- Do not paraphrase the measure text. Extract short verbatim excerpts and cite the section.

## Acceptance criteria

- ✅ A single Markdown walkthrough document.
- ✅ Every commitment in the Safety and Security chapter is covered.
- ✅ For each measure: measure ID, verbatim excerpt, artifact, module, peer / next-up role, dependency notes.
- ✅ Cross-reference to the AI Act article(s) each measure operationalises.
- ✅ Signatory-status note, citing the Commission's published signatory list.
- ✅ Deviations documented where a signatory has publicly stated one.
- ✅ Inline `<!-- needs-research: ... -->` markers for uncertain readings.
- ✅ Reads as an internal working document, not a policy summary.

## Stretch goals

- Produce a parallel **YAML crosswalk** (`gpai-cop-safety-chapter-crosswalk.yaml`) as machine-readable structured data: `measure_id → { verbatim, artifact, module, act_article, dependencies }`. This becomes reusable for later modules.
- Add a mapping to the **Frontier AI Safety Commitments** (Seoul, May 2024) — for each Seoul commitment, note the closest GPAI Code measure. This makes visible where the industry-side and the EU-side commitments align vs. diverge.
- Sketch a serious-incident-report skeleton whose fields align to both **Article 73** (high-risk AI systems) and the GPAI Code's serious-incident-related measures. Note where the two lanes require different information.

## Deliverable location

Personal notes or private repo. Not this course repo. Solutions live in the paired solutions repo.
