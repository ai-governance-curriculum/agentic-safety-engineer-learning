# Resources for mod-105 — Agent-Specific Attack Surface Engineering

This is the curated primary-source reading list for the module. Every chapter cites a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

Benchmark scores, propagation numbers, per-suite ASRs, public leaderboards, and protocol version numbers move with every model release, every framework release, and every benchmark version bump. Any figure you drop into an AASS artefact from these sources needs a re-verification pass — chapters 02–06 all flag this explicitly with `<!-- needs-research: ... -->` markers. Note the observed-on date on any citation you drop into a finding.

The mod-103 (Prompt-Injection Engineering) and mod-104 (Jailbreak Engineering) `resources.md` files carry the injection-specific and jailbreak-specific primaries this module builds on. Rather than duplicate those lists, this file focuses on the **agent-attack-specific** primaries cited across chapters 01–07: tool-abuse chains, memory / vector-store poisoning, long-horizon planning subversion, multi-agent adversarial coordination, and the AgentDojo + InjecAgent coverage matrix.

---

## The two anchor benchmarks (chapters 02, 05, 06 — required before exercise 05)

- [Debenedetti et al. — "AgentDojo: A Dynamic Environment to Evaluate Attacks and Defenses for LLM Agents" (NeurIPS 2024)](https://arxiv.org/abs/2406.13352) — the primary agent-environment benchmark this module ships coverage against. Required reading before chapters 02, 05, 06 and before exercise 05.
- [AgentDojo — reference implementation](https://github.com/ethz-spylab/agentdojo) — the code, suites, and reproducibility harness.
- [Zhan et al. — "InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated Large Language Model Agents" (ACL Findings 2024)](https://arxiv.org/abs/2403.02691) — the primary indirect-prompt-injection benchmark against tool-integrated agents. Required reading before chapters 02, 06 and before exercise 05.
- [InjecAgent — reference implementation](https://github.com/uiuc-kang-lab/InjecAgent) — the code, cases, and default judge.

<!-- needs-research: pin the current AgentDojo release tag, suite list, and reported baselines; pin the current InjecAgent release tag, case count per category, and reported baselines. Both benchmarks iterate. -->

---

## Foundational indirect-injection framing (chapters 01, 02)

- [Greshake et al. — "Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection" (AISec 2023)](https://arxiv.org/abs/2302.12173) — the canonical framing paper for indirect prompt injection in tool-integrated LLM apps. Read the case studies (§4–§7) before chapter 02 and exercise 01. Chapter 03 explicitly picks up from §7 on memory persistence.
- [Kai Greshake's blog and code](https://kai-greshake.de/) — the author's continuing publications and demos.
- [Willison — "prompt injection" tag](https://simonwillison.net/tags/prompt-injection/) — the running practitioner index for concrete tool-abuse, memory-poisoning, and cross-agent incidents.

---

## Tool-abuse chain literature (chapter 02)

The composed-tool-attack literature the AASS's chain library sits on top of.

- [Greshake et al. (2023)](https://arxiv.org/abs/2302.12173) — repeated: the tool-abuse-chain surface is the surface the paper's case studies made public.
- [Debenedetti et al. — "AgentDojo" (2024)](https://arxiv.org/abs/2406.13352) — the workspace / banking / travel / Slack suites are the concrete tool-abuse-chain arenas chapter 02's sub-families run against.
- [Zhan et al. — "InjecAgent" (2024)](https://arxiv.org/abs/2403.02691) — the direct-harm and data-stealing case categorisation is the closest public taxonomy to chapter 02's sub-families 1–3.
- [Andriushchenko et al. — "AgentHarm: A Benchmark for Measuring Harmfulness of LLM Agents" (2024)](https://arxiv.org/abs/2410.09024) — harmfulness-benchmark methodology for agent settings; useful comparator against AgentDojo's utility-security composite for chapter 06.
- [Ruan et al. — "ToolEmu: Identifying the Risks of LM Agents with an LM-Emulated Sandbox" (2023)](https://arxiv.org/abs/2309.15817) — evaluation-time sandbox for agent tool-use; the emulated-tool-response methodology chapter 02's reproducibility bundle borrows.
- [Rehberger — "Data Exfiltration via ChatGPT and other AI Chatbots" (Embrace The Red)](https://embracethered.com/blog/) — a recurring venue for chapter-02 sub-family 2 (argument smuggling) and sub-family 3 (chained exfil) demonstrations against production products.
- [PortSwigger Research — LLM attack write-ups](https://portswigger.net/research/topics/ai) — application-security-flavoured write-ups often documenting tool-abuse chains against deployed products.

<!-- needs-research: pick two or three publicly-documented tool-abuse-chain incidents (search + email + calendar exfiltration; code-interpreter credential theft; markdown-image side channel) against named production agent products with dates and disclosure references, and cite them from chapter 02. -->

---

## Memory and vector-store poisoning (chapter 03 — required before exercise 02)

The persistent-state attack literature.

- [Zou et al. — "PoisonedRAG: Knowledge Corruption Attacks to Retrieval-Augmented Generation of Large Language Models" (2024)](https://arxiv.org/abs/2402.07867) — the canonical PoisonedRAG paper. Required reading before chapter 03 and exercise 02.
- [Chen et al. — "AgentPoison: Red-teaming LLM Agents via Poisoning Memory or Knowledge Bases" (NeurIPS 2024)](https://arxiv.org/abs/2407.12784) — the canonical embedding-space backdoor paper for agents; the AgentPoison family chapter 03 develops. Required reading before chapter 03 and exercise 02. <!-- needs-research: confirm the arXiv ID and NeurIPS venue for the currently cited AgentPoison version. -->
- [Chaudhari et al. — "Phantom: General Trigger Attacks on Retrieval Augmented Language Generation" (2024)](https://arxiv.org/abs/2405.20485) — trigger-conditioned RAG attack variant chapter 03 references alongside PoisonedRAG.
- [Xue et al. — "BadRAG: Identifying Vulnerabilities in Retrieval Augmented Generation of Large Language Models" (2024)](https://arxiv.org/abs/2406.00083) — companion BadRAG poisoning study.
- [Ju et al. — "Flooding Spread of Manipulated Knowledge in LLM-Based Multi-Agent Communities" (2024)](https://arxiv.org/abs/2407.07791) — poison propagation across an agent graph via shared corpora; connects chapter 03 to chapter 05's propagation metrics.
- [Rehberger — "ChatGPT: Hacking Memories with Prompt Injection" (Embrace The Red, 2024)](https://embracethered.com/blog/posts/2024/chatgpt-hacking-memories/) — public write-up of the ChatGPT long-term-memory injection surface; grounds chapter 03 sub-family 1.
- [Greshake et al. (2023) §7](https://arxiv.org/abs/2302.12173) — the memory-persistence section that predates the modern PoisonedRAG / AgentPoison work; still useful framing.

<!-- needs-research: track successor RAG-poisoning demonstrations (TrojanRAG, prompt-triggered corpus attacks) as the literature matures; this row moves fast. -->

---

## Long-horizon planning subversion (chapter 04 — required before exercise 03)

The planner-as-attack-surface literature.

- [Yao et al. — "ReAct: Synergizing Reasoning and Acting in Language Models" (ICLR 2023)](https://arxiv.org/abs/2210.03629) — the Thought/Action/Observation loop most modern agent architectures descend from. Required reading before chapter 04 and exercise 03.
- [Yao et al. — "Tree of Thoughts: Deliberate Problem Solving with Large Language Models" (NeurIPS 2023)](https://arxiv.org/abs/2305.10601) — branch-and-search planning; the sub-family 4 entry point.
- [Wang et al. — "Plan-and-Solve Prompting: Improving Zero-Shot Chain-of-Thought Reasoning by Large Language Models" (ACL 2023)](https://arxiv.org/abs/2305.04091) — the plan-first / execute-second architecture the AASS attacks distinctly from ReAct. <!-- needs-research: confirm the currently canonical citation for plan-and-execute in the widely-deployed agent frameworks (LangGraph, AutoGen). -->
- [Shinn et al. — "Reflexion: Language Agents with Verbal Reinforcement Learning" (NeurIPS 2023)](https://arxiv.org/abs/2303.11366) — the reflection-step methodology chapter 04 identifies as the highest-leverage subversion target.
- [Ruan et al. — "ToolEmu" (2023)](https://arxiv.org/abs/2309.15817) — sandbox-simulation for planning-under-attack behaviour, referenced from chapter 04 for the goal-drift trace instrumentation pattern.
- [Wang et al. — "BadAgent: Inserting and Activating Backdoor Attacks in LLM Agents" (ACL 2024)](https://arxiv.org/abs/2406.03007) — backdoor attacks against tool-selection behaviour; observable at the planning-subversion boundary. <!-- needs-research: verify the current BadAgent venue and authors of record. -->
- [Yang et al. — "Watch Out for Your Agents! Investigating Backdoor Threats to LLM-Based Agents" (2024)](https://arxiv.org/abs/2402.11208) — companion agent-backdoor work touching plan-hijack surfaces.

<!-- needs-research: confirm the latest ATLAS technique identifiers that pattern-match to planning subversion (reasoning-target vs. tool-target techniques). -->

---

## Multi-agent adversarial coordination (chapter 05 — required before exercise 04)

The graph-shaped attack literature and the protocol specifications the graph rides on.

### Protocols

- [Model Context Protocol (MCP) — specification hub](https://modelcontextprotocol.io/) — Anthropic's tool-server protocol. Read the transport, tool discovery, and result-carriage sections before chapter 05.
- [MCP on GitHub](https://github.com/modelcontextprotocol) — the reference server / client implementations whose principal-handling defaults are chapter 05 sub-family 3's target.
- [Anthropic — Model Context Protocol overview](https://docs.anthropic.com/en/docs/agents-and-tools/mcp) — the vendor-side documentation.
- [Google — Agent2Agent (A2A) protocol specification](https://google.github.io/A2A/) — the cross-agent messaging spec; the primary A2A surface chapter 05 examines. Read the identity, discovery, and message-artefact sections before exercise 04.
- [A2A on GitHub](https://github.com/google/A2A) — the reference implementations and sample agents.

<!-- needs-research: pin the current A2A and MCP release tags, their identity / auth models, and any published security advisories. Both are moving standards. -->

### Propagation and worm-shaped attacks

- [Cohen, Bitton, Nassi — "Here Comes The AI Worm: Preventing the Propagation of Adversarial Self-Replicating Prompts Within GenAI Ecosystems" (Morris-II)](https://arxiv.org/abs/2403.02817) — the demonstration that indirect injection can propagate worm-shaped across an agent ecosystem via memory writes and message relays. Required reading before chapter 05 and exercise 04. <!-- needs-research: verify the currently cited version and the reported reproduction numbers. -->
- [Ju et al. — "Flooding Spread of Manipulated Knowledge in LLM-Based Multi-Agent Communities" (2024)](https://arxiv.org/abs/2407.07791) — repeated from chapter 03; equally load-bearing for chapter 05's propagation metrics.
- [Gu et al. — "Agent Smith: A Single Image Can Jailbreak One Million Multimodal LLM Agents Exponentially Fast" (2024)](https://arxiv.org/abs/2402.08567) — exponential-spread demonstration in a multi-agent multimodal setting; another framing of the R-estimate.
- [Zhang et al. — "PsySafe: A Comprehensive Framework for Psychological-Based Attack, Defense, and Evaluation of Multi-Agent System Safety" (2024)](https://arxiv.org/abs/2401.11880) — companion multi-agent safety framework worth reading for the graph-level metric conventions.

### Backdoor and identity attacks

- [Wang et al. — "BadAgent" (2024)](https://arxiv.org/abs/2406.03007) — repeated from chapter 04; observable across the multi-agent boundary.
- [Yang et al. — "Watch Out for Your Agents!" (2024)](https://arxiv.org/abs/2402.11208) — repeated from chapter 04; useful for the identity-spoofing framing at the A2A boundary.

<!-- needs-research: track successor multi-agent propagation and A2A / MCP identity-spoofing demonstrations as the two protocols mature and see adversarial evaluation. -->

---

## Judge methodology and evaluation harness plumbing (chapter 06)

The chain-aware judge scaffolding and the calibration methodology the AASS inherits.

- [Souly et al. — "A StrongREJECT for Empty Jailbreaks" (2024)](https://arxiv.org/abs/2402.10260) — the StrongREJECT judge paper; the calibration methodology chapter 06 references and mod-104 chapter 07 owns.
- [Zheng et al. — "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena" (2023)](https://arxiv.org/abs/2306.05685) — the general LLM-as-judge scaffolding the AASS's chain-decomposed judge builds on.
- [Liu et al. — "AgentBench: Evaluating LLMs as Agents" (ICLR 2024)](https://arxiv.org/abs/2308.03688) — auxiliary utility-measurement benchmark chapter 06 cites alongside AgentDojo.
- [Qin et al. — "ToolLLM: Facilitating Large Language Models to Master 16000+ Real-world APIs" (ICLR 2024)](https://arxiv.org/abs/2307.16789) — the ToolBench / ToolLLM utility-measurement setup for tool-integrated agents.
- [Kinniment et al. (METR) — "Evaluating Language-Model Agents on Realistic Autonomous Tasks" (2023)](https://arxiv.org/abs/2312.11671) — realistic-task agent evaluation baseline chapter 06's utility axis references.

---

## Defence layers the AASS measures (chapters 02, 05, 06)

The AASS is a defence-*measurement* module (mod-107 and mod-108 engineer the defences). These are the primaries for the postures the AASS toggles.

### Tool-response sanitisation and structured-input defences

- [Hines et al. — "Defending Against Indirect Prompt Injection Attacks With Spotlighting" (Microsoft, 2024)](https://arxiv.org/abs/2403.14720) — the spotlighting posture chapter 06 uses as posture 1's canonical loadout.
- [Chen et al. — "StruQ: Defending Against Prompt Injection with Structured Queries" (2024)](https://arxiv.org/abs/2402.06363) — structured-input defence family for chapter 06 posture 1.
- [Wallace et al. — "The Instruction Hierarchy: Training LLMs to Prioritize Privileged Instructions" (OpenAI, 2024)](https://arxiv.org/abs/2404.13208) — model-side principal-hierarchy training that grounds several posture-1 and posture-2 assumptions.
- [Debenedetti et al. — "Defending against Prompt Injection with a Few DefensiveTokens" (2024)](https://arxiv.org/abs/2405.09113) — defence-token approach useful as a comparator against spotlighting.
- [Willison — "Design Patterns for Securing LLM Agents against Prompt Injections" (2025)](https://simonwillison.net/2025/Apr/9/llm-security-design-patterns/) — practitioner-level catalogue of boundary-control patterns (dual-LLM, action-scoping, provenance-labelling) that inform chapter 06's posture axis and chapter 07's routing.

### Capability gates and least-privilege delegation (posture 2)

- [Anthropic — Tool use with Claude](https://docs.anthropic.com/en/docs/build-with-claude/tool-use) — reference tool-invocation surface the capability-gate posture wraps.
- [OpenAI — Function calling and tools documentation](https://platform.openai.com/docs/guides/function-calling) — reference tool-invocation surface for the same posture.
- [Willison — "Prompt injection: Bing" and related "action scoping" write-ups](https://simonwillison.net/tags/prompt-injection/) — practitioner discussions of action-scoping as posture 2 in production.
- The `ai-infra-security` learning track — canonical reference for capability-token issuer design (see the adjacent-tracks section below).

### Classifier guards and constitutional-classifier defences (chapter 06 posture composition; deep coverage in mod-108)

- [Anthropic — "Constitutional Classifiers: Defending against universal jailbreaks" (2025)](https://www.anthropic.com/research/constitutional-classifiers) — the classifier-guard state of the art referenced in chapter 06 for composed postures.
- [Sharma et al. — "Constitutional Classifiers: Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming" (2025)](https://arxiv.org/abs/2501.18837) — the companion arXiv paper.
- [Inan et al. — "Llama Guard: LLM-based Input-Output Safeguard for Human-AI Conversations" (2023)](https://arxiv.org/abs/2312.06674) — open-source classifier guard chapter 06 references as a comparator.
- [ShieldGemma (Zeng et al., 2024) — Google's safety classifier family](https://arxiv.org/abs/2407.21772) — comparator open guard.

### Sandbox and runtime hardening (chapter 06 posture 2; deep coverage in `ai-infra-security` and mod-107)

- [Firecracker — micro-VM sandbox](https://firecracker-microvm.github.io/) — micro-VM runtime chapter 07 identifies as one of the standard sandbox layers the peer role hardens.
- [gVisor — application-kernel sandbox](https://gvisor.dev/) — user-space kernel comparator.
- [bubblewrap (bwrap) — unprivileged sandbox](https://github.com/containers/bubblewrap) — lightweight sandbox chapter 07 references for code-interpreter isolation.

---

## OWASP, MITRE ATLAS, NIST — the standards overlay

- [OWASP Top 10 for LLM Applications (2025) — project page](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — chapters 02 and 03 cite **LLM01** (prompt injection, indirect variant), **LLM04** (data and model poisoning), **LLM06** (excessive agency), and **LLM08** (vector and embedding weaknesses). <!-- needs-research: confirm the current LLM Top 10 IDs and titles against the latest published edition. -->
- [OWASP GenAI Security Project — hub](https://genai.owasp.org/) — umbrella for the LLM Top 10 and the OWASP Agentic AI documents.
- [OWASP Agentic AI — Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/) — the agent-specific threat catalogue chapters 02–05 map into. Individual threats referenced across chapters: *Memory Poisoning* (T1), *Tool Misuse* (T2), *Privilege Compromise* (T3), *Cascading Hallucination Attacks* (T5), *Identity Spoofing & Impersonation*, *Agent Communication Poisoning*, *Rogue Agents in Multi-Agent Systems*, *Intent-Breaking / Goal Manipulation*, *Repudiation & Untraceability*. <!-- needs-research: confirm the current OWASP Agentic threat IDs, titles, and revision date; the numbering has iterated. -->
- [MITRE ATLAS — hub](https://atlas.mitre.org/) and [technique catalogue](https://atlas.mitre.org/techniques) — chapters 02–05 cite `LLM Plugin Compromise` (AML.T0053) and adjacent plugin-execution / poisoned-dataset / erode-dataset-integrity techniques. <!-- needs-research: confirm the current ATLAS technique IDs, parent tactics, and any newly-added agentic techniques. -->
- [NIST AI 100-2 E2023 — Adversarial ML: A Taxonomy and Terminology of Attacks and Mitigations](https://csrc.nist.gov/pubs/ai/100/2/e2023/final) — the level-25 prerequisite's taxonomy; AASS findings cross-reference the poisoning-attack terminology back to it.

---

## Vendor safety and policy documentation

Referenced for policy-refusal definitions, defence-stack claims, and the operator's compliance surface. The AASS measures deployed products against these.

- [Anthropic — Claude System Cards and safety announcements](https://www.anthropic.com/news) — browse for the current model's system card and its agent-safety sections.
- [Anthropic — Responsible Scaling Policy (RSP)](https://www.anthropic.com/rsp) — the tier framing mod-101 develops and this module's finding severity references.
- [Anthropic — Usage Policies](https://www.anthropic.com/legal/aup) — the policy chapter 01 cites for what "policy the model would refuse" means for Anthropic-hosted agents.
- [OpenAI — Safety and Preparedness Framework hub](https://openai.com/safety/) — GPT-4 family, o-series, GPT-5 family system cards; the Preparedness Framework mod-101 develops.
- [OpenAI — Usage Policies](https://openai.com/policies/usage-policies/)
- [Google DeepMind — Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/)
- [Google — Prohibited Use Policy for Generative AI](https://policies.google.com/terms/generative-ai/use-policy)
- [Microsoft — Responsible AI Standard](https://www.microsoft.com/en-us/ai/responsible-ai) — for Bing / Copilot family incident-response documentation.
- [UK AISI — model evaluation reports](https://www.aisi.gov.uk/) — third-party evaluations that ground chapter 06's expectations about what a numeric AASS coverage report looks like next to a national-institute evaluation.

---

## Practitioner venues that publish agent-attack incidents

Ongoing sources for chapters 02–05's illustrative cases and the exercise write-ups' incident references.

- [Simon Willison's weblog — "prompt injection" tag](https://simonwillison.net/tags/prompt-injection/) — the running index of tool-abuse, exfil, and memory-poisoning incidents in production products.
- [Embrace The Red (Johann Rehberger)](https://embracethered.com/blog/) — a recurring venue for chapter-02 chained-exfil write-ups (markdown-image side channel, plugin-mediated exfiltration) and chapter-03 memory-poisoning write-ups against production chat products.
- [PortSwigger Research — LLM attack write-ups](https://portswigger.net/research/topics/ai) — application-security-flavoured incident write-ups.
- [Kai Greshake's blog](https://kai-greshake.de/) — foundational-paper author's continuing publications on indirect-injection ecosystems.
- [OWASP GenAI Security Project — resources](https://genai.owasp.org/) — collects vendor and community write-ups under the LLM01 / Agentic-AI umbrella.
- [HackAPrompt competition write-ups (Schulhoff et al., 2023)](https://arxiv.org/abs/2311.16119) — competition-scale corpus of injection and jailbreak payloads with a taxonomy; many of the corpus's cases inform chapter 02 sub-family 1 illustrations.

---

## Agent frameworks the AASS attacks in the wild

The frameworks whose default wiring is the target of most chapter-02 and chapter-05 findings. Read at least one framework's docs before authoring exercises 01, 03, and 04 — the AASS's reproducibility bundle pins the framework version.

- [LangChain / LangGraph — documentation hub](https://python.langchain.com/) and [LangGraph docs](https://langchain-ai.github.io/langgraph/) — the most widely deployed agent framework; chapter 04's ReAct-shape and plan-and-execute discussions cite this stack.
- [AutoGen — Microsoft agent framework](https://microsoft.github.io/autogen/) — orchestrator-shaped multi-agent framework chapter 05 references for the sub-agent-spawn surface.
- [CrewAI — role-based multi-agent framework](https://docs.crewai.com/) — another orchestrator-shaped surface chapter 05 references.
- [LlamaIndex — data-framework docs](https://docs.llamaindex.ai/) — reference RAG stack for chapter 03 exercises.
- [Semantic Kernel — Microsoft agent framework](https://learn.microsoft.com/en-us/semantic-kernel/) — comparator framework for chapter 02's tool-bus surface.

<!-- needs-research: track successor / competitor agent frameworks (Anthropic's Agents SDK, OpenAI Assistants API, xAI agents, etc.) as they iterate; the framework list moves quickly. -->

---

## Tooling worth knowing (chapters 06; deep coverage in mod-108 / mod-111)

Named here so you can install and try them while reading the module. These are the load-bearing tools for exercise 05.

- [AgentDojo — reference implementation](https://github.com/ethz-spylab/agentdojo) — repeated from the top; the exercise-05 harness.
- [InjecAgent — reference implementation](https://github.com/uiuc-kang-lab/InjecAgent) — repeated from the top; the exercise-05 harness.
- [garak — LLM vulnerability scanner (NVIDIA)](https://github.com/NVIDIA/garak) — the mod-104 chapter 06 comparator; carries agent-relevant probe sets.
- [PyRIT — Python Risk Identification Toolkit (Microsoft)](https://github.com/Azure/PyRIT) — attacker-loop scaffolding referenced from chapter 06 for chain-aware attacker construction.
- [Promptfoo — red-team + eval harness](https://www.promptfoo.dev/) — CI-integratable regression runner for the AASS's cell suite.
- [Inspect — UK AISI evaluation framework](https://inspect.aisi.org.uk/) — evaluation framework used by AISI publications; a natural runner for chapter-06 benchmark cells.
- [Rebuff — prompt-injection defence library](https://github.com/protectai/rebuff) — a practitioner-scale defence stack for the posture-1 loadout.
- [PoisonedRAG — reference implementation](https://github.com/sleeepeer/PoisonedRAG) — companion code for the PoisonedRAG paper; useful for exercise 02. <!-- needs-research: confirm the currently maintained PoisonedRAG repository URL and the license terms. -->
- [AgentPoison — reference implementation](https://github.com/BillChan226/AgentPoison) — companion code for the AgentPoison paper; useful for exercise 02. <!-- needs-research: confirm the currently maintained AgentPoison repository URL and license. -->

---

## Related standards this module plugs into

Same list as mod-102 / mod-103 / mod-104 for continuity; keep for cross-reference when an AASS finding routes into the operator's compliance framework.

- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html)
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html)
- [ISO/IEC 42005:2025 — AI system impact assessment](https://www.iso.org/standard/44545.html)
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Article 55 (systemic-risk models) and Article 73 (serious-incident reporting) are the ones a mod-112 disclosure most often touches when the AASS surfaces a high-severity chain.
- [Microsoft AI Red Team Guide](https://learn.microsoft.com/en-us/security/ai-red-team/) — operator-side red-team methodology complementary to the OWASP GenAI Red Teaming Guide.
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) — practitioner methodology chapter 06 aligns with.

---

## Adjacent tracks in the AI Career Curriculum ecosystem

The peer-role handoffs chapter 07 codifies.

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — level-25 prerequisite. Owns the general LLM-security craft (garak, PyRIT, Promptfoo, NIST AI 100-2, OWASP ML Top 10, STRIDE / LINDDUN). Not duplicated here.
- [`senior-agentic-ai-engineer-learning`](https://github.com/ai-engineering-curriculum/senior-agentic-ai-engineer-learning) — the level-40 agentic-AI peer whose architecture patterns the AASS attacks and whose pattern-level remediations chapter 07 boundary 1 routes to. <!-- needs-research: confirm the exact repository URL. -->
- [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — the level-35 platform-security peer that owns tool-runtime hardening (sandboxes, MCP-server hardening, credential brokers, egress proxies). Chapter 07 boundary 2 routes to it.
- [`security-learning`](https://github.com/ai-infra-curriculum/security-learning) — level-35 platform-security sibling; complementary to `ai-infra-security-learning` for classical infra-security craft (SSRF, container escape, IAM). AASS findings that compose with classical infra bugs cross-route here.
- [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) — level-30 AI Engineering peer that owns the app-side trace format and judge scaffolds the AASS's chain-aware judge composes with. <!-- needs-research: confirm the exact repository URL. -->
- [`model-evaluation-engineer-learning`](https://github.com/ai-engineering-curriculum/model-evaluation-engineer-learning) — level-30 sibling that supplies the statistical calibration methodology (best-of-N CI, judge–human agreement) chapter 06 leans on. <!-- needs-research: confirm the exact repository URL. -->
- [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) — level 50; consumes the release-gate policy from exercise 05 and the disclosure severity annotations from exercises 01–05.

---

## Upstream and downstream modules in this track

Read mod-101, mod-102, mod-103, and mod-104 first (they are prerequisites for this module). The downstream modules consume the artefacts this module ships.

- **mod-101 (Frontier Safety Frameworks)** — the RSP / Preparedness / FSF tier grounding chapter 01 references for finding severity anchoring.
- **mod-102 (Threat Modelling for Autonomous and Tool-Using Agents)** — the ATMD whose prioritised (surface × persona) cells inform which chapters and cells the AASS runs first.
- **mod-103 (Prompt-Injection Engineering)** — supplies the entry primitives every mod-105 chain rides on. The mod-103 PIEH is the load-bearing input for chapter 02.
- **mod-104 (Jailbreak Engineering)** — supplies the unlock when a chain requires the model to bypass a usage policy. The mod-104 JEH is the load-bearing input for chains that touch policy-forbidden capabilities.
- **mod-106 (Dangerous-Capability Evaluations)** — CBRN, cyber-uplift, autonomous-replication, R&D-uplift capability benchmarks. Agent chains this module produces that elicit dangerous capability route findings to mod-106; the capability-level evaluations themselves live in mod-106.
- **mod-107 (Excessive-Agency Containment)** — engineers the sandboxing, egress policy, credential-scoping, and capability-token issuers the AASS measures under posture 2. This module measures; mod-107 engineers.
- **mod-108 (Frontier Guardrails and Monitors)** — trains the classifier guards, constitutional classifiers, and monitors the AASS measures under composed postures. This module measures; mod-108 trains.
- **mod-109 (Safety Cases and Structured Argumentation)** — consumes the AASS coverage report as evidence in the safety case.
- **mod-110 (Control and Deception Evaluation)** — adjacent; findings where the target *complies while deceiving* about the tool-call arguments route here alongside this module.
- **mod-111 (Automated and Scaled Red-Teaming)** — industrialises the AASS's per-cell chain construction into scaled LLM-vs-LLM chains. The stable interfaces this module exposes (attack families, defensive postures, judge scaffold, cost / throughput envelope) are what mod-111 orchestrates.
- **mod-112 (Safety Program and Disclosure)** — consumes high-severity findings for coordinated disclosure. Chain findings above the disclosure severity threshold route through the mod-112 workflow before publication.
