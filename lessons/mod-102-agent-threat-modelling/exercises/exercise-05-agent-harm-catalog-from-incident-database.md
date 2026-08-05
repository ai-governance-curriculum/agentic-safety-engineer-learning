# exercise-05 — Agent Harm Catalogue from the AI Incident Database

**Estimated effort:** 2 hours
**Prerequisite chapters:** 04 (helpful: 01, 02, 09).
**Prerequisite exercises:** none required; exercise-01 helps you scope the surfaces to slice against.

## Objective

Produce the **incident-grounding** section of the ATMD: a harm catalogue drawn from the AI Incident Database (AIID), the OECD.AI Incidents Monitor, and the MIT AI Risk Repository, sliced through the six-surface lens (chapter 02) and cited with durable identifiers. Every row in the catalogue carries at least one piece of evidence; rows without a corpus hit are augmented from the OWASP threat catalogues, academic red-team papers, ATLAS case studies, or internal telemetry, and are labelled as such.

## Problem statement

The published incident corpora skew heavily to pre-agent chat, image-generation, recommender-system, and classical-ML harms; agent-specific incidents are undercounted (chapter 04). Your job is to (a) slice the corpora *by surface shape* rather than by the literal word "agent," (b) fill the coverage gaps with OWASP entries and academic evidence, and (c) produce a catalogue whose every row would answer a regulator's *"how do you know this is a real risk?"* question with a durable citation.

Pick a scope: either your target agent from exercise-01, or the general shape of tool-using agents at your organisation. Either way, the deliverable is a citable evidence stack the ATMD can attach to each row.

## Requirements

Produce one Markdown artefact (~1200–2000 words) plus one structured companion file. Structure:

### Artefact A — Prose catalogue

Named `atmd-<target>-incident-grounding.md`. Structure:

1. **Corpora accessed and version-observed.** For each of AIID, OECD.AI Incidents Monitor, and MIT AI Risk Repository: the URL, the date accessed, the query / filter used, and the count returned. This is the transparency layer that lets a reviewer reproduce the slice.
2. **One row per surface (chapter 02).** For each of the six surfaces — data-input, tool-invocation, memory / VS, environment-observation, HITL, cross-agent — produce a section (~150–300 words) that:
   - Names the surface and the threat-type you sliced for (e.g., "data-input surface, indirect-prompt-injection incidents").
   - Cites at least two AIID incident numbers (or the closest analogues, noting the disanalogy). If AIID returns zero direct hits, cite the closest recommender-system / chatbot / automation analogue and mark the disanalogy explicitly.
   - Cites at least one OECD.AI Incidents Monitor category rollup.
   - Cites at least one MIT AI Risk Repository entry (by ID).
   - Adds compensating evidence: OWASP Agentic / LLM Top 10 threat ID, MITRE ATLAS technique + case study, academic paper (arXiv ID), or internal red-team / bug-bounty finding.
   - States the primary + contributing cause (chapter 09) drawn from the evidence stack.
3. **Coverage-gap ledger.** A section listing every surface × threat cell where the corpora returned zero direct hits. For each gap, name the compensating evidence source and the `<!-- needs-research: revisit next quarterly slice -->` cadence for re-check.
4. **Handoff to `ai-risk-engineer` prerequisite.** A one-paragraph note describing what changed from the prerequisite's general harm catalogue — which rows this catalogue *adds* (agent-specific) and which rows it *inherits* (still owned by the prerequisite).

### Artefact B — Structured evidence stack

A YAML file (`atmd-<target>-incident-grounding.yaml`) with one entry per surface row from Artefact A. Follow the chapter 04 template:

```yaml
- surface: memory_vector_store
  threat: memory poisoning of cross-session RAG index
  personas: [tier_2, tier_3, tier_4, tier_5]
  evidence:
    aiid:
      - incident_id: <#>
        url: https://incidentdatabase.ai/cite/<#>
        note: <disanalogy if not a direct agent-shaped precedent>
    oecd_ai:
      - category_id: <id>
        url: <url>
    mit_air:
      - entry_id: <id>
        url: <url>
    owasp:
      - agentic: T1
        version_observed: "YYYY-QN"
      - llm_top10: LLM04
        version_observed: "2025"
    atlas:
      - id: AML.T####
        case_study_id: AML.CS####
        version_observed: "YYYY-QN"
    academic:
      - arxiv_id: "2404.xxxxx"
        title: <paper title>
    internal:
      - ticket_id: <internal ref, or "none">
  causal_owner: {primary: data, contributing: [operator]}
  needs_research: [<gaps as bullets>]
```

