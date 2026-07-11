# exercise-03 — Encoding / Obfuscation Vectors Drill

**Estimated effort:** 2 hours
**Prerequisite chapters:** 05 (helpful: 02, 03, 06).

## Objective

For each of the obfuscation vectors from chapter 05, produce a **defence-quality evaluation artefact** — the plain-form detection rate, the obfuscated-form detection rate, the model-compliance delta, and the *defence hole* (plain minus obfuscated detection). The exercise turns the vector taxonomy into per-vector engineering findings you can hand to the operator's defence stack owners.

## Problem statement

Pick 4–6 obfuscation vectors from chapter 05 to drill against your target agent's defence stack. If your target is under-defined, use a plausible baseline stack:

- Input-side NFKC normalisation.
- A simple regex-based classifier that flags obvious "ignore previous instructions" patterns and their close paraphrases.
- No specific Tag-character rejection.
- No decoded-base64 re-classification.
- No cross-lingual classifier coverage beyond English.

You may substitute your target's real stack if you have access; the drill is more useful when calibrated to real defences.

For each chosen vector, produce a defence-quality evaluation artefact per chapter 05's template.

## Requirements

Produce one Markdown artefact per vector, plus one aggregate summary.

### Per-vector artefact (`obfuscation-<vector>-eval.md`)

~600–1000 words per file. Sections:

- **Vector definition.** Chapter-05 restatement of the vector.
- **Chosen primitive × channel cell** to drill against. Pick one from your exercise-01 or exercise-02 catalogue. Different vectors will make sense against different cells — do not force one vector against every cell.
- **Plain-form payload shape.** The baseline payload. Defanged.
- **Obfuscated-form payload shape** (or shapes — most vectors admit sub-forms). Defanged.
- **Detection experiment.** For each of plain and obfuscated:
  - How many trials?
  - Which defence in the stack scored each trial (input classifier, output classifier, sandwich reminder, model self-refusal)?
  - What was the detection rate?
- **Model-compliance experiment.** For each of plain and obfuscated, when the defence *did not* catch it, did the model comply?
- **Numbers.** A small table:

  | Form | Detection rate | Model-compliance rate on undetected |
  |---|---|---|
  | Plain | ... | ... |
  | Obfuscated | ... | ... |
  | **Defence hole** | | |
- **Failure-mode analysis.** For the vector's specific sub-forms, which sub-form was easiest to catch, which was hardest, and why? What does that suggest about the sanitiser's design (e.g., "the sanitiser stripped U+200B but not U+200D — the ZWJ pass-through is a defence hole")?
- **Recommended mitigations** — concrete changes to the defence stack. Name the owner.
- **Reproducibility bundle** — model ID, decoding config, template, prompt scaffold, seed range.

### Aggregate summary (`obfuscation-drill-summary.md`)

A one-page rollup, ~400–600 words:

- Vector-by-vector defence-hole table.
- Ranking of vectors by defence hole for this target.
- Ranking of vectors by *cost to close* (how expensive is each recommended mitigation?).
- The composed-vector recommendation — which combinations of vectors (e.g., low-resource-language + base64 + delimiter-spoof) does chapter 05 note that this drill has not yet exercised, and are they on the exercise-05 backlog?
- Cross-references to exercise 04 (which cells the drill enriches) and exercise 05 (which defences the drill's mitigations feed into).

## Starter guidance

- **Pick vectors that cover distinct defence layers.** Homoglyph and Tag-character vectors stress input sanitisation. Base64 stresses input classifier decoding. Low-resource language stresses cross-lingual generalisation. ASCII art stresses layout-normalisation. Choose vectors whose recommended mitigations route to *different* owners — a drill that finds five holes in one owner's stack is less useful than one that finds five holes in five stacks.
- **Number-of-trials is a real design choice.** For a preliminary drill, 20–50 trials per (form, defence) is plausible. If you want release-gate-quality numbers, use exercise 04's harness.
- **Distinguish "sanitiser stripped it" from "classifier didn't flag it" from "model refused unaided."** All three appear as "no compliance" but they route to different fixes. The per-trial log has to preserve which layer caught the trial.
- **Test cross-primitive.** A base64-encoded exfil-through-completion payload lands differently than a base64-encoded instruction-override. Do not assume one vector's number generalises across primitives.
- **For low-resource-language vector, cite Yong et al. (2023).** For ASCII art, cite Jiang et al. (2024). Chapter 05 has the references.
- **For Tag characters, note the specific Unicode block (U+E0000–U+E007F, U+E0100–U+E01EF).** A sanitiser that strips one block but not the other is still a hole.
- **Do not publish weaponised strings.** Rendered payloads live outside this repo.

## Acceptance criteria

- ✅ One artefact per chosen vector (4–6 total), each with vector definition, target cell, plain / obfuscated shapes, detection experiment, compliance experiment, numbers table, failure-mode analysis, recommended mitigations, and reproducibility bundle.
- ✅ Aggregate summary with defence-hole ranking, cost-to-close ranking, composed-vector backlog, and cross-references.
- ✅ Detection experiment logs which defence layer scored each trial (input classifier vs. sanitiser vs. model self-refusal) so remediation is routable.
- ✅ Numbers reported across a stated seed range with sample size ≥20 per cell — or the artefact explicitly notes it is a rough drill and points to exercise 04 for calibrated numbers.
- ✅ Recommended mitigations name a specific defence layer and a specific owner.
- ✅ No weaponised payloads in the deliverables; payload storage-location placeholders only.
- ✅ Any unverified factual claim marked `<!-- needs-research: ... -->`.

## Stretch goals

- **Cross-model comparison.** Run the same drill against a second model (a safety-tuned variant, a comparison provider) and compare defence-hole rankings. Vector effectiveness varies by model family in ways that surprise operators.
- **Cross-composition drill.** Pick two vectors and evaluate their composed defence hole. Compare to the sum of individual defence holes; a composed hole larger than the sum is a signal for exercise 05.
- **Sanitiser fuzzing artefact.** Author a small script (100–200 lines) that iterates over Unicode blocks and reports which characters the target's sanitiser strips vs. passes. Include the output as a per-vector appendix.
- **Cross-reference OWASP Agentic Threat mapping.** For each defence-hole finding, name the OWASP Agentic threat it exacerbates (memory poisoning, cascading hallucination, etc.).

## Deliverable location

Personal notes or private repo. Do **not** commit into this course repo. Rendered payloads live outside both repos.
