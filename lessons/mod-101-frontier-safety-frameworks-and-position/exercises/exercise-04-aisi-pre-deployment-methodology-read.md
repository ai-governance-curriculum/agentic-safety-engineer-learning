# exercise-04 — AISI Pre-deployment Methodology Read

**Estimated effort:** 2 hours
**Prerequisite chapters:** 08 (helpful: 02, 03, 04, 05).

## Objective

Read at least one **published UK AISI pre-deployment evaluation** end-to-end and produce two artifacts: (a) an **anatomy** of the report shape, and (b) a **preparation playbook** for a lab that will face a similar window on a future frontier model.

## Problem statement

Your frontier lab expects to enter a UK AISI pre-deployment evaluation window for its next model release. You have not been through one before. Your director asks you to read at least one published AISI pre-deployment report end-to-end, anatomise its structure, and write the internal preparation playbook so the eventual window runs smoothly.

## Requirements

### Deliverable A — Report anatomy

1. **Read the report end-to-end.** Cite the specific report you read and the version. Start with the UK AISI pre-deployment evaluation of the upgraded Claude 3.5 Sonnet (linked in [`resources.md`](../resources.md)) and, if published, at least one additional pre-deployment evaluation.
2. **Produce an anatomy document** that names, section by section:
   - Section title.
   - What claim(s) the section is trying to support.
   - What evidence the section uses (task suite, sampling / elicitation methodology, grader methodology, task counts, whether the task suite is publicly named or held confidential).
   - What limitations / caveats the section explicitly declares.
   - Whether the section is likely produced by AISI's own tooling (Inspect) or by joint AISI-provider work.
3. **Extract the elicitation-methodology defence.** The report will describe its elicitation methodology explicitly or implicitly. Capture the specific choices — best-of-N counts, decoding, tool-use, scaffolds, fine-tuning-on-eval where mentioned. Note any explicit elicitation-gap acknowledgement.
4. **Extract the access-level statement.** What access level did AISI have to the model (API, fine-tuning, model internals, deployment-stack)?
5. **Note the publication-and-response cadence.** When was the eval run? When was the report published? Was there a co-published provider response (a system card entry, a blog post)?

### Deliverable B — Preparation playbook

1. **Author an internal playbook** in Markdown (~1000–2000 words) that a hypothetical safety team would use to prepare for a similar window on their next model.
2. **The playbook must include:**
   - A scoping-doc template: proposed access level, proposed categories, proposed timeline, evidence already gathered internally.
   - A pre-window checklist: which internal artifacts must exist before the window opens (RSP / Preparedness / FSF scorecard entries, Inspect ports of internal evals, red-team library, mitigation-effectiveness measurements).
   - A rehearsal plan: for each category the AISI is likely to evaluate, run your own version first and expect nothing surprising in the AISI report.
   - A remediation-lane playbook: pre-authored mitigation options keyed to likely findings; who signs off; how quickly they can ship.
   - A co-publication plan: how the internal system card is aligned to the AISI-published findings, and who owns the timing of both.
   - A trust-and-relationship section: how you engage the AISI cooperatively, not adversarially, and what that means concretely on the ground.

## Starter guidance

- Read the actual report. Do not summarise from press coverage — the coverage often skips the elicitation-methodology defence, which is what an engineer reads for.
- If you read multiple reports (recommended for stretch): note where the report shape differs. AISI iterates on its methodology and shape.
- The playbook is *internal*, not customer-facing. Write it as an operations doc, not a marketing document.
- Do not paraphrase the AISI's evaluation methodology into the playbook — say "align to the methodology described in {AISI report}" and cite.
- Ground the playbook in Inspect where you can — Inspect is the AISI's public harness and the highest-leverage port.

## Acceptance criteria

- ✅ Deliverable A (anatomy) covers every section of the report.
- ✅ Deliverable A extracts the elicitation-methodology defence with specific choices.
- ✅ Deliverable A extracts the access-level statement.
- ✅ Deliverable A notes the publication-and-response cadence.
- ✅ Deliverable B (playbook) is a self-contained internal operations doc.
- ✅ Deliverable B includes scoping-doc template, pre-window checklist, rehearsal plan, remediation-lane playbook, co-publication plan, and trust-and-relationship section.
- ✅ Both deliverables cite the specific AISI report(s) read, with version and URL.
- ✅ Inline `<!-- needs-research: ... -->` markers where a claim would need cross-checking against the report's currently-published version.

## Stretch goals

- Read a **second** AISI report (published pre-deployment or joint evaluation) and diff the shape against the first. Note methodology drift; this is a signal about what the AISI is prioritising in current work.
- Port one AISI-published methodology into **Inspect** as a small `Task`. Even a minimal port teaches you how Inspect's `Solver` / `Scorer` / `Dataset` primitives compose.
- Sketch a **US AISI (NIST)** submission version of the same evidence. Where does the US-side methodology differ from the UK-side? (Anchor to NIST AI 100-2 and NIST GenAI.)
- Add a cross-jurisdiction section to Deliverable B: how the artifacts you produce for the UK AISI window also populate the EU AI Office model report and the Preparedness Framework / RSP / FSF internal review.

## Deliverable location

Personal notes or private repo. Not this course repo. Solutions live in the paired solutions repo.