Every row from Artefact A has one YAML entry. Every YAML entry has at least one non-empty evidence sub-list.

## Starter guidance

- **Slice by surface shape, not by keyword.** Filtering AIID for the token "agent" excludes 90% of the relevant precedents. Slice for the *behaviour*: tool-triggered actions, retrieval-based misuse, persistent-state poisoning, HITL bypass, cross-tenant leakage.
- **Recommender-system and moderation-model incidents are legitimate precedents.** Many AIID rows predate the agent-era vocabulary but exhibit the same surface behaviour (memory poisoning as recommender feedback-loop gaming, indirect injection as SEO poisoning). Cite the analogue and note the disanalogy.
- **The MIT AI Risk Repository covers the space where AIID is empty.** Because it is a taxonomy of *risks* rather than a corpus of incidents, it fills coverage gaps for threats that have not yet manifested in press coverage. Chapter 04 explains the split.
- **OWASP and ATLAS are legitimate evidence sources.** When the incident corpora return zero, the practitioner-consensus catalogues and the ATLAS case-study set carry the burden. Chapter 04 makes this explicit: rows without corpus hits are still defensible if the OWASP / ATLAS / academic evidence stack is present.
- **Cite by durable ID, always.** AIID incident number, OECD.AI category ID, MIT AI Risk Repository entry ID, ATLAS technique + case-study ID, arXiv ID. News-article URLs decay; the durable identifier does not.
- **Note the version-observed date.** Corpus contents and OWASP / ATLAS IDs change; the observed-on date localises your citation.

## Acceptance criteria

- ✅ Two artefacts delivered: a prose catalogue (~1200–2000 words) and a structured YAML companion.
- ✅ Corpora accessed section names URL, date, filter, and count returned for each of AIID / OECD.AI / MIT AI Risk Repository.
- ✅ All six surfaces have a populated section with at least one AIID citation (or an explicit "zero direct hits, cite analogue X with disanalogy Y").
- ✅ Every populated section cites at least one OECD.AI rollup, one MIT AI Risk Repository entry, and at least one compensating source (OWASP / ATLAS / academic / internal).
- ✅ Every row has a primary + contributing cause attribution using the chapter 09 taxonomy.
- ✅ Coverage-gap ledger present with re-check cadence per gap.
- ✅ YAML companion validates and every row has at least one non-empty evidence sub-list.
- ✅ Handoff paragraph names the `ai-risk-engineer` prerequisite catalogue and describes the added-vs-inherited split.

## Stretch goals

- Automate the AIID slice: use the AIID public JSON snapshot to programmatically filter for incidents whose taxonomy tags overlap with the six-surface lens, and produce a shortlist. Commit the filter script (or command line) alongside the catalogue so the slice is reproducible on the next quarterly re-check.
- Produce a **corpus-completeness heatmap**: a table with one row per surface and one column per corpus, coloured by direct-hit count. The pattern of empty cells is itself a finding — it shows where the agent-relevant literature is thin.
- Cross-map every catalogue row to a MITRE ATLAS case study (`AML.CS####`) — if one exists. Where none exists, note it as a candidate for community contribution.
- Extend the catalogue with an **internal-telemetry section**: bug-bounty submissions, red-team findings, production abuse reports for your organisation's own agent deployments (chapter 04's most agent-specific evidence source). Redact identifiers as required, but do include the row shape.
- Author a **regulator-facing summary** of the catalogue (~500 words) framed for an EU AI Office / AISI / regulator reader: what evidence base, what confidence level, what cadence for re-review. This is the pre-form of the disclosure artefact mod-112 owns.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable (or the raw corpus slices) into this course repo. Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo.
