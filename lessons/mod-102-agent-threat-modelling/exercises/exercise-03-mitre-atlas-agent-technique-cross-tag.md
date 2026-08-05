# exercise-03 — MITRE ATLAS Agent-Technique Cross-Tag

**Estimated effort:** 3 hours
**Prerequisite chapters:** 07 (helpful: 02, 05, 06, 08).
**Prerequisite exercises:** exercise-01 (surface + persona matrix), exercise-02 (OWASP overlay).

## Objective

Layer the **MITRE ATLAS** agent-side technique catalogue over the ATMD you have been building for your target agent. Produce a `mitre_atlas` block that populates both the technique view and the tactic view, cross-tags every row from the OWASP overlay (exercise-02) with its ATLAS technique IDs and case-study references, and includes the version-observed markers that survive MITRE's ID churn. The artefact reads to an infosec reviewer as complete on the ATT&CK-shape spine of the ATMD.

## Problem statement

You have an OWASP-overlay artefact (exercise-02) that populates the surface × threat matrix for your target agent with OWASP Agentic and OWASP LLM Top 10 labels. In this exercise you add the ATLAS spine — the third overlay from chapter 07 — so that the ATMD is legible to a SOC analyst, product-security engineer, or external red-team consultant who reads ATLAS-first.

Your ATLAS-overlay artefact must:

- Cite the currently published MITRE ATLAS matrix with the version-observed date recorded on every technique reference.
- Cross-tag each populated cell from exercise-02's surface × threat matrix with one or more ATLAS technique IDs.
- Populate the ATLAS tactic view (chapter 07) — which tactics are relevant, and which techniques land under each.
- For each cited technique, cite at least one specific ATLAS **case study** (or an academic paper analogue where no case study applies) rather than the technique alone.
- Explicitly note where ATLAS does not currently cover the surface (multi-agent-emergent harms, HITL-presentation manipulation, RAG-index cross-tenant amplification) and how you augment.

## Requirements

Produce two artefacts.

### Artefact A — Overlay document

A Markdown document, ~2000–3500 words, named `atmd-<target>-atlas-overlay.md`. Structure:

1. **Version-observed header.** MITRE ATLAS matrix version + date-observed. Note the URL for the technique catalogue and the case-study collection you consulted.
2. **Technique-by-technique prose.** For each ATLAS technique that lands on your target agent, produce a 150–300 word section that:
   - Cites the `AML.T####` ID and the version-observed date.
   - Describes how the technique manifests on this specific agent, on which surface(s) (chapter 02), against which persona tiers (chapter 03).
   - Cross-references the OWASP Agentic and OWASP LLM Top 10 rows this technique tags (exercise-02).
   - Cites at least one ATLAS case study (`AML.CS####`) that grounds the technique, or an academic paper that serves the same evidence function if no case study applies.
   - References the harm-cause attribution (chapter 09) for the primary + contributing causes and the fix-owner routing.
   - Names the mitigation module (mod-107 / mod-108 / mod-111) with a one-sentence shape hint.
   Cover at minimum the load-bearing agent-side techniques from chapter 07 (LLM prompt injection, LLM jailbreak, LLM plugin compromise, LLM data leakage, publish poisoned datasets, erode dataset integrity, LLM trusted output components manipulation, LLM prompt self-replication). If a technique does not apply to your target agent, exclude it explicitly with reasoning.
3. **Tactic view table.** A table showing every ATLAS tactic column and which techniques from section 2 land under each tactic for this deployment. Include a "coverage" column marking tactics as covered / partial / not-applicable with justification.
4. **Where ATLAS is thin for this agent.** A short section naming the surfaces or threats where ATLAS does not cover well (chapter 07): multi-agent-emergent, HITL-presentation, cross-tenant amplification, or any deployment-specific gap. Describe your augmentation (academic paper, OWASP entry, internal telemetry) for each gap.
5. **Cross-map to OWASP.** A table joining every OWASP row from exercise-02 with its ATLAS technique IDs. Rows with no ATLAS analogue are marked `no current mapping` and flagged for possible ATLAS-project contribution.
6. **Handoff to `security` / `ai-infra-security` peer (level 35).** A short section identifying the ATMD rows this overlay hands to the platform-security peer: which SIEM signals, egress policies, DLP rules, container-hardening or network-segmentation changes each row implies.

### Artefact B — Structured `mitre_atlas` block

