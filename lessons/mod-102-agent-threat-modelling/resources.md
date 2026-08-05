# Resources for mod-102 — Threat Modelling for Autonomous and Tool-Using Agents

This is the curated primary-source reading list for the module. Every chapter links to a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

Numerical IDs — OWASP threat numbers, MITRE ATLAS technique IDs, SAIF pillar counts — move between versions. Note the version-observed date on any citation you drop into an ATMD row.

---

## OWASP GenAI Security Project

The load-bearing practitioner-consensus catalogues for chapters 05 and 06.

- [OWASP GenAI Security Project — hub](https://genai.owasp.org/) — the umbrella project that publishes the LLM Top 10, the Agentic threats paper, the Red Teaming Guide, and adjacent material.
- [OWASP Agentic AI — Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/) — the agent-specific threat catalogue this module treats as authoritative for chapter 05.
- [OWASP Top 10 for LLM Applications (2025)](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — the LLM-generic Top 10 re-read through the agent lens in chapter 06.
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) — practitioner methodology; feeds mod-111.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — the classical-ML companion the level-25 prerequisite owns; keep for cross-reference.

<!-- needs-research: pin the exact currently published versions of the Agentic Threats paper and the LLM Top 10, with observed-on dates. Both have iterated and both will iterate again. -->

---

## MITRE ATLAS

The infosec-legible spine for chapter 07.

- [MITRE ATLAS — hub](https://atlas.mitre.org/)
- [MITRE ATLAS Matrix](https://atlas.mitre.org/matrices/ATLAS) — the tactic × technique grid.
- [MITRE ATLAS Techniques](https://atlas.mitre.org/techniques) — the technique catalogue; every `AML.T####` ID resolves here.
- [MITRE ATLAS Case Studies](https://atlas.mitre.org/studies/) — worked adversarial demonstrations tagged to techniques.
- [MITRE ATLAS on GitHub](https://github.com/mitre-atlas/atlas-data) — the machine-readable data source that backs the site.

<!-- needs-research: ATLAS renumbers techniques across releases. Re-verify every `AML.T####` ID cited in the module before you cite it in a production ATMD row, and record the version-observed date. -->

---

## Google Secure AI Framework (SAIF)

The operator-side control-family taxonomy for chapter 08.

- [Google Secure AI Framework (SAIF) — hub](https://safety.google/cybersecurity-advancements/saif/)
- [SAIF: A framework for secure AI systems (Google blog, June 2023)](https://blog.google/technology/safety-security/introducing-googles-secure-ai-framework/) — the launch article with the original pillar list.
- [Google Cloud — SAIF resources](https://cloud.google.com/security/business/saif) — enterprise-oriented rendering of SAIF elements as controls.
- [SAIF Risk Assessment tool](https://saif.google/) — Google's self-assessment questionnaire that lets you score a deployment against the pillars.

<!-- needs-research: confirm the current published SAIF pillar list, wording, and ordering; Google has iterated the framing since the 2023 launch. -->

---

## NIST and US standards (assumed from the level-25 prerequisite)

These belong to the `ai-risk-engineer` prerequisite; kept here for cross-reference because the ATMD's harm-cause routing (chapter 09) hands rows back into the prerequisite's risk register.

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST AI RMF Generative AI Profile (NIST AI 600-1)](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf)
- [NIST AI 100-2 E2023 — Adversarial ML: A Taxonomy and Terminology of Attacks and Mitigations](https://csrc.nist.gov/pubs/ai/100/2/e2023/final)
- [NIST GenAI Program](https://ai-challenges.nist.gov/genai)
- [US AI Safety Institute (NIST)](https://www.nist.gov/aisi)

---

## Incident corpora (chapter 04)

- [AI Incident Database (AIID)](https://incidentdatabase.ai/) — the curated incident corpus; every incident has a stable numeric ID.
- [AIID — public dataset snapshots](https://incidentdatabase.ai/research/snapshots/) — machine-readable JSON exports.
- [AIID Taxonomies (CSET, GMF, Goose)](https://incidentdatabase.ai/taxonomies/) — the tagging systems chapter 04 recommends slicing on.
- [OECD.AI Incidents Monitor](https://oecd.ai/en/incidents) — policy-oriented rollup companion to AIID, with jurisdictional slicing.
- [MIT AI Risk Repository](https://airisk.mit.edu/) — the taxonomy of *risks* (not incidents) sourced from the peer-reviewed literature.

<!-- needs-research: confirm the AIID's currently published taxonomies and any changes to the OECD.AI Incidents Monitor's category schema and data-refresh cadence. Confirm the current published version of the MIT AI Risk Repository. -->

---

## Frontier-lab safety frameworks (referenced from mod-101 for chapter 10 tiering)

Chapter 10 borrows the *shape* of these frameworks (capability → threshold → elicitation → tripwire → mitigation → rollback → review → evidence → disclosure) as the template for enterprise-agent deployment tiering. Read them at the depth mod-101 already covers; this module does not re-teach them.

- [Anthropic Responsible Scaling Policy (RSP) — hub](https://www.anthropic.com/rsp)
- [OpenAI Preparedness Framework — hub](https://openai.com/safety/preparedness/)
- [Google DeepMind Frontier Safety Framework (FSF) — hub](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/)

---

## Academic red-team literature (grounding evidence for chapters 02, 04, 05, 06)

The AIID / OECD.AI corpora undercount agent-specific incidents (chapter 04). Compensate with peer-reviewed adversarial demonstrations.

### Indirect prompt injection and agent-side injection

- [Greshake et al. — "Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection" (2023)](https://arxiv.org/abs/2302.12173) — the foundational paper on the indirect prompt-injection surface (chapter 02).
- [Zhan et al. — "InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated LLM Agents" (2024)](https://arxiv.org/abs/2403.02691) — indirect-injection benchmark focused on tool-using agents.
- [Debenedetti et al. — "AgentDojo: A Dynamic Environment to Evaluate Attacks and Defenses for LLM Agents" (2024)](https://arxiv.org/abs/2406.13352) — cross-tool exploitation benchmark including memory writes.
- [Yi et al. — "Benchmarking and Defending Against Indirect Prompt Injection Attacks on Large Language Models" (2023)](https://arxiv.org/abs/2312.14197)
- [Willison — "Prompt injection: What's the worst that can happen?"](https://simonwillison.net/2023/Apr/14/worst-that-can-happen/) — practitioner-level framing referenced across the OWASP entries.

### Direct jailbreaks (for persona-ladder Tier 2 modelling)

- [Zou et al. — GCG (2023)](https://arxiv.org/abs/2307.15043)
- [Chao et al. — PAIR (2023)](https://arxiv.org/abs/2310.08419)
- [Mehrotra et al. — TAP (2023)](https://arxiv.org/abs/2312.02119)
- [Anthropic — Many-shot Jailbreaking (2024)](https://www.anthropic.com/research/many-shot-jailbreaking)
- [Shen et al. — "Do Anything Now": Characterizing and Evaluating In-The-Wild Jailbreak Prompts on Large Language Models (2024)](https://arxiv.org/abs/2308.03825) — the DAN-family corpus cited in chapter 03.

### Multi-agent adversarial coordination (chapter 09 multi-agent-emergent)

- [Ju et al. — "Flooding Spread of Manipulated Knowledge in LLM-Based Multi-Agent Communities" (2024)](https://arxiv.org/abs/2407.07791) — poison-propagation across an agent graph.
- [Gu et al. — "Agent Smith: A Single Image Can Jailbreak One Million Multimodal LLM Agents Exponentially Fast" (2024)](https://arxiv.org/abs/2402.08567) — exponential-spread demonstration in a multi-agent multimodal setting.
- [Wu et al. — "AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation" (2023)](https://arxiv.org/abs/2308.08155) — background architecture referenced when threat-modelling AutoGen-shape agent graphs.
- [Chan et al. — "Agent Boards: An Analytical Evaluation Board of Multi-turn LLM Agents" (2024)](https://arxiv.org/abs/2401.13178)

<!-- needs-research: add newer multi-agent-attack demonstrations as the literature matures — this is the fastest-moving corner of the field and chapter 09 will need refresh cadence. -->

### Memory poisoning and RAG-index attacks

- [Zou et al. — "PoisonedRAG: Knowledge Corruption Attacks to Retrieval-Augmented Generation of Large Language Models" (2024)](https://arxiv.org/abs/2402.07867)
- [Chaudhari et al. — "Phantom: General Trigger Attacks on Retrieval Augmented Language Generation" (2024)](https://arxiv.org/abs/2405.20485)
- [Xue et al. — "BadRAG: Identifying Vulnerabilities in Retrieval Augmented Generation of Large Language Models" (2024)](https://arxiv.org/abs/2406.00083)

### Data-leakage and prompt extraction (LLM07)

- [Carlini et al. — "Extracting Training Data from Large Language Models" (2021)](https://arxiv.org/abs/2012.07805)
- [Nasr et al. — "Scalable Extraction of Training Data from (Production) Language Models" (2023)](https://arxiv.org/abs/2311.17035)
- [Zhang et al. — "Effective Prompt Extraction from Language Models" (2023)](https://arxiv.org/abs/2307.06865)

### Tool-use safety and agent evaluation

- [Ruan et al. — "ToolEmu: Identifying the Risks of LM Agents with an LM-Emulated Sandbox" (2023)](https://arxiv.org/abs/2309.15817)
- [Andriushchenko et al. — "AgentHarm: A Benchmark for Measuring Harmfulness of LLM Agents" (2024)](https://arxiv.org/abs/2410.09024)
- [Kinniment et al. (METR) — "Evaluating Language-Model Agents on Realistic Autonomous Tasks" (2023)](https://arxiv.org/abs/2312.11671)

### Defences that inform the mitigation-module handoffs

- [Hines et al. — "Defending Against Indirect Prompt Injection Attacks With Spotlighting" (2024)](https://arxiv.org/abs/2403.14720)
- [Chen et al. — "StruQ: Defending Against Prompt Injection with Structured Queries" (2024)](https://arxiv.org/abs/2402.06363)
- [Anthropic — Constitutional Classifiers (2025)](https://www.anthropic.com/research/constitutional-classifiers)
- [Willison — "Design Patterns for Securing LLM Agents against Prompt Injections"](https://simonwillison.net/2025/Apr/9/llm-security-design-patterns/) — practitioner rundown of the defence patterns.

---

## Agent protocols and dynamic tool discovery (chapter 02 cross-agent surface)

- [Model Context Protocol (MCP) — specification](https://modelcontextprotocol.io/)
- [MCP on GitHub](https://github.com/modelcontextprotocol)
- [Google — Agent2Agent (A2A) protocol](https://google.github.io/A2A/) — cross-agent messaging spec cited in chapter 02.
- [OpenAI — Function calling and tools documentation](https://platform.openai.com/docs/guides/function-calling)
- [Anthropic — Tool use with Claude](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)

<!-- needs-research: track successor / competing agent-communication protocols as they emerge; the cross-agent surface is the fastest-mutating architecture surface in the module. -->

---

## Frontier-lab and AISI evaluations (chapter 04 augmentation, chapter 10 Tier 4)

- [Anthropic — Claude System Cards](https://www.anthropic.com/news) (browse for the current model's system card)
- [OpenAI — System Cards](https://openai.com/safety/) — o-series, GPT-4 family, etc.
- [Google DeepMind — Responsibility & Safety publications](https://deepmind.google/about/responsibility-safety/)
- [UK AI Security Institute — published research and evaluations](https://www.aisi.gov.uk/research)
- [US AI Safety Institute (NIST)](https://www.nist.gov/aisi)
- [METR (formerly ARC Evals) — public tasks and RE-Bench](https://metr.github.io/public-tasks/)
- [Apollo Research — deception and scheming evaluations](https://www.apolloresearch.ai/research)

---

## Related standards this role plugs into

- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html)
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html)
- [ISO/IEC 42005:2025 — AI system impact assessment](https://www.iso.org/standard/44545.html)
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Article 55 (general-purpose model provider obligations) and Article 73 (serious-incident reporting) are the ones that touch this module's ATMD.
- [Microsoft AI Red Team Guide](https://learn.microsoft.com/en-us/security/ai-red-team/) — operator-side red-team methodology that complements the OWASP Red Teaming Guide.

---

## Tooling worth knowing (deep coverage in mod-108 / mod-111)

Named here so you can install and try them while reading the module; do not attempt to engineer with them yet.

- [garak — LLM vulnerability scanner](https://github.com/NVIDIA/garak) (NVIDIA)
- [PyRIT — Python Risk Identification Toolkit](https://github.com/Azure/PyRIT) (Microsoft)
- [Promptfoo — red-team + eval harness](https://www.promptfoo.dev/)
- [Inspect — UK AISI evaluation harness](https://inspect.aisi.org.uk/)
- [AgentDojo — agent-attack benchmark](https://github.com/ethz-spylab/agentdojo)
- [InjecAgent — indirect-injection benchmark](https://github.com/uiuc-kang-lab/InjecAgent)

---

## Adjacent tracks in the AI Career Curriculum ecosystem

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — the required prerequisite (level 25). Owns the general harm-catalogue, NIST AI RMF, OWASP ML Top 10, and STRIDE / LINDDUN craft that this module assumes.
- [`senior-agentic-ai-engineer-learning`](https://github.com/ai-engineering-curriculum/senior-agentic-ai-engineer-learning) — the agentic-AI-family peer whose architecture spec is the input to your ATMD. <!-- needs-research: confirm the exact repository URL for this peer track. -->
- [`ai-evaluation-engineer-learning`](https://github.com/ai-governance-curriculum/ai-evaluation-engineer-learning) — the level-35 governance peer that consumes tier assignment + ATMD into release-assurance packaging (chapter 10).
- [`security-learning`](https://github.com/ai-infra-curriculum/security-learning) and [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — the platform-security peers that consume the ATMD's ATLAS + SAIF blocks (chapter 07, chapter 08).
- [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) — level 50; reconciles tiering across the enterprise's control library.

---

## Downstream modules in this track that consume the ATMD

- **mod-103** (Prompt Injection Engineering) — engineers defences against LLM01 / OWASP-Agentic data-input threats.
- **mod-104** (Jailbreak Engineering) — engineers against LLM01 / refusal-erosion at Tier 2 depth.
- **mod-105** (Agent-Specific Attack Surface Engineering) — engineers the multi-agent-emergent surface (chapter 09) and cross-agent-protocol hardening.
- **mod-107** (Excessive-Agency Containment) — engineers containment against tool misuse, privilege compromise, resource overload, HITL bypass, unexpected RCE.
- **mod-108** (Frontier-Scale Guardrails and Safety Monitors) — engineers detection / prevention for prompt-injected impersonation, memory-poisoning canaries, hallucination-cascade filters.
- **mod-110** (AI Control and Adversarial Alignment Evaluation) — evaluates the misaligned-goals / intent-breaking threats at frontier depth.
- **mod-111** (Automated and Scaled Red-Teaming) — exercises every ATMD row through automated fuzzers and LLM-vs-LLM attacker loops.
- **mod-112** (Frontier-Safety Program, Serious-Incident Response, Disclosure) — rolls tiering and incident evidence up into the org-scope program.
