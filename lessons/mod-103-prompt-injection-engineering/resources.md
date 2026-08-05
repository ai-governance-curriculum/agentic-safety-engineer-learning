# Resources for mod-103 — Prompt-Injection Engineering

This is the curated primary-source reading list for the module. Every chapter links to a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

Numerical IDs — OWASP LLM Top 10 numbering, MITRE ATLAS technique IDs, OWASP Agentic threat numbers — move between versions. Note the version-observed date on any citation you drop into a PIEH finding or defence-catalogue row.

The mod-102 resource list carries the general threat-modelling reading base this module builds on (OWASP GenAI hub, MITRE ATLAS hub, NIST AI RMF, AIID, OECD.AI, MIT AI Risk Repository, etc.). Rather than duplicate that list, this file focuses on the **injection-specific** primaries: the papers, standards, and tools cited in chapters 01–09.

---

## The two anchor overlays (chapter 01)

Every finding this module emits carries at least one label from each.

- [OWASP Top 10 for LLM Applications (2025) — project page](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — anchors **LLM01 Prompt Injection** and the direct-vs-indirect split chapter 01 adopts verbatim.
- [MITRE ATLAS — hub](https://atlas.mitre.org/) and [technique catalogue](https://atlas.mitre.org/techniques) — anchors the `Prompt Injection` technique (`AML.T0051` as of the ATLAS version the chapters cite).
- [OWASP GenAI Security Project — hub](https://genai.owasp.org/) — umbrella for LLM01 mitigation guidance, the Agentic Threats paper, the Red Teaming Guide.

<!-- needs-research: pin the currently published OWASP LLM01 sub-class enumeration + mitigation list and the current ATLAS technique ID and parent tactic for prompt injection, with observed-on dates. Both have iterated. -->

---

## Foundational papers on indirect prompt injection (chapters 03, 04)

The load-bearing academic grounding for the indirect channel taxonomy.

- [Greshake et al. — "Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection" (2023)](https://arxiv.org/abs/2302.12173) — the canonical framing paper. Required reading before chapter 03 and before exercise 02.
- [Zhan et al. — "InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated LLM Agents" (2024)](https://arxiv.org/abs/2403.02691) — the benchmark chapter 03 cites for tool-response injection against tool-using agents. Required reading before exercise 02.
- [Debenedetti et al. — "AgentDojo: A Dynamic Environment to Evaluate Attacks and Defenses for LLM Agents" (2024)](https://arxiv.org/abs/2406.13352) — cross-tool exploitation benchmark including memory writes, referenced in chapter 04.
- [Yi et al. — "Benchmarking and Defending Against Indirect Prompt Injection Attacks on Large Language Models" (2023)](https://arxiv.org/abs/2312.14197) — earlier indirect-injection benchmarking work.
- [Willison — "Prompt injection: What's the worst that can happen?" (2023)](https://simonwillison.net/2023/Apr/14/worst-that-can-happen/) — practitioner-level framing that predates most academic treatment; referenced in chapters 01 and 03.
- [Willison — "prompt injection" tag on simonwillison.net](https://simonwillison.net/tags/prompt-injection/) — the running index of incidents, defence claims, and defence failures. The most useful single link for keeping current between formal-literature updates.

---

## Long-term memory and cross-plugin channels (chapter 04)

The persistent / transitive channel literature.

- [Zou et al. — "PoisonedRAG: Knowledge Corruption Attacks to Retrieval-Augmented Generation of Large Language Models" (2024)](https://arxiv.org/abs/2402.07867) — corpus-poisoning against RAG.
- [Chaudhari et al. — "Phantom: General Trigger Attacks on Retrieval Augmented Language Generation" (2024)](https://arxiv.org/abs/2405.20485) — trigger-conditioned RAG attacks; grounds chapter 04's latent-activation discussion.
- [Xue et al. — "BadRAG: Identifying Vulnerabilities in Retrieval Augmented Generation of Large Language Models" (2024)](https://arxiv.org/abs/2406.00083)
- [Rehberger — "ChatGPT: Hacking Memories with Prompt Injection" (Embrace The Red, 2024)](https://embracethered.com/blog/posts/2024/chatgpt-hacking-memories/) — public write-up of the ChatGPT long-term-memory injection surface; grounds chapter 04's per-user memory channel.
- [Ju et al. — "Flooding Spread of Manipulated Knowledge in LLM-Based Multi-Agent Communities" (2024)](https://arxiv.org/abs/2407.07791) — poison propagation across an agent graph; relevant to chapter 04's cross-plugin section and mod-105.
- [Gu et al. — "Agent Smith: A Single Image Can Jailbreak One Million Multimodal LLM Agents Exponentially Fast" (2024)](https://arxiv.org/abs/2402.08567) — exponential-spread demonstration in a multimodal multi-agent setting.

<!-- needs-research: track successor multi-agent-injection demonstrations as the literature matures; this is one of the fastest-moving corners and will need refresh cadence. -->

---

## Obfuscation vectors (chapter 05)

Per-vector primary sources.

### Unicode confusables / homoglyphs / invisible characters

- [Unicode Technical Standard #39 — "Unicode Security Mechanisms"](https://www.unicode.org/reports/tr39/) — the confusables data and the security-considerations reasoning.
- [Unicode Annex #9 — "Unicode Bidirectional Algorithm"](https://www.unicode.org/reports/tr9/) — bidi controls (U+202D, U+202E, U+061C) relevant to the invisible-character vector.
- [Unicode Character Database](https://www.unicode.org/ucd/) — canonical index; searchable for the Tags block (U+E0000–U+E007F, U+E0100–U+E01EF).
- [Boucher & Anderson — "Trojan Source: Invisible Vulnerabilities" (2021)](https://arxiv.org/abs/2111.00169) — the source-code analogue that popularised bidi-control awareness; still the readable framing for why invisible-character defences must cover the Unicode surface generally, not one character at a time.
- [Riley Goodside — thread demonstrating Unicode Tag character prompt injection (2024)](https://twitter.com/goodside/status/1745511940351287394) — the widely-cited public demonstration of the Tags-block injection channel. <!-- needs-research: cite the first academic writeup of the Tag-character injection surface if / when it lands, plus the major frontier labs' documented responses. -->

### Encoded payloads (base64 / hex / cipher)

- OWASP LLM01 mitigation guidance on encoded payloads — see the OWASP LLM Top 10 project page above.
- [Willison — "prompt injection" tag, encoded-payload write-ups](https://simonwillison.net/tags/prompt-injection/) — running catalogue of specific incidents.

<!-- needs-research: pick two or three specific publicly-documented base64 / hex-encoded prompt-injection payloads that succeeded against named production systems, with dates, and cite them from chapter 05. -->

### Low-resource-language translation

- [Yong et al. — "Low-Resource Languages Jailbreak GPT-4" (2023)](https://arxiv.org/abs/2310.02446) — the paper chapter 05 cites for the low-resource-language vector.
- [Deng et al. — "Multilingual Jailbreak Challenges in Large Language Models" (2023)](https://arxiv.org/abs/2310.06474) — companion multilingual-jailbreak evaluation.

### ASCII art

- [Jiang et al. — "ArtPrompt: ASCII Art-based Jailbreak Attacks Against Aligned LLMs" (2024)](https://arxiv.org/abs/2402.11753) — the paper chapter 05 cites for the ASCII-art vector.

### Tool-response HTML / markdown-image exfiltration

- [Rehberger — "Data Exfiltration via ChatGPT" and related write-ups on the markdown-image exfiltration pattern (Embrace The Red)](https://embracethered.com/blog/posts/2023/ai-injections-threats-context-matters/) — a recurring venue for concrete markdown-image exfil demonstrations against production chat products.
- [Willison — "Markdown image exfiltration" writeups](https://simonwillison.net/tags/prompt-injection/) — indexed under the prompt-injection tag.

<!-- needs-research: pick two or three specific publicly-documented markdown-image exfiltration incidents in production chat products (ChatGPT plugins, Bing Chat, Google Bard early demos), with dates and CVE / disclosure references if available, and cite them from chapter 05. -->

### Prompt-template / delimiter spoofing

- Model-family chat-template documentation — see the "Model chat templates" section below for the specific templates (Llama, GPT, Claude, Gemini) whose delimiter tokens the vector abuses.

---

## Broader jailbreak literature that neighbours injection (chapters 02, 05, 08)

Injection-delivered refusal-bypass payloads ride on the general jailbreak literature. mod-104 owns the jailbreak methodology at depth; this module cites the boundary.

- [Wei et al. — "Jailbroken: How Does LLM Safety Training Fail?" (2023)](https://arxiv.org/abs/2307.02483) — the framing paper on why safety training fails under adversarial framing.
- [Zou et al. — "Universal and Transferable Adversarial Attacks on Aligned Language Models" (GCG, 2023)](https://arxiv.org/abs/2307.15043)
- [Chao et al. — "Jailbreaking Black Box Large Language Models in Twenty Queries" (PAIR, 2023)](https://arxiv.org/abs/2310.08419)
- [Mehrotra et al. — "Tree of Attacks: Jailbreaking Black-Box LLMs Automatically" (TAP, 2023)](https://arxiv.org/abs/2312.02119)
- [Anil et al. — "Many-shot Jailbreaking" (Anthropic, 2024)](https://www.anthropic.com/research/many-shot-jailbreaking)
- [Shen et al. — "Do Anything Now: Characterizing and Evaluating In-The-Wild Jailbreak Prompts on Large Language Models" (2024)](https://arxiv.org/abs/2308.03825) — the DAN-family corpus chapter 02 references.
- [Russinovich et al. — "Great, Now Write an Article About That: The Crescendo Multi-Turn LLM Jailbreak Attack" (Microsoft, 2024)](https://arxiv.org/abs/2404.01833) — the multi-turn erosion pattern chapter 08 cites for adaptive attacks against sandwich prompting.
- [Schulhoff et al. — "Ignore This Title and HackAPrompt: Exposing Systemic Vulnerabilities of LLMs" (2023)](https://arxiv.org/abs/2311.16119) — competition-scale corpus of successful prompt-injection and jailbreak payloads with taxonomy.

---

## Defences (chapter 07)

The five layers the defence catalogue starts from.

- [Hines et al. — "Defending Against Indirect Prompt Injection Attacks With Spotlighting" (Microsoft, 2024)](https://arxiv.org/abs/2403.14720) — layer 1.
- [Chen et al. — "StruQ: Defending Against Prompt Injection with Structured Queries" (2024)](https://arxiv.org/abs/2402.06363) — layer 2.
- [Wallace et al. — "The Instruction Hierarchy: Training LLMs to Prioritize Privileged Instructions" (OpenAI, 2024)](https://arxiv.org/abs/2404.13208) — the model-side principal-hierarchy training programme that grounds several of chapter 07's layer-2 and layer-4 assumptions.
- [Piet et al. — "Jatmo: Prompt Injection Defense by Task-Specific Finetuning" (2023)](https://arxiv.org/abs/2312.17673) — task-specific fine-tuning as a defence layer; useful comparator for StruQ.
- [Anthropic — "Constitutional Classifiers: Defending against universal jailbreaks" (2025)](https://www.anthropic.com/research/constitutional-classifiers) — the classifier-guard state of the art referenced in chapter 07 layer 4.
- [Sharma et al. — "Constitutional Classifiers: Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming" (2025)](https://arxiv.org/abs/2501.18837) — the companion arXiv paper. <!-- needs-research: confirm the currently published version and citation of the Sharma et al. paper and pin its measured robustness numbers against adaptive attackers. -->
- [Willison — "Design Patterns for Securing LLM Agents against Prompt Injections" (2025)](https://simonwillison.net/2025/Apr/9/llm-security-design-patterns/) — practitioner-level rundown of the boundary-control patterns (dual-LLM, action-scoping, provenance-labelling) that map onto chapter 07 layer 5.
- [Debenedetti et al. — "Defending against Prompt Injection with a Few DefensiveTokens" (2024)](https://arxiv.org/abs/2405.09113) — companion defence-token approach; useful comparator for spotlighting.
- [Zverev et al. — "Can LLMs Separate Instructions from Data? And What Do We Even Mean By That?" (2024)](https://arxiv.org/abs/2403.06833) — probes the boundary the chapter-07 layers are all trying to strengthen.

---

## Adaptive attack methodology (chapter 08)

The general adversarial-robustness discipline transferred to injection.

- [Carlini et al. — "On Evaluating Adversarial Robustness" (2019)](https://arxiv.org/abs/1902.06705) — the canonical adaptive-attack methodology paper. Chapter 08 leans on it explicitly.
- [Tramer et al. — "On Adaptive Attacks to Adversarial Example Defenses" (2020)](https://arxiv.org/abs/2002.08347) — companion methodology; the same discipline applied to a broader defence catalogue.
- [Andriushchenko et al. — "Jailbreaking Leading Safety-Aligned LLMs with Simple Adaptive Attacks" (2024)](https://arxiv.org/abs/2404.02151) — direct transfer of adaptive-attack methodology to LLM defences; grounds chapter 08's per-layer playbook style.
- [Perez & Ribeiro — "Ignore Previous Prompt: Attack Techniques For Language Models" (2022)](https://arxiv.org/abs/2211.09527) — foundational catalogue of the direct-injection attack primitives chapter 02 lists.

---

## Evaluation harness plumbing (chapters 06, 09)

Judge design, calibration, agent-eval infrastructure.

- [Zheng et al. — "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena" (2023)](https://arxiv.org/abs/2306.05685) — the reference for the LLM-judge scaffolding chapter 06 assumes.
- [Souly et al. — "A StrongREJECT for Empty Jailbreaks" (2024)](https://arxiv.org/abs/2402.10260) — judge design for jailbreak-adjacent evaluation; chapter 08 and mod-111 reference the pattern.
- [Andriushchenko et al. — "AgentHarm: A Benchmark for Measuring Harmfulness of LLM Agents" (2024)](https://arxiv.org/abs/2410.09024) — harmfulness-benchmark methodology for agent settings.
- [Ruan et al. — "ToolEmu: Identifying the Risks of LM Agents with an LM-Emulated Sandbox" (2023)](https://arxiv.org/abs/2309.15817) — evaluation-time sandbox for agent tool-use.
- [Kinniment et al. (METR) — "Evaluating Language-Model Agents on Realistic Autonomous Tasks" (2023)](https://arxiv.org/abs/2312.11671) — realistic-task evaluation baseline.

---

## Agent protocols and dynamic tool discovery (chapters 03, 04)

The cross-plugin channel targets.

- [Model Context Protocol (MCP) — specification](https://modelcontextprotocol.io/)
- [MCP on GitHub](https://github.com/modelcontextprotocol) — including the reference servers whose tool descriptions and responses are the chapter-04 attack surface.
- [Google — Agent2Agent (A2A) protocol](https://google.github.io/A2A/) — cross-agent messaging spec; the chapter-04 peer-agent channel.
- [OpenAI — Function calling and tools documentation](https://platform.openai.com/docs/guides/function-calling)
- [Anthropic — Tool use with Claude](https://docs.anthropic.com/en/docs/build-with-claude/tool-use)
- [Anthropic — Model Context Protocol overview](https://docs.anthropic.com/en/docs/agents-and-tools/mcp)

<!-- needs-research: track competing / successor agent-communication protocols and their tool-manifest / tool-description handling as the space matures; the cross-plugin channel is one of the fastest-mutating surfaces. -->

---

## Model chat templates (chapter 05 — delimiter-spoof vector)

Per-model chat templates and role delimiters, referenced by chapter 05's template-delimiter-spoof vector and chapter 07's spotlighting discussion. Sanitisers must know the *specific* delimiter tokens for the *specific* model served.

- [Llama chat templates — Meta developer docs](https://www.llama.com/docs/model-cards-and-prompt-formats/)
- [OpenAI chat completion messages](https://platform.openai.com/docs/api-reference/chat) — the `role`/`content` conventions that render into the model input.
- [Anthropic messages API reference](https://docs.anthropic.com/en/api/messages)
- [Google Gemini API reference](https://ai.google.dev/api) — role-token conventions.
- [Hugging Face — Chat Templates documentation](https://huggingface.co/docs/transformers/chat_templating) — the canonical guide to how chat-template renderers work in the open-source stack.

---

## Vendor safety documentation and system cards

Referenced for defence claims chapters 07 and 09 measure the operator against.

- [Anthropic — Claude System Cards](https://www.anthropic.com/news) — browse for the current model's system card and its prompt-injection / defence-in-depth sections.
- [OpenAI — System Cards](https://openai.com/safety/) — GPT-4 family, o-series, etc.
- [Google DeepMind — Responsibility & Safety publications](https://deepmind.google/about/responsibility-safety/)
- [Microsoft — Responsible AI Standard and product security documentation](https://www.microsoft.com/en-us/ai/responsible-ai) — for Bing / Copilot family injection incident response.

---

## Practitioner venues that publish injection incidents (chapters 04, 05)

Ongoing sources for the incidents the chapters cite and the exercises reference.

- [Simon Willison's weblog — "prompt injection" tag](https://simonwillison.net/tags/prompt-injection/)
- [Embrace The Red (Johann Rehberger)](https://embracethered.com/blog/) — the venue for many of the ChatGPT-plugin and long-term-memory injection write-ups.
- [Kai Greshake's blog and code](https://kai-greshake.de/) — the author of the foundational Greshake et al. (2023) paper continues to publish related work.
- [PortSwigger Research — LLM attack write-ups](https://portswigger.net/research/topics/ai) — application-security-flavoured LLM incident write-ups.
- [OWASP GenAI Security Project — resources](https://genai.owasp.org/) — collects vendor and community write-ups under the LLM01 umbrella.

---

## Related standards this module plugs into

Same list as mod-102 for continuity; keep for cross-reference when a PIEH finding routes into the operator's compliance framework.

- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html)
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html)
- [ISO/IEC 42005:2025 — AI system impact assessment](https://www.iso.org/standard/44545.html)
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Article 55 and Article 73 are the ones a mod-112 disclosure most often touches.
- [NIST AI 100-2 E2023 — Adversarial ML: A Taxonomy and Terminology of Attacks and Mitigations](https://csrc.nist.gov/pubs/ai/100/2/e2023/final) — the level-25 prerequisite's taxonomy; the module's prompt-injection labels cross-reference back to it.
- [Microsoft AI Red Team Guide](https://learn.microsoft.com/en-us/security/ai-red-team/) — operator-side red-team methodology that complements the OWASP GenAI Red Teaming Guide.
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) — the practitioner methodology chapter 06 aligns with.

---

## Tooling worth knowing (chapters 06, 08; deep coverage in mod-108 / mod-111)

Named here so you can install and try them while reading the module.

- [garak — LLM vulnerability scanner (NVIDIA)](https://github.com/NVIDIA/garak) — carries a prompt-injection probe set out of the box.
- [PyRIT — Python Risk Identification Toolkit (Microsoft)](https://github.com/Azure/PyRIT) — attacker-loop scaffolding referenced from chapter 08's scripted-attacker example.
- [Promptfoo — red-team + eval harness](https://www.promptfoo.dev/) — application-side eval harness that co-exists with the PIEH per chapter 09.
- [Inspect — UK AISI evaluation harness](https://inspect.aisi.org.uk/) — evaluation framework used by AISI publications.
- [AgentDojo — agent-attack benchmark](https://github.com/ethz-spylab/agentdojo) — companion benchmark implementation of Debenedetti et al. (2024).
- [InjecAgent — indirect-injection benchmark](https://github.com/uiuc-kang-lab/InjecAgent) — companion implementation of Zhan et al. (2024). Required reading before exercise 02.
- [Rebuff — prompt-injection defence library](https://github.com/protectai/rebuff) — a practitioner-scale defence stack for cross-reference against chapter 07's layers.

---

## Adjacent tracks in the AI Career Curriculum ecosystem

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — level-25 prerequisite. Owns the general LLM-security craft (OWASP ML Top 10, NIST AI 100-2, STRIDE / LINDDUN) this module assumes.
- [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) — the level-30 AI Engineering peer that owns the app-side trace format, judge scaffolds, and eval-gated CI plumbing the PIEH consumes. Chapter 09 codifies the contract. <!-- needs-research: confirm the exact repository URL for this peer track. -->
- [`senior-agentic-ai-engineer-learning`](https://github.com/ai-engineering-curriculum/senior-agentic-ai-engineer-learning) — the agentic-AI peer whose architecture specification is the input to your PIEH target definition. <!-- needs-research: confirm the exact repository URL. -->
- [`model-evaluation-engineer-learning`](https://github.com/ai-engineering-curriculum/model-evaluation-engineer-learning) — level-30 sibling that supplies statistical calibration methodology (best-of-N CI, judge–human agreement). <!-- needs-research: confirm the exact repository URL. -->
- [`security-learning`](https://github.com/ai-infra-curriculum/security-learning) and [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — level-35 platform-security peers that co-own the boundary-controls layer (chapter 07 layer 5) at platform scale.
- [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) — level 50; consumes the release-gate policy from chapter 09.

---

## Upstream and downstream modules in this track

Read mod-101 and mod-102 first (they are prerequisites). The downstream modules consume the artefacts this module ships.

- **mod-101 (Frontier Safety Frameworks)** — the RSP / Preparedness / FSF shape that grounds chapter 08's elicitation-gap discussion.
- **mod-102 (Agent Threat Modelling)** — the ATMD whose prioritised (surface × persona) cells feed the PIEH's Tier A cell selection.
- **mod-104 (Jailbreak Engineering)** — owns the general jailbreak-construction methodology this module cites the boundary against. Refusal-bypass primitives delivered *through* injection channels are in-scope here; the general jailbreak methodology is in mod-104.
- **mod-105 (Agent-Specific Attack-Surface Engineering)** — owns tool-abuse chains, memory-poisoning at scale, long-horizon planning subversion, multi-agent adversarial coordination. This module supplies the injection primitives those chains ride on.
- **mod-107 (Excessive-Agency Containment)** — owns the sandboxing, egress policy, credential-scoping engineering that implements chapter 07 layer 5. This module measures the layer's coverage; mod-107 engineers it.
- **mod-108 (Frontier Guardrails and Monitors)** — owns classifier-guard training and constitutional-classifier methodology. This module measures the classifier defence layer; mod-108 trains it.
- **mod-111 (Automated and Scaled Red-Teaming)** — industrialises the scripted-attacker loop from chapter 08 into LLM-vs-LLM chains and StrongREJECT-style judge industrialisation. This module ships the harmful-payload-discipline pattern mod-111 codifies at scale.
- **mod-112 (Safety Program and Disclosure)** — consumes findings above the disclosure severity threshold and routes them through the disclosure workflow. Adaptive-ASR numbers (chapter 08) are the honest numbers a mod-112 disclosure cites.
