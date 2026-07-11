# 04 — Grounding in the AI Incident Database, OECD.AI, and MIT AI Risk Repository

## Motivation

A threat model that lists threats the author thought of is *creative writing*. A threat model that lists threats *the world has observed* is evidence-grounded. The difference matters when a reviewer asks "how do you know this is a real risk?" and it matters when a regulator asks "why is this in your risk register?"

The level-25 prerequisite gave you fluency with three incident corpora — AI Incident Database (AIID), OECD.AI Incidents Monitor, and MIT AI Risk Repository. This chapter does not re-teach how to use them. It shows how to slice them for *agent-relevant* incidents, how to reason about **what the corpora undercount** (most published incidents predate broad agent deployment), and how to cite them in the ATMD so the harm catalogue lands with evidence next to each row.

## The three corpora at a glance

### AI Incident Database (AIID)

Curated by the Responsible AI Collaborative. Structured entries by incident (numerical ID), each with a narrative report, taxonomies, and cited news sources. Machine-readable JSON snapshots are published. Coverage skews toward publicly reported incidents in the English-language press, which means it under-samples enterprise-internal incidents and non-English harms.

Key affordances for this role:

- **Query by taxonomy** — CSET taxonomy (harm type, sector) and GMF taxonomy are attached to many incidents. You can filter for LLM-related, chatbot, agent-adjacent, code-execution, or automation-related incidents.
- **Cite by incident number** — every incident has a stable numeric ID, so ATMD entries can reference `AIID incident 123` with a durable URL.
- **Read the report narrative, not just the headline** — the report often captures the causal chain in enough detail to draw a threat-model row from it.

<!-- needs-research: pull a current snapshot of the AIID incident count and confirm the currently published taxonomies (CSET, GMF, Goose) match what the module cites. -->

### OECD.AI Incidents Monitor

Maintained under the OECD.AI Policy Observatory. Aggregates AI-related incidents from press coverage, with jurisdiction and sector tags. Complements AIID because it applies a policy-monitor lens (incident *categories* rather than incident *entries*).

Key affordances:

- **Jurisdictional slicing** — useful for EU AI Act / UK / US regulatory grounding.
- **Category rollups** — patterns across incidents rather than individual entries.
- **Regulatory-facing citation** — regulators recognise the OECD.AI source directly.

<!-- needs-research: confirm current URL, data-refresh cadence, and category schema of the OECD.AI Incidents Monitor. -->

### MIT AI Risk Repository

An academic taxonomy: it catalogues *risks* (not incidents) sourced from the peer-reviewed and grey literature. Each entry has a causal-factor and hazard-domain tag. The strength here is completeness of the *space of risks*, not the frequency of incidents.

Key affordances:

- **Causal-factor grounding** — helps distinguish operator- vs. model- vs. data-caused harms (chapter 09) with citation.
- **Domain coverage** — covers risks that have not yet manifested in press coverage, which is useful for tier-boundary reasoning.
- **Numerical IDs** — durable citation.

<!-- needs-research: confirm the current published version and citation format of the MIT AI Risk Repository. -->

## How to slice for agent-relevant incidents

The published incident corpora skew toward pre-agent chat, image-generation, recommender-system, and classical-ML harms. When you slice for agent relevance, apply the six-surface lens (chapter 02) as the filter:

1. **Any incident involving indirect prompt injection, tool-triggered actions, retrieval-based misuse, or code-execution outputs** touches the tool-invocation and/or environment-observation surfaces. Prioritise.
2. **Any incident involving persistent state, cross-session behaviour, or shared knowledge base contamination** touches the memory / vector-store surface. These are relatively new; the corpora undercount them.
3. **Any incident involving human-in-the-loop bypass, approval fatigue, or over-trusted automation** touches the HITL surface. Look for automation-related workplace incidents even when they are not tagged as LLM-related.
4. **Any incident involving multi-model or multi-agent orchestration** touches the cross-agent surface. This category is nascent — expect to write forward-looking ATMD rows with *analogous* incidents rather than exact matches.
5. **Any incident involving misused credentials, over-broad permissions, or unintended data disclosure** touches insider and cybercrime-affiliate personas. Slice by these regardless of whether the incident is agent-shaped.

A common trap: filtering only for incidents whose narrative explicitly says "agent." Most agent-relevant harms in the corpora predate the "agent" label; the *shape* of the incident is what matters.

## What the corpora undercount and how to compensate

You will not find enough published agent incidents to populate a comprehensive threat model. Compensate as follows:

- **Read the OWASP GenAI project's threat catalogues as a *forward-looking* corpus.** OWASP Agentic AI Threats and Mitigations and the LLM Top 10 are compiled from practitioner reports, not from AIID-shape published incidents. When a threat has no AIID entry yet, cite the OWASP source as the empirical basis.
- **Read the MITRE ATLAS case-study collection.** ATLAS maintains a case-study set with each technique. Many of these are enterprise-security examples that map onto agent surfaces with translation.
- **Read the academic red-team literature** — Greshake et al. on indirect prompt injection, Zhan et al. on InjecAgent, Debenedetti et al. on AgentDojo, Wang et al. on multi-agent adversarial coordination — as *incident-shape* evidence. Peer-reviewed adversarial demonstrations are legitimate evidence for a threat model even if no press-reported harm has occurred yet.
- **Read frontier-lab and AISI publications** — Anthropic, OpenAI, and Google DeepMind system cards; UK / US AISI pre-deployment evaluations. These are curated empirical demonstrations of agent misbehaviour.
- **Read your own operator-side telemetry** — production abuse reports, bug-bounty submissions, internal red-team findings. These are the most agent-specific evidence available to you, and they should be cited in the ATMD.

