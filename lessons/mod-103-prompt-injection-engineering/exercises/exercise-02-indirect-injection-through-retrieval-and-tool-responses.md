# exercise-02 — Indirect Injection Through Retrieval and Tool Responses

**Estimated effort:** 3 hours
**Prerequisite chapters:** 03, 04 (helpful: 01, 02, 06).

## Objective

Extend the direct-injection catalogue from exercise 01 across the **four indirect channel families** — retrieved context, tool responses, long-term memory / vector stores, and cross-plugin — for the same target agent. Ground the payload scenarios in Greshake et al. (2023) and the InjecAgent (Zhan et al., 2024) taxonomy. Produce a catalogue that populates the indirect rows of the PIEH coverage matrix (chapter 06).

## Problem statement

Using the same target agent as exercise 01, author an indirect-injection attack catalogue that:

- Covers all four indirect channel families from chapters 03–04.
- Enumerates every sub-channel your target agent actually exposes (webpage, PDF, RAG snippet; search snippet, code-interpreter, external API; per-user memory, shared corpus, feedback-loop-into-training; sub-agent, peer-agent, MCP server, plugin marketplace).
- For each sub-channel that is present in your target, catalogues at least one attack scenario per primitive it plausibly carries.
- Explicitly excludes sub-channels the target does not expose, with reasoning.

You may focus on 2–3 highest-leverage sub-channels rather than all thirteen if the target is small — but the *exclusions* must be enumerated, not silently omitted.

## Requirements

Produce three artefacts:

### Artefact A — `pieh-<target>-indirect-catalogue.md`

A Markdown catalogue, ~2500–4000 words. Structure:

**1. Channel inventory.** A table listing every indirect sub-channel from chapters 03–04, whether the target agent exposes it, and (if excluded) why.

**2. Retrieved-context section.** For each of webpage / PDF / RAG snippet the target exposes: sub-channel description, delivery mechanics (where the payload lives in the raw source), primitives that ride it (at least the ones from the top-10 prioritised in your ATMD), attack scenario per primitive with defanged payload shape, success criterion, and the coverage-matrix cell.

**3. Tool-response section.** Same structure for each of search snippet / code-interpreter / external API the target exposes.

**4. Long-term memory section.** Same structure for each of per-user memory / shared corpus / feedback-loop-into-training the target exposes. **Include a latent-activation note** for every payload: what triggers activation (time, content, principal, agent trigger)? A memory payload without a named trigger is not a finding.

**5. Cross-plugin section.** Same structure for each of sub-agent / peer-agent / MCP server / plugin marketplace the target exposes. **Include a cross-principal-reach note**: who writes, whose session fires, what credentials the payload accesses at fire time.

**6. Greshake / InjecAgent grounding.** A short section (200–400 words) mapping your target's channels to the Greshake et al. (2023) taxonomy and to the InjecAgent tool-category taxonomy. Cite the InjecAgent paper's category count and note which of its categories your target does *not* expose.

**7. Handoff notes.** Which exercise-04 harness cells this catalogue populates; which exercise-03 obfuscation vectors will compose with which cells; which findings from this exercise trigger a mod-107 boundary-controls conversation.

### Artefact B — `pieh-<target>-indirect-manifest.yaml`

An extension of exercise 01's manifest with entries for every catalogued indirect payload. Same schema; `matrix_cell` now uses the actual indirect channels.

### Artefact C — `pieh-<target>-latent-trigger-table.md`

A one-page table for the memory + cross-plugin entries listing:

- Payload ID
- Write principal (who plants the payload)
- Fire principal (whose session activates it)
- Trigger condition (time / content / principal / agent)
- Credentials-at-fire (what the payload can do when it fires)
- Cross-tenant reach (yes / no; explain)

## Starter guidance

- **Start from the channel inventory.** The most common mistake is to lead with "what payload can I write" rather than "what channels does this agent actually read from." The inventory forces the second question.
- **Read Greshake et al. §4–5 before you start writing payloads.** The paper's case studies (Bing Chat webpage injection, prompt-injection to leak conversation, cross-service exfil) are the ground truth for the channel primitives. Once you have the primitives, adapting to your target is straightforward.
- **For PDFs, do not underestimate metadata.** Author, Subject, Keywords, custom XMP entries all reach the model when a document-ingest tool is naive. Include at least one metadata-only payload in your catalogue.
- **For code-interpreter, the payload most often lives in a downloaded file or a package's stdout.** Enumerate the interpreter's inputs (fetched URLs, installed packages, environment banners) as the delivery vectors.
- **For memory / shared corpora, the *write* path is the surface.** The catalogue's write-authority column often reveals that the operator has not thought through who can plant a payload — that is a finding on its own, even before you write the payload.
- **For MCP servers, treat the tool *description* as an attack surface, not just the tool responses.** Descriptions enter the model context as summary text; a malicious description drives tool-choice behaviour without any tool call at all.
- **Cross-principal reach is where the interesting harm lives.** If your catalogue does not include at least one cross-tenant task-override scenario, revisit it.
- **Defanged payloads only.** Same as exercise 01.

## Acceptance criteria

- ✅ Channel inventory table covering every sub-channel from chapters 03–04.
- ✅ Exclusions listed with reasoning; no silent omissions.
- ✅ At least one attack scenario per primitive × sub-channel cell the target exposes, up to the depth described above.
- ✅ Greshake / InjecAgent grounding section with concrete references.
- ✅ Latent-trigger table populated for every memory / cross-plugin entry with a named trigger, fire principal, and credentials-at-fire.
- ✅ At least one cross-tenant / cross-principal scenario is authored explicitly.
- ✅ Coverage-matrix cells named in chapter-06 format for every entry.
- ✅ YAML manifest extension covers every entry with IDs, `matrix_cell`, author, date, notes, storage-location placeholder.
- ✅ Any unverified factual claim (specific incident, specific benchmark number, specific ATLAS ID) marked `<!-- needs-research: ... -->`.
- ✅ Handoff notes name what exercise 03 (obfuscation) will layer on top, what exercise 04 (harness) will assemble, and what exercise 05 (defence stress) will target.

## Stretch goals

- **Reproduce one Greshake et al. case study against your target's architecture.** Their Bing-Chat webpage case study is the most transferable; write a version adapted to your target's browser / retrieval integration.
- **Author a machine-readable version of the channel inventory** (`channel-inventory.yaml`) that the harness in exercise 04 can iterate directly.
- **Cross-reference the InjecAgent benchmark's tool categories** against your target's tool inventory and note (a) which InjecAgent categories your target maps to and (b) which of your target's categories InjecAgent does not cover — the delta is where you extend the benchmark for your target.
- **Draft a "poisoned-corpus onboarding" scenario** end-to-end for your target: adversary uploads document → retriever indexes → victim query surfaces it → agent complies. This is the highest-leverage indirect scenario in most enterprise deployments.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. Payloads live outside both repos.
