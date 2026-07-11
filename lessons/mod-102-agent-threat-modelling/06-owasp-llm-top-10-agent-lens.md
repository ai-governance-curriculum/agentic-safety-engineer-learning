# 06 — OWASP LLM Top 10 (2025) Through the Agent Lens

## Motivation

The **OWASP Top 10 for Large Language Model Applications** is the checklist you already know from the level-25 prerequisite, read as a *chatbot* checklist. Almost every item on it deepens or mutates once you deploy the model as a tool-using agent. This chapter re-reads LLM01–LLM10 through the agent lens — not to duplicate the OWASP source, but to point out (a) which items the prerequisite already owns, (b) where the agent surface changes the mitigation, and (c) how each item lands on the six-surface × persona matrix.

## Primary source

- **[OWASP Top 10 for LLM Applications — 2025 edition](https://owasp.org/www-project-top-10-for-large-language-model-applications/)** — the current published Top 10 with detailed risk descriptions and mitigations.

<!-- needs-research: pin the version of the OWASP LLM Top 10 this chapter is written against and update if the numbered list changes. As of the 2025 edition the ten items below are named; earlier editions differed. -->

## The ten, re-read for agents

### LLM01 — Prompt Injection

**Level-25 prerequisite depth:** direct user-prompt injection defence — filtering, refusal training, output classifiers.

**Level-40 agent-lens delta:**

- **Indirect prompt injection is the primary channel** — the attacker never types into the user prompt. Payloads ride in retrieved documents (Greshake et al., 2023), tool responses, memory recalls, environment observations, and peer-agent messages.
- **Surface reach.** Data-input, memory / VS, environment, cross-agent — four of six surfaces.
- **Personas reached.** Tier 1–5 in different intensities; Tier 4 (cybercrime affiliate) has industrial capacity to publish payloads at scale.
- **Mitigation delta.** Provenance labels on every string, spotlighting / StruQ-style boundary rendering, per-channel sanitisation, model-side attention constraints where feasible; deep coverage in mod-103 and mod-108.

### LLM02 — Sensitive Information Disclosure

**Level-25 prerequisite depth:** training-data leakage, membership inference, model inversion.

**Level-40 agent-lens delta:**

- **Tool-response leakage becomes primary.** Agents can query internal data sources (databases, wikis, ticket systems) and then leak the results through downstream tools (email, chat, screenshots). Data crosses trust boundaries at tool-response time.
- **Surface reach.** Tool-invocation (primary), data-input, memory, environment, cross-agent.
- **Personas reached.** Tier 2–6; Tier 3 insider especially, because their credentials expand the sensitive corpus the tool touches.
- **Mitigation delta.** DLP applied at tool-response time (before the string enters the model context); per-role sensitivity classification; egress filters on tool outputs; per-channel disclosure policy. See mod-107 containment.

### LLM03 — Supply Chain

**Level-25 prerequisite depth:** model provenance, dataset provenance, dependency hygiene.

**Level-40 agent-lens delta:**

- **Tool supply chain is the new axis.** MCP servers, plugin marketplaces, third-party API tools, and dynamically discovered peer agents are all supply-chain risk surfaces. A malicious MCP server can inject payloads into every conversation.
- **Model supply chain expands** to include fine-tunes and adapters that ship with agents, safety-tuning pipelines, and constitutional-classifier weights.
- **Surface reach.** Tool-invocation (primary), cross-agent (primary), memory (via poisoned embedding corpora), environment (via retrieved documents).
- **Personas reached.** Tier 4–6; Tier 6 (nation-state) has explicit standing to compromise supply chain at scale.
- **Mitigation delta.** Pinned MCP servers; signed tool manifests; egress allow-lists per tool; SBOM discipline for the agent runtime; frontier-lab handoff for weight-provenance concerns.

### LLM04 — Data and Model Poisoning

**Level-25 prerequisite depth:** training-data poisoning, backdoor / trojan risks, data-integrity monitoring.

**Level-40 agent-lens delta:**

- **Inference-time poisoning is the new primary vector.** RAG-index poisoning, per-user memory poisoning, embedding-store poisoning, feedback-signal poisoning-into-fine-tuning. The corruptible surface is the *deployment state*, not (only) the training set.
- **Cross-tenant amplification.** A poisoned document in a shared corpus affects every user of that agent.
- **Surface reach.** Memory / VS (primary), data-input, cross-agent.
- **Personas reached.** Tier 2 (probing), Tier 3 (insider RAG write access), Tier 4 (scaled poisoning), Tier 5 (targeted), Tier 6 (uplift-oriented).
- **Mitigation delta.** Provenance labels, per-tenant isolation, canary-probe retrieval, anomaly detection, retention limits, human-approved corpus onboarding. See mod-108.

### LLM05 — Improper Output Handling

**Level-25 prerequisite depth:** output sanitisation, XSS, template-injection, downstream escape.

**Level-40 agent-lens delta:**

- **Output *is* a tool call.** The model's structured output becomes the argument set of a tool the runtime executes. Improper handling means executing an unvalidated tool call — the "improper output handling" analogue for agents is *executing structured output without validation*.
- **Downstream propagation** through the agent graph — an output from agent A becomes an input to agent B. Each hop is another improper-output-handling risk.
- **Surface reach.** Tool-invocation (primary), cross-agent (primary).
- **Personas reached.** Tier 1–6.
- **Mitigation delta.** Argument validators at every tool boundary; output classifier at every peer-agent send boundary; canonical serialisation with schema pinning; See mod-107 argument-validator and side-effect scope work.

### LLM06 — Excessive Agency

**Level-25 prerequisite depth:** the concept itself as a category label.

**Level-40 agent-lens delta:**

- **The centrepiece OWASP threat for this role.** OWASP LLM06 describes exactly the failure mode agents institutionalise — a model can take actions with too-broad authority, too-few checks, too-large blast radius.
- **Concrete sub-cases** (from the OWASP text): excessive functionality (too many tools), excessive permissions (per-tool credentials over-broad), excessive autonomy (missing HITL where it matters).
- **Surface reach.** Tool-invocation (primary), HITL (primary), cross-agent, memory (via write authority).
- **Personas reached.** Tier 1–6.
- **Mitigation delta.** Principle-of-least-authority per tool; per-user credentials; capability gates; blast-radius caps; mandatory HITL on high-stakes; kill-switches. Mod-107 owns the engineering depth.

### LLM07 — System Prompt Leakage

**Level-25 prerequisite depth:** system-prompt secrecy hygiene, prompt-extraction defence.

**Level-40 agent-lens delta:**

- **The system prompt often carries tool definitions, credentials, tenant identifiers.** Leaking it leaks the *tool inventory* and often the operator-side security assumptions.
- **The system prompt is often assembled dynamically** at runtime — every fragment source is a potential leak channel.
- **Surface reach.** Data-input (primary — the extraction target), cross-agent (peer agents can extract).
- **Personas reached.** Tier 2 (research), Tier 5 (targeted extraction).
- **Mitigation delta.** Do not put secrets in the system prompt; treat the system prompt as attacker-visible; separate the *tool inventory* from the operator's confidential business logic.

### LLM08 — Vector and Embedding Weaknesses

**Level-25 prerequisite depth:** general vector-DB security awareness.

**Level-40 agent-lens delta:**

- **Cross-tenant embedding leakage.** Near-duplicate detection in shared embedding stores can leak sensitive tokens across tenants.
- **Adversarial embedding attacks.** Content crafted to match specific vectors and hijack retrieval.
- **Poisoning of embedding stores** — a variant of LLM04 with vector-specific mitigations.
- **Surface reach.** Memory / VS (primary), data-input.
- **Personas reached.** Tier 2, 4, 5.
- **Mitigation delta.** Per-tenant embedding namespaces; retrieval filters (source, freshness, provenance); anomaly detection on retrieval-frequency spikes; encrypted-at-rest embedding storage.

### LLM09 — Misinformation

**Level-25 prerequisite depth:** hallucination, factuality, output-classifier discipline.

**Level-40 agent-lens delta:**

- **Agents *act* on misinformation.** A chat app that hallucinates says a wrong thing; an agent that hallucinates emails a wrong recipient, transfers money to a wrong account, files a wrong bug. Misinformation compounds through tools and peer agents.
- **Cascading hallucination** (OWASP Agentic T5 — chapter 05) is the agent-specific mutation of LLM09.
- **Surface reach.** Data-input, tool-invocation, cross-agent.
- **Personas reached.** Tier 1–5.
- **Mitigation delta.** Provenance-required tool arguments (source-cite before act); verification loops; independence-across-hops constraints; hallucination classifiers on tool inputs.

### LLM10 — Unbounded Consumption

**Level-25 prerequisite depth:** rate limits, quotas, model-DoS protection.

**Level-40 agent-lens delta:**

- **Compound consumption axes.** Tokens, tool calls, sub-agent spawns, HITL approvals, memory writes, embedding-store writes, external API budgets. Any of them can be run away by attacker input.
- **Sub-agent spawn explosions** are the specific new failure mode — one prompt injection can trigger recursive sub-agent creation until a depth or breadth budget stops it.
- **Surface reach.** Tool-invocation (primary), memory, HITL, cross-agent.
- **Personas reached.** Tier 1 (accidental), Tier 4 (deliberate).
- **Mitigation delta.** Explicit per-session budgets across all consumption axes; sub-agent depth/breadth caps; approval-queue rate limits; model-DoS discipline from the level-25 prerequisite still applies.

## Cross-mapping to OWASP Agentic (chapter 05)

The two OWASP catalogues overlap; they were drafted by adjacent groups within the OWASP GenAI project and are meant to be read together. Rough correspondence (not perfect):

| OWASP LLM Top 10 | OWASP Agentic |
|---|---|
| LLM01 Prompt Injection | Underlies every Agentic threat with a prompt-side carrier |
| LLM02 Sensitive Information Disclosure | Related to Tool Misuse; overlaps with Privilege Compromise |
| LLM03 Supply Chain | Agentic Agent Communication Poisoning + Rogue Agents in Multi-Agent Systems |
| LLM04 Data and Model Poisoning | Agentic Memory Poisoning (T1) |
| LLM05 Improper Output Handling | Agentic Tool Misuse (T2), Unexpected RCE |
| LLM06 Excessive Agency | Agentic Tool Misuse (T2), Privilege Compromise (T3), HITL Bypass |
| LLM07 System Prompt Leakage | Agentic Identity Spoofing (partly) |
| LLM08 Vector and Embedding Weaknesses | Agentic Memory Poisoning (T1) |
| LLM09 Misinformation | Agentic Cascading Hallucination (T5), Misaligned Goals |
| LLM10 Unbounded Consumption | Agentic Resource Overload (T4) |

<!-- needs-research: verify the current cross-mapping against the OWASP GenAI project's own published cross-mapping if one exists in the current versions. -->

The mapping is *many-to-many*. When you cite an OWASP source in the ATMD, cite both catalogues where both apply — reviewers will read whichever one they know first.

## What the prerequisite already owns and this module does not re-teach

The level-25 prerequisite covers all ten OWASP LLM items *at general depth*:

- Chat-app prompt-injection defence baseline.
- Training-data leakage, membership inference at general depth.
- SBOM / dependency hygiene, model provenance.
- Training-data poisoning defences.
- Output-encoding hygiene.
- Rate-limiting.

This module does **not** re-teach any of the above. This chapter's contribution is *the agent-lens delta* per item — the surface expansion, the persona reach, and the mitigation delta.

If your ATMD is duplicating the level-25 prerequisite text on any of the ten items, delete the duplicate and cite the prerequisite instead.

## Common misreadings to avoid

- **"LLM06 Excessive Agency is only for agents."** LLM06 was written to be agent-aware from the start of the 2025 edition. It is the OWASP entry-point for agent-scope issues and belongs to this role.
- **"LLM07 System Prompt Leakage is a marketing risk, not a security risk."** Leaked system prompts often leak tool inventories, tenant identifiers, secrets embedded historically, and operator-side security assumptions. Treat it as a security risk.
- **"We cover OWASP LLM Top 10 by having a classifier."** A classifier is one piece of the mitigation for a subset of items. LLM03 (supply chain), LLM06 (excessive agency), LLM07 (system prompt leakage) require different classes of mitigation entirely.
- **"OWASP LLM Top 10 is a superset of OWASP Agentic."** No — the two overlap and were authored to complement each other. Read both.

## Summary

- OWASP LLM Top 10 (2025 edition) is the LLM-generic checklist. Every one of the ten items *mutates* when the deployment is a tool-using agent.
- Agent-lens deltas per item: surface reach expands beyond user-prompt into memory / tools / environment / cross-agent; personas reached expand to insiders, cybercrime affiliates, and beyond; mitigations move from output classifier to argument validators, provenance labels, egress filters, and containment.
- The overlap with OWASP Agentic threats (chapter 05) is many-to-many; cite both.
- The prerequisite owns the LLM-generic depth; this module owns the agent-lens delta.