For each ATMD row, cite at least *one* piece of evidence — AIID / OECD.AI incident, OWASP entry, academic paper, ATLAS case study, or internal report. Rows without evidence are hypotheses; keep them but label them `<!-- needs-research: ... -->` in the artefact so a reviewer can tell.

## A worked example: memory-poisoning row

Suppose you are populating the memory-poisoning row of the ATMD for the Acme support agent (chapter 03). The evidence stack looks like:

- **AIID** — search for `poisoning`, `chatbot`, `training data`, `recommender`. Expect to find recommender-system poisoning cases (analogous but not identical), pre-agent chatbot poisoning, and adjacent data-quality incidents. Cite each by incident number and note the disanalogy in the ATMD.
- **OECD.AI** — filter for the "data governance" or "data quality" category and note whether jurisdictions treat the incident as regulatory-material.
- **MIT AI Risk Repository** — the risk category "compromised training data" or equivalent. Cite the entry ID.
- **OWASP Agentic AI Threats and Mitigations** — T1 Memory Poisoning is the direct label. Cite the OWASP entry as the practitioner-consensus source. <!-- needs-research: confirm the current OWASP Agentic threat-ID scheme against the latest published version. -->
- **OWASP LLM Top 10** — LLM04 Data and Model Poisoning is the closest LLM-generic label. Cite for cross-checking.
- **Academic** — Zhan et al. (InjecAgent) demonstrates injection through retrieval; Debenedetti et al. (AgentDojo) demonstrates cross-tool exploitation including memory writes. Cite arXiv IDs.
- **Internal** — cite any bug-bounty or red-team finding your organisation has that matches.

The row that lands in the ATMD:

```yaml
- surface: memory_vector_store
  threat: memory poisoning of cross-session RAG index
  personas: [tier_2, tier_3, tier_4, tier_5]
  evidence:
    aiid: []                     # no direct match; note the gap
    oecd_ai: []
    mit_air: [<risk_id>]
    owasp:
      - agentic: T1
      - llm_top10: LLM04
    academic:
      - injecagent (Zhan et al. 2024)
      - agentdojo (Debenedetti et al. 2024)
    atlas: [Publish Poisoned Datasets, Erode Dataset Integrity]  # <!-- needs-research: confirm current ATLAS IDs -->
    internal: []
  causal_owner: data + operator (see chapter 09)
  mitigation_refs: [mod-108 guardrails, mod-107 containment]
```

Even where AIID / OECD.AI provide no direct hit, the row is defensibly cited because the OWASP entries, MIT AI Risk Repository entry, and academic demonstrations carry the burden. That is a legitimate ATMD row.

## Citation discipline

- **Cite by durable identifier.** AIID incident number, OECD.AI category ID, MIT AI Risk Repository entry ID, ATLAS technique ID, arXiv ID. Do not cite news headlines; cite the entry that references them.
- **Cite the version.** OWASP threats and ATLAS techniques renumber. Note the observed-on date and the published version.
- **Note the disanalogy.** If the closest AIID hit is a pre-agent recommender-system poisoning, say so in the ATMD row — do not pretend it is a direct agent-shaped precedent.
- **Label your gaps.** Use `<!-- needs-research: no AIID hit found for this row; revisit after next quarterly slice -->` inline, so the reviewer sees the gap explicitly.
- **Update on cadence.** Re-slice the corpora quarterly (or more often if the agent deployment is on a fast tier — chapter 10). New AIID / OECD.AI entries land continuously.

## Handoff to `ai-risk-engineer` (prerequisite, level 25)

The general fluency with AIID / OECD.AI / MIT AI Risk Repository belongs to the prerequisite role and is not re-taught here. The level-40 delta is:

- **Agent-relevant slicing.** The prerequisite slices for enterprise ML harms in general; this role slices for the six-surface, six-persona agent-specific harms.
- **Evidence-stack authoring.** The prerequisite cites individual sources; this role authors the composite evidence stack (AIID + OECD.AI + MIT AI Risk Repository + OWASP + ATLAS + academic + internal) per ATMD row.
- **Forward-looking augmentation.** The prerequisite treats the corpora as ground truth; this role explicitly reasons about *what they undercount* and augments with academic and internal evidence.

## Common misreadings to avoid

- **"If AIID has no matching incident, the risk isn't real."** No. AIID is a curated corpus of *publicly reported* incidents. Absence of an AIID entry is a data-availability statement, not a risk statement.
- **"OECD.AI is a duplicate of AIID."** They overlap but serve different functions — AIID for incident-level detail, OECD.AI for policy-relevant category rollups. Cite both when the row is regulatory-material.
- **"MIT AI Risk Repository is academic, not for engineering."** The Repository is a taxonomy backed by peer-reviewed literature. Its role in the evidence stack is *coverage of the risk space*, not incident frequency.
- **"OWASP is authoritative and we can skip the incident corpora."** OWASP is practitioner-consensus and load-bearing but does not carry the empirical incident weight regulators expect. Cite both.

## Summary

- Three corpora: **AIID** (curated incidents by numeric ID), **OECD.AI Incidents Monitor** (policy-oriented rollups), **MIT AI Risk Repository** (taxonomy of risks from the literature).
- Slice the corpora through the six-surface lens (chapter 02) — do not filter on the word "agent."
- The corpora undercount agent-specific harms; compensate with OWASP catalogues, academic red-team papers, ATLAS case studies, frontier-lab system cards, and internal telemetry.
- Every ATMD row cites at least one piece of evidence; unfilled rows carry `<!-- needs-research: ... -->` markers.
- Cite by durable identifier and version-marker; re-slice on a defined cadence.
