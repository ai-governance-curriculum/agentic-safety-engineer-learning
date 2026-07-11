# mod-102 — Threat Modelling for Autonomous and Tool-Using Agents

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 14 hours (≈1 hour lecture reading + 13 hours exercises)

## Why this module exists

The `ai-risk-engineer` (level 25) prerequisite already gives you a general engineering threat-modelling craft — STRIDE / LINDDUN discipline, harm-model authoring against the AI Incident Database and MIT AI Risk Repository, NIST AI 100-2 taxonomy fluency, OWASP ML Top 10, and general LLM misuse patterns. That craft *stops working* the moment the system under test can **take actions in the world**. A chat-only LLM behind a form has a small blast radius; a tool-using agent that can search, email, schedule, execute code, transact, or spawn sub-agents does not.

An agent-specific threat model has to enumerate surfaces that a text-in-text-out threat model cannot see: **data-input**, **tool-invocation**, **memory / vector-store**, **environment-observation**, **human-in-the-loop**, and **cross-agent**. It has to reason about adversaries who are not just prompt tinkerers — insiders who own credentials, cybercrime affiliates who own supply chains, and nation-state actors seeking capability uplift. And it has to route each finding to the owner who can actually fix it: product, model, retrieval, tools, orchestration.

This module gives you that craft, and then layers four defensible checklists on top — OWASP Agentic AI Threats and Mitigations, OWASP LLM Top 10, MITRE ATLAS agent-side techniques, and Google SAIF pillars — so the threat model you emit is not just complete-to-you but complete-to-a-reviewer. Finally, it borrows the RSP / Preparedness / FSF *shape* (mod-101) and applies it downstream: enterprise agent-deployment tiering, so an "internal read-only Q&A agent" and a "customer-facing agent with a billing tool and a refund tool" land in different tiers with different mitigation contracts.

## Learning objectives

By the end of the module you can:

1. Author an agent-specific threat model across the **six surfaces** (data-input, tool-invocation, memory / vector-store, environment-observation, human-in-the-loop, cross-agent) with an explicit adversary-persona ladder from script-kiddie jailbreaker through insider through nation-state uplift-seeker, grounded in the AI Incident Database and OECD.AI Incidents Monitor.
2. Layer **OWASP Agentic AI Threats and Mitigations**, **OWASP LLM Top 10**, **MITRE ATLAS** agent-side techniques, and **Google SAIF** pillars over that threat model to make it defensibly complete to an external reviewer.
3. Distinguish **operator-caused, model-caused, data-caused, integration-caused, and multi-agent-emergent** harms so the mitigation contract routes to the right owner (product, model, retrieval, tools, orchestration, program).
4. Reason about frontier-lab risk-tiered frameworks (RSP / Preparedness / FSF) as the **shape template** for enterprise agent-deployment tiering.
5. Coordinate with the `ai-risk-engineer` (prerequisite, level 25) on the general harm-model artefacts this role starts from, and with `senior-agentic-ai-engineer` (peer, agentic AI family) on the agent architecture being threat-modelled.

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Why agents break the general threat-model prerequisite](01-why-agents-break-general-threat-modelling.md) | Names the delta between the level-25 general harm-model craft and the agent-specific craft this role owns. Sets up the six-surface model. |
| 02 | [The six-surface agent attack model](02-six-surface-attack-model.md) | Data-input, tool-invocation, memory / vector-store, environment-observation, human-in-the-loop, cross-agent — the enumeration you build every subsequent overlay against. |
| 03 | [Adversary personas from script-kiddie to nation-state](03-adversary-personas.md) | A persona ladder with capability, intent, and cross-surface reach for each tier. Grounds the "who could exploit this" column of the threat model. |
| 04 | [Grounding in the AI Incident Database, OECD.AI, and MIT AI Risk Repository](04-grounding-in-incident-corpora.md) | The three empirical corpora this role reads before authoring — how to query them, what they cover, and what they miss. |
| 05 | [OWASP Agentic AI Threats and Mitigations overlay](05-owasp-agentic-threats-overlay.md) | Memory poisoning, tool misuse, privilege compromise, resource overload, cascading hallucination, identity spoofing, misaligned goals, human-in-the-loop bypass — cross-mapped to the six surfaces. |
| 06 | [OWASP LLM Top 10 (2025) through the agent lens](06-owasp-llm-top-10-agent-lens.md) | LLM01–LLM10 re-read for tool-using agents rather than chat-only apps. Marks which items the level-25 prerequisite already owns and which this role deepens. |
| 07 | [MITRE ATLAS agent-side techniques overlay](07-mitre-atlas-agent-overlay.md) | Adopt ATLAS technique IDs for prompt injection, jailbreak, plugin compromise, LLM data leakage, and the agent-adjacent primitives. Includes an ID-churn note. |
| 08 | [Google SAIF pillars overlay](08-google-saif-pillars-overlay.md) | The six SAIF elements applied as an operator-side control catalogue over the six-surface model. |
| 09 | [Harm-cause attribution: the routing contract](09-harm-cause-attribution.md) | Operator-caused / model-caused / data-caused / integration-caused / multi-agent-emergent — the taxonomy that makes the mitigation contract route to the right owner. |
| 10 | [Enterprise agent-deployment tiering and role coordination](10-enterprise-agent-tiering-and-coordination.md) | Reuse the RSP / Preparedness / FSF shape (mod-101) for enterprise agent deployments. Codify the handoff with `ai-risk-engineer` (prerequisite) and `senior-agentic-ai-engineer` (peer). |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [Agent attack-surface and persona mapping](exercises/exercise-01-agent-attack-surface-and-persona-mapping.md) | 3 |
| 02 | [OWASP Agentic Threats overlay](exercises/exercise-02-owasp-agentic-threats-overlay.md) | 3 |
| 03 | [MITRE ATLAS agent-technique cross-tag](exercises/exercise-03-mitre-atlas-agent-technique-cross-tag.md) | 3 |
| 04 | [Multi-agent emergent-harm catalogue](exercises/exercise-04-multi-agent-emergent-harm-catalogue.md) | 2 |
| 05 | [Agent harm catalogue from the AI Incident Database](exercises/exercise-05-agent-harm-catalog-from-incident-database.md) | 2 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys here.

## Structure

```
mod-102-agent-threat-modelling/
├── 01-…10-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise and map — they do **not** substitute for reading the primary documents (OWASP GenAI project, MITRE ATLAS matrix, Google SAIF hub, AIID, OECD.AI). Every chapter links to the canonical URL. **Read the primary source once, then re-read it against the chapter.** If a chapter and the primary document disagree, the primary document wins — file an issue. Numerical IDs (OWASP threat numbers, ATLAS technique IDs) move between versions; check the version marker on each source before you cite it.