A YAML file (`atmd-<target>-atlas.yaml`) or a structured section appended to the exercise-02 companion YAML. For each cited technique:

```yaml
mitre_atlas:
  - id: AML.T0051                          # example
    title: LLM Prompt Injection
    version_observed: "YYYY-QN"             # matrix version you read
    source_url: https://atlas.mitre.org/techniques/AML.T0051
    surfaces: [data_input, environment_observation]
    personas: [tier_1, tier_2, tier_4]
    owasp_rows:                             # cross-ref exercise-02 row IDs
      - <owasp-row-id-1>
      - <owasp-row-id-2>
    case_study_refs:
      - id: AML.CS####
        title: <case study title>
        note: <one-line disanalogy if any>
    harm_cause: {primary: model, contributing: [operator, data]}
    mitigation_module: mod-108
    saif_pillars: [3, 5]                    # chapter 08 cross-ref
```

Every technique cited in Artefact A must appear in Artefact B with the same fields populated.

## Starter guidance

- **Verify every ID before you commit it.** ATLAS renumbers between releases. Read the current matrix and re-verify each `AML.T####` mentioned in chapter 07 before adopting it in the ATMD. If the historical ID from the chapter is deprecated, cite the current equivalent and add a `deprecated_from` note.
- **The tactic view is the reviewer's first read.** An infosec reviewer opens ATLAS by tactic (initial access, execution, persistence, exfiltration, impact). Populate that view first; the technique detail is scaffolding under it.
- **Cite the case study, not just the technique.** A row that cites `AML.T0051 (LLM Prompt Injection)` alone is a checklist item. A row that cites `AML.T0051` *plus* a specific `AML.CS####` case study is a defensible finding. See chapter 07's "Reading ATLAS case studies as evidence" section.
- **Do not paraphrase MITRE's technique descriptions.** Cite them by ID and add the agent-specific instantiation. The primary source is authoritative.
- **Use exercise-02's row IDs as the join key.** Every OWASP row from exercise-02 should get either an ATLAS ID or a `no current mapping` flag. That join is what makes the multi-overlay ATMD reviewable end-to-end.
- **Flag augmentation gaps as `<!-- needs-research: propose ATLAS technique -->`.** Novel agent-specific threats that have no current ATLAS mapping are worth marking for potential upstream contribution.

## Acceptance criteria

- ✅ Two artefacts delivered: an overlay document (~2000–3500 words) and a structured `mitre_atlas` block.
- ✅ Version-observed date recorded on every ATLAS technique reference.
- ✅ Every load-bearing agent-side technique named in chapter 07 is either populated with an agent-specific section or explicitly excluded with reasoning.
- ✅ Tactic view table present with per-tactic coverage marking.
- ✅ Every populated technique cites at least one case study (or academic-paper analogue) with a one-line disanalogy note if it is not a direct match.
- ✅ Cross-map table joins every exercise-02 OWASP row to its ATLAS ID(s), with `no current mapping` flags where applicable.
- ✅ "Where ATLAS is thin" section names gaps and augmentation.
- ✅ Handoff section identifies concrete platform-side items for the `security` / `ai-infra-security` peer.
- ✅ Structured `mitre_atlas` YAML validates and every technique has `id`, `version_observed`, `source_url`, `surfaces`, `personas`, `owasp_rows`, `case_study_refs`, `harm_cause`, and `mitigation_module` fields.

## Stretch goals

- Extend the tactic view into a full **ATT&CK-shape kill-chain narrative** for the top-3 attack scenarios exercise-01 flagged. Walk one persona through reconnaissance → resource-development → initial-access → execution → persistence → impact for that scenario using ATLAS techniques as the vocabulary.
- Publish an *ATLAS contribution proposal* for any surface × threat cell that has `no current mapping`. Frame it as a MITRE-ATLAS-community pull-request-shape write-up (technique name, description, sub-techniques, mitigations, references). The contribution does not need to be filed; the write-up is the deliverable.
- Cross-link every ATLAS technique in Artefact B to its SAIF pillar controls (chapter 08) so the three-overlay ATMD (OWASP × ATLAS × SAIF) is joinable on shared IDs.
- Produce a **detection-content stub** for each ATLAS technique your artefact populates: what SIEM signal / rule / detection query would fire when the technique executes against your target agent. Handoff to `ai-infra-security` peer.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo.
