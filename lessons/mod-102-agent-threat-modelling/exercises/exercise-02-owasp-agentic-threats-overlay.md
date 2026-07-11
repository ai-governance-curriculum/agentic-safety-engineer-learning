# exercise-02 — OWASP Agentic Threats Overlay

**Estimated effort:** 3 hours
**Prerequisite chapters:** 05 (helpful: 06, 07, 08, 09).
**Prerequisite exercise:** exercise-01 (surface + persona matrix).

## Objective

Layer the **OWASP Agentic AI Threats and Mitigations** catalogue over the six-surface enumeration you produced in exercise-01. Populate the surface × threat matrix for your target agent, produce the OWASP LLM Top 10 cross-map, and stub each row with the harm-cause routing (chapter 09) and the mitigation-module reference (mod-107, mod-108, mod-111).

## Problem statement

You have already produced the surface × persona matrix for your target agent in exercise-01. In this exercise you take the *top-10 prioritised* cells from that matrix and populate them into an OWASP-overlay artefact that a reviewer from a chatbot-security background will read as complete.

Your OWASP-overlay artefact must:

- Cite the current published version of "Agentic AI — Threats and Mitigations" (OWASP GenAI Security Project) and the current OWASP LLM Top 10 (2025 edition).
- Cover the eight learning-objective threats at a minimum: memory poisoning, tool misuse, privilege compromise, resource overload, cascading hallucination, identity spoofing, misaligned goals, human-in-the-loop bypass.
- Populate each cell with (a) a concrete threat description scoped to your agent, (b) reachable personas, (c) evidence citation from OWASP + OWASP LLM Top 10 + one incident-corpus source (AIID / OECD.AI / MIT AI Risk Repository) or academic paper, (d) primary-cause and contributing-cause attribution (chapter 09), (e) mitigation-module reference.

## Requirements

Produce two artefacts:

### Artefact A — Overlay document

A Markdown document, ~2500–4000 words, named `atmd-<target>-owasp-overlay.md`. Structure:

1. **Version-observed** header. OWASP Agentic paper version + date read. OWASP LLM Top 10 version + date read.
2. **Threat-by-threat prose.** For each of the eight learning-objective threats, produce a 200–400 word section that:
   - Cites the OWASP threat ID (Agentic + LLM Top 10 correspondents).
   - Describes how the threat manifests on this specific agent, on which surface(s), with what mechanism.
   - Names the reachable persona tiers with the mechanism.
   - Cites at least one non-OWASP piece of evidence (AIID incident, OECD.AI category, MIT AI Risk Repository entry, academic paper, or internal report). If no evidence exists, flag `<!-- needs-research: ... -->`.
   - Attributes primary cause and contributing causes (chapter 09) with justification.
   - References the mitigation module (mod-107, mod-108, mod-111) and the specific mitigation shape expected.
3. **Adjacent OWASP threats.** For the threats OWASP lists but the objective did not enumerate (repudiation & untraceability, unexpected RCE, agent communication poisoning, rogue agents in multi-agent systems, human attacks on multi-agent systems), a short 100–200 word section per applicable threat. Threats not applicable to this deployment are explicitly excluded with reasoning.
4. **OWASP LLM Top 10 cross-map.** A table showing each LLM Top 10 item's correspondents in the Agentic list *for this agent* (some items may not apply — mark them excluded).
5. **Coverage gaps.** A section that names any surface × threat cells the exercise did not populate. Each gap is either (a) not applicable, (b) deferred to a later exercise, or (c) flagged for research. No silent gaps.

### Artefact B — Surface × threat matrix

A structured file (`atmd-<target>-owasp-matrix.yaml` or `.md`) that renders the full surface × threat matrix in one place. Rows are the eight learning-objective threats (plus any adjacent OWASP threats you covered); columns are the six surfaces; cells are one of:

- `POP` — populated, with pointer to the section in Artefact A.
- `X` — explicitly excluded with justification link.
- `?` — flagged `<!-- needs-research: ... -->`.

## Starter guidance

- **Do not paraphrase OWASP.** Cite the exact threat ID and version-observed date. The primary source is authoritative; your artefact adds the agent-specific mapping.
- **Reuse exercise-01's prioritisation.** Populate the top-10 cells first; excluded cells are still explicit.
- **Chapter 05 shows the pattern.** The surface × threat matrix in chapter 05 is the template; your job is to instantiate it for your target agent with concrete threats.
- **Do not attempt to engineer mitigations.** Reference the mitigation module (mod-107 / mod-108 / mod-111) with a one-sentence shape hint. Mod-107 to mod-111 own the depth; this artefact owns the routing.
- **Cross-check OWASP Agentic and OWASP LLM Top 10.** Every ATMD row should point to *both* catalogues if both apply. The two overlap deliberately.

## Acceptance criteria

- ✅ Two artefacts delivered: an overlay document and a surface × threat matrix.
- ✅ Version-observed dates present for both OWASP sources.
- ✅ All eight learning-objective threats have a populated section.
- ✅ Adjacent OWASP threats each have a section or are explicitly excluded with reasoning.
- ✅ Every populated row cites at least one non-OWASP piece of evidence, or carries a `<!-- needs-research: ... -->` marker.
- ✅ Every populated row attributes primary + contributing causes with justification (chapter 09 taxonomy).
- ✅ Every populated row references the mitigation module (mod-107 / mod-108 / mod-111) with a one-sentence shape hint.
- ✅ OWASP LLM Top 10 cross-map table present.
- ✅ Coverage-gap section explicit; no silent gaps in the matrix.

## Stretch goals

- Extend the overlay to the OWASP GenAI Red Teaming Guide's methodology — for each populated threat, sketch the red-team probe that would exercise it. The probe list feeds into mod-111.
- Cross-link every populated row to the corresponding AIID incident number, OECD.AI category, or academic paper arXiv ID as a durable citation.
- Author a **coverage-inversion report**: for every OWASP threat not applicable to this agent, describe what architectural change would make it applicable. Handoff to the `senior-agentic-ai-engineer` peer.
- Produce a machine-readable companion (`atmd-<target>-owasp-overlay.yaml`) so exercise-03 (MITRE ATLAS cross-tag) can join on the same row IDs.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo.
