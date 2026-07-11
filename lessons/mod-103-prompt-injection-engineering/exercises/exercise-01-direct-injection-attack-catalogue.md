# exercise-01 — Direct Injection Attack Catalogue

**Estimated effort:** 2 hours
**Prerequisite chapters:** 01, 02 (helpful: 06, 09).

## Objective

Produce a **direct-injection attack catalogue** for one concrete tool-using agent — one entry per direct-injection primitive from chapter 02, with a defanged payload shape, an OWASP LLM01 + MITRE ATLAS mapping, a success criterion, and the coverage-matrix cell it lands on. The catalogue is the first population of your PIEH replay-bundle manifest (chapter 06) and the artefact you extend across exercises 02–05.

## Problem statement

Pick a concrete tool-using agent as your target for this module. Reuse the target you chose in mod-102 exercise 01 if you already have an Agent Threat Model Document (ATMD) — the ATMD's ranked six-surface × persona matrix is the *input* the direct-injection catalogue prioritises against. Otherwise, pick from these three:

- **Customer support agent** — reads support tickets, searches an internal KB and public docs, drafts replies for human review, and with human-in-the-loop approval issues refunds up to $500 and re-schedules subscription renewals.
- **Coding agent** — has code interpreter + shell in a sandbox + checked-out git branch + PR-open tool. HITL approval on PR open.
- **Research agent** — has browser + search + read-file + write-notes + call-peer-agent tools. Persistent memory of prior searches per user.

For the chosen agent, author a catalogue that covers all six direct-injection primitives from chapter 02 — instruction-override, refusal-bypass, role-hijack, task-override, exfiltration-through-completion, output-format hijack — through the *direct* channel (the user prompt). Indirect channels come in exercise 02; obfuscation in exercise 03.

## Requirements

Produce two artefacts:

### Artefact A — `pieh-<target>-direct-catalogue.md`

A Markdown catalogue, ~1500–2500 words, with one section per primitive. Each section contains:

- **Primitive** and its chapter-02 definition, restated in one sentence.
- **OWASP LLM01 sub-class** and the co-labelled Top 10 items (e.g., LLM02 for exfil primitives, LLM05 for output-format-hijack).
- **MITRE ATLAS technique ID(s)** with the ATLAS version noted (IDs churn).
- **Attack scenario** — a two- or three-paragraph description of who does what, why, and to whom, framed against your target agent.
- **Payload *shape*** (not a working weaponised string) — the template or category, with placeholders where the attacker specifics go. Illustrations from chapter 02 are the style guide. Do not paste a working exploit into this file.
- **Success criterion** — precisely what "the payload worked" means for this primitive against your target. For task-override, include the divergence between tool trajectory and user-facing summary.
- **Coverage-matrix cell** — the `(primitive, channel, obfuscation)` triple in chapter 06's format. For this exercise every cell is `(primitive, direct.user_prompt, none)`.
- **Judge sketch** — a paragraph on how you would score this cell in the harness (rubric, LLM-judge, deterministic parser). Cite chapter 06 patterns.

### Artefact B — `pieh-<target>-manifest-draft.yaml`

A YAML file in the chapter-06 manifest format. One entry per primitive with:

- `id` — a stable identifier (e.g., `PIEH-DIR-INST-001`).
- `matrix_cell` — the triple.
- `sha256` — a placeholder here; you will populate it when you author the actual payload into the external store (do *not* store the payload in this exercise's deliverable).
- `author` — your handle.
- `added` — today's date (ISO).
- `notes` — what the payload is intended to elicit and against which target-agent configuration.
- `storage_location` — the external store URI (a placeholder in this exercise; a real one when the harness is stood up in exercise 04).

## Starter guidance

- **Start from the primitive definitions, not from your favourite payload.** The instinct is to lead with the payload you know works. Instead, take chapter 02's primitive one at a time and construct an agent-specific attack scenario for each. This forces you to notice which primitives don't have a natural landing site against your target — an important finding on its own.
- **Task-override is the highest-consequence primitive in an agent setting.** Spend real time on the success-criterion definition: the tool-call log divergence vs. the user-facing summary. If your target has HITL on high-stakes actions, task-override composes with HITL-bypass — note that but do not build the HITL-bypass itself here (it is exercise 02 or 05 territory depending on where the bypass lands).
- **Exfil-through-completion is the primitive most tied to the renderer.** Note explicitly whether the target agent's chat UI renders markdown, HTML, or plain text; the primitive lands differently on each.
- **Output-format hijack requires a downstream parser to hijack.** Look at what parses the model's output in your target — the tool-argument validator, the client JSON parser, the sub-agent input parser. Different parsers → different sub-variants of the primitive.
- **Cite the ATLAS version explicitly.** The ID for `Prompt Injection` (AML.T0051) may have moved between the version you cite and the one your reviewer looks up.
- **Do not paste working payloads.** Payload *shapes* only. Chapter 06 explains why. If you must record a working payload for your own reference, it goes in the external store, not here.

## Acceptance criteria

- ✅ Single Markdown catalogue with a section per direct-injection primitive (six sections total).
- ✅ Each section names the OWASP LLM01 sub-class, at least one co-labelled Top 10 item where applicable, and a MITRE ATLAS technique ID with version.
- ✅ Each section carries a defanged payload shape (not a working weaponised string), an agent-specific attack scenario, a precise success criterion, and a judge sketch.
- ✅ Each section names the coverage-matrix cell it lands on in chapter 06 format.
- ✅ Companion YAML manifest with one entry per primitive in chapter-06 manifest format.
- ✅ No working payloads in either artefact. Payload store URIs are placeholders here.
- ✅ Any un-verified factual claim (specific ATLAS ID, specific OWASP numbering, specific incident) is marked `<!-- needs-research: ... -->` rather than guessed.
- ✅ Handoff notes at the end naming what exercise 02 will extend (channels), exercise 03 will extend (obfuscation), exercise 04 will assemble (harness), and exercise 05 will stress (defences).

## Stretch goals

- **Add an "against-what-defence" column** to each primitive entry sketching what an operator's baseline defence stack (system-prompt hardening + a naive refusal classifier) would do to your payload shape. This gives exercise 05 a head-start on the defence catalogue.
- **Draft judge rubrics** in more detail — a checklist of observations the judge scores, with pass / fail / partial thresholds. This is chapter 06's judge-versioning material started early.
- **Cross-reference your mod-102 ATMD** cell-by-cell: for each of the top-10 (surface × persona) cells your ATMD prioritised, note whether the direct-injection catalogue covers it and by which primitive.
- **Author a per-primitive "how do I know the payload is working?" observability note** — what production trace field, monitoring signal, or online-eval judge would fire on this primitive if it succeeded in the wild.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference catalogue. Working payloads (as opposed to defanged shapes) never live in either repo — see chapter 06's harmful-payload discipline.
