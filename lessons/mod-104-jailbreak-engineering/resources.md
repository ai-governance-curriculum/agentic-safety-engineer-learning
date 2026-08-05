# Resources for mod-104 — Jailbreak Engineering at Frontier Depth

This is the curated primary-source reading list for the module. Every chapter cites a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

Benchmark scores, transfer rates, per-language ASR numbers, and attack-family thresholds move with every model release and every safety-tuning pass. Any figure you drop into a JEH artefact from these sources needs a re-verification pass — chapter 02 (transfer rates), chapter 05 (many-shot thresholds, per-language / per-cipher ASR), chapter 06 (benchmark version details), and chapter 07 (StrongREJECT rubric specifics) all flag this explicitly. Note the observed-on date on any citation you drop into a finding.

The `ai-risk-engineer` prerequisite (level 25) covers garak, PyRIT, Promptfoo, the general NIST AI 100-2 / OWASP ML Top 10 baseline, and standard red-team hygiene; that reading is not duplicated here. Mod-103's `resources.md` (companion module) carries the injection-specific primaries; this file focuses on the **jailbreak-specific** primaries cited across chapters 01–09.

---

## The four attack-family primaries (chapters 02–05)

These are the primaries that name the attack families the JEH owns.

### White-box gradient (chapter 02)

- [Zou, Wang, Kolter, Fredrikson — "Universal and Transferable Adversarial Attacks on Aligned Language Models" (GCG, 2023)](https://arxiv.org/abs/2307.15043) — the foundational paper. Required reading before chapter 02 and exercise 01.
- [Liu et al. — "AutoDAN: Generating Stealthy Jailbreak Prompts on Aligned Large Language Models" (2023)](https://arxiv.org/abs/2310.04451) — fluent-suffix genetic search; chapter 02's descendant discussion.
- [Liu et al. — "AutoDAN-Turbo: A Lifelong Agent for Strategy Self-Exploration to Jailbreak LLMs" (2024)](https://arxiv.org/abs/2410.05295) — strategy-library-conditioned attacker LLM. <!-- needs-research: confirm the current arXiv version and cited results before quoting. -->
- [Sadasivan et al. — "Fast Adversarial Attacks on Language Models In One GPU Minute" (BEAST, 2024)](https://arxiv.org/abs/2402.15570)
- [Alon & Kamfonas — "Detecting Language Model Attacks with Perplexity" (2023)](https://arxiv.org/abs/2308.14132) — the perplexity-filter defence chapter 02 cites.
- [Jain et al. — "Baseline Defenses for Adversarial Attacks Against Aligned Language Models" (2023)](https://arxiv.org/abs/2309.00614) — companion defence-baseline work.
- [Robey et al. — "SmoothLLM: Defending Large Language Models Against Jailbreaking Attacks" (2023)](https://arxiv.org/abs/2310.03684) — input-perturbation defence chapter 02 cites.
- [Zou et al. — "Improving Alignment and Robustness with Circuit Breakers" (2024)](https://arxiv.org/abs/2406.04313) — representation-engineering defence line.
- [`nanoGCG` — reference implementation of GCG](https://github.com/GraySwanAI/nanoGCG) — the practical starting point chapter 02 recommends for exercise 01.
- [`llm-attacks` — Zou et al.'s reference repository](https://github.com/llm-attacks/llm-attacks) — the original code and evaluation harness for GCG.

### Black-box LLM-vs-LLM (chapter 03)

- [Chao, Robey, Dobriban, Hassani, Pappas, Wong — "Jailbreaking Black Box Large Language Models in Twenty Queries" (PAIR, 2023)](https://arxiv.org/abs/2310.08419) — the PAIR paper; required before chapter 03 and exercise 02.
- [Mehrotra et al. — "Tree of Attacks: Jailbreaking Black-Box LLMs Automatically" (TAP, 2023)](https://arxiv.org/abs/2312.02119) — TAP paper.
- [`JailbreakBench` — reference PAIR + reference GCG-transfer implementations](https://github.com/JailbreakBench/jailbreakbench) — the codebase chapter 03 references as the leaderboard-comparable baseline.
- [Andriushchenko, Croce, Flammarion — "Jailbreaking Leading Safety-Aligned LLMs with Simple Adaptive Attacks" (2024)](https://arxiv.org/abs/2404.02151) — modern adaptive-attack baseline; useful comparator against PAIR / TAP.

### Multi-turn / Crescendo (chapter 04)

- [Russinovich, Salem, Eldan — "Great, Now Write an Article About That: The Crescendo Multi-Turn LLM Jailbreak Attack" (Microsoft, 2024)](https://arxiv.org/abs/2404.01833) — required before chapter 04 and exercise 03.
- [Yang et al. — "Chain of Attack: A Semantic-Driven Contextual Multi-Turn Attacker for LLM" (2024)](https://arxiv.org/abs/2405.05610) — companion multi-turn attack methodology. <!-- needs-research: verify the current arXiv version and cited numbers. -->
- [Rahman et al. — "X-Teaming: Multi-Turn Jailbreaks and Defenses with Adaptive Multi-Agents" (2025)](https://arxiv.org/abs/2504.13203) — successor multi-turn methodology.

### In-context, persona, low-resource, cipher (chapter 05)

- [Anil et al. — "Many-shot Jailbreaking" (Anthropic, 2024)](https://www.anthropic.com/research/many-shot-jailbreaking) — the many-shot post required before chapter 05 and exercise 04.
- [Wei, Haghtalab, Steinhardt — "Jailbroken: How Does LLM Safety Training Fail?" (2023)](https://arxiv.org/abs/2307.02483) — the framing paper on why safety training fails under adversarial framing; chapter 05 and chapter 08 both reference.
- [Yong, Menghini, Bach — "Low-Resource Languages Jailbreak GPT-4" (2023)](https://arxiv.org/abs/2310.02446) — the low-resource-language paper.
- [Deng et al. — "Multilingual Jailbreak Challenges in Large Language Models" (2023)](https://arxiv.org/abs/2310.06474) — companion multilingual evaluation.
- [Yuan et al. — "GPT-4 Is Too Smart To Be Safe: Stealthy Chat with LLMs via Cipher" (CipherChat, 2023)](https://arxiv.org/abs/2308.06463) — the CipherChat paper.
- [Jiang et al. — "ArtPrompt: ASCII Art-based Jailbreak Attacks Against Aligned LLMs" (2024)](https://arxiv.org/abs/2402.11753) — ASCII-art cipher variant.

---

## Benchmark landscape (chapter 06)

The five benchmark families the JEH ships coverage against.

### HarmBench

- [Mazeika et al. — "HarmBench: A Standardized Evaluation Framework for Automated Red Teaming and Robust Refusal" (2024)](https://arxiv.org/abs/2402.04249)
- [HarmBench project page](https://www.harmbench.org/) and [HarmBench GitHub](https://github.com/centerforaisafety/HarmBench) — behaviour set, classifier, and reproducibility conventions chapter 06 aligns with.

<!-- needs-research: verify HarmBench's exact behaviour count, category structure, and current classifier version before quoting specific numbers in an artefact. -->

### JailbreakBench

- [Chao et al. — "JailbreakBench: An Open Robustness Benchmark for Jailbreaking Large Language Models" (2024)](https://arxiv.org/abs/2404.01318)
- [JailbreakBench project page](https://jailbreakbench.github.io/) and [JailbreakBench GitHub](https://github.com/JailbreakBench/jailbreakbench) — behaviour set (harmful + benign pairs), leaderboard, reference attackers, and submission process.

<!-- needs-research: verify the current JailbreakBench behaviour count and whether the public leaderboard remains active before quoting. -->

### AIR-Bench 2024

- [Zeng et al. — "AIR-Bench 2024: A Safety Benchmark Based on Risk Categories from Regulations and Policies" (2024)](https://arxiv.org/abs/2407.17436)
- [AIR-Bench 2024 project page](https://crfm.stanford.edu/helm/air-bench/latest/) — the 314-risk-category taxonomy grounded in EU AI Act, EO 14110, and lab RSPs.

<!-- needs-research: verify AIR-Bench 2024's exact category count (314 at release), the prompts-per-category, and whether an AIR-Bench 2025 has been published. -->

### MLCommons AILuminate

- [MLCommons AILuminate benchmark page](https://mlcommons.org/benchmarks/ai-safety/) — the hazard-graded consumer-facing safety benchmark chapter 06 references.
- [MLCommons AILuminate v1.0 announcement](https://mlcommons.org/2024/12/ailuminate/) — release notes.

<!-- needs-research: verify AILuminate's current hazard categories, prompt-set size, judge protocol, and grading scale before quoting. -->

### Meta CyberSecEval

- [Bhatt et al. — "Purple Llama CyberSecEval: A Secure Coding Benchmark for Language Models" (v1, 2023)](https://arxiv.org/abs/2312.04724)
- [Bhatt et al. — "CyberSecEval 2: A Wide-Ranging Cybersecurity Evaluation Suite for Large Language Models" (2024)](https://arxiv.org/abs/2404.13161)
- [Wan et al. — "CYBERSECEVAL 3: Advancing the Evaluation of Cybersecurity Risks and Capabilities in Large Language Models" (2024)](https://arxiv.org/abs/2408.01605)
- [Purple Llama on GitHub](https://github.com/meta-llama/PurpleLlama) — the CyberSecEval codebase.

<!-- needs-research: verify Meta CyberSecEval's current major version and test list; the benchmark evolves rapidly. -->

### Adjacent benchmarks referenced across the module

- [Andriushchenko et al. — "AgentHarm: A Benchmark for Measuring Harmfulness of LLM Agents" (2024)](https://arxiv.org/abs/2410.09024) — agent-setting harmfulness benchmark referenced across mod-102 / mod-104 / mod-105.
- [Röttger et al. — "XSTest: A Test Suite for Identifying Exaggerated Safety Behaviours in Large Language Models" (2023)](https://arxiv.org/abs/2308.01263) — the over-refusal control chapter 07 recommends alongside JailbreakBench benign pairs.
- [Schulhoff et al. — "Ignore This Title and HackAPrompt: Exposing Systemic Vulnerabilities of LLMs" (2023)](https://arxiv.org/abs/2311.16119) — competition-scale corpus of successful jailbreak and injection payloads with taxonomy.

---

## Judge methodology (chapter 07)

- [Souly et al. — "A StrongREJECT for Empty Jailbreaks" (2024)](https://arxiv.org/abs/2402.10260) — the StrongREJECT paper; required before chapter 07 and exercise 06.
- [`strong_reject` — reference implementation](https://github.com/alexandrasouly/strongreject) — the paper's open-source judge implementation.
- [Zheng et al. — "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena" (2023)](https://arxiv.org/abs/2306.05685) — the general LLM-as-judge scaffolding chapter 07's implementation pattern builds on.
- [Chao et al. — "JailbreakBench" (2024)](https://arxiv.org/abs/2404.01318) — also carries a documented judge protocol referenced by chapter 07's discussion of native-judge vs. StrongREJECT-style cross-check.
- [Röttger et al. — "XSTest" (2023)](https://arxiv.org/abs/2308.01263) — over-refusal control (repeated from above; chapter 07 references it specifically for the over-refusal-alongside-ASR pairing).

<!-- needs-research: verify Souly et al.'s exact rubric dimensions, scoring scale, and Appendix composite formula before quoting from chapter 07 in a JEH artefact. -->

---

## In-the-wild taxonomy and DAN-family evolution (chapter 08)

- [Shen et al. — "'Do Anything Now': Characterizing and Evaluating In-The-Wild Jailbreak Prompts on Large Language Models" (2024)](https://arxiv.org/abs/2308.03825) — the DAN-family corpus and taxonomy chapter 08 builds on; required before chapter 08 and exercise 07.
- [Deng et al. — "MASTERKEY: Automated Jailbreaking of Large Language Model Chatbots" (2023)](https://arxiv.org/abs/2307.08715) — companion in-the-wild taxonomy work chapter 08 cites.
- [Wei, Haghtalab, Steinhardt — "Jailbroken: How Does LLM Safety Training Fail?" (2023)](https://arxiv.org/abs/2307.02483) — chapter 08's competing-objective / mismatched-generalization framing.
- [`in-the-wild-jailbreak-prompts` — Shen et al.'s public corpus release](https://github.com/verazuo/jailbreak_llms) — the actual corpus.
- [Perez & Ribeiro — "Ignore Previous Prompt: Attack Techniques For Language Models" (2022)](https://arxiv.org/abs/2211.09527) — foundational catalogue that predates most in-the-wild work; useful for the format-hijack and authority-framing dimensions.

<!-- needs-research: verify Shen et al.'s reported corpus size and taxonomy dimension list before quoting; the corpus grows with subsequent releases. -->

---

## Adaptive-attack methodology (chapters 02, 07)

- [Carlini et al. — "On Evaluating Adversarial Robustness" (2019)](https://arxiv.org/abs/1902.06705) — the canonical adaptive-attack methodology paper; chapter 07's adaptive-attack-survival metric leans on it.
- [Tramer et al. — "On Adaptive Attacks to Adversarial Example Defenses" (2020)](https://arxiv.org/abs/2002.08347) — companion methodology across a broader defence catalogue.
- [Andriushchenko, Croce, Flammarion — "Jailbreaking Leading Safety-Aligned LLMs with Simple Adaptive Attacks" (2024)](https://arxiv.org/abs/2404.02151) — direct application of adaptive-attack methodology to modern LLM defences.

---

## Frontier lab safety frameworks and evaluation practice (chapters 01, 06, 07)

The RSP / Preparedness / FSF elicitation-gap grounding chapter 01 and chapter 07 reference. These are also mod-101's territory; cited here for the interfaces the JEH exposes.

- [Anthropic Responsible Scaling Policy (RSP) — current version](https://www.anthropic.com/rsp)
- [OpenAI Preparedness Framework](https://openai.com/safety/preparedness/)
- [Google DeepMind Frontier Safety Framework (FSF)](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/)
- [Anthropic Usage Policies](https://www.anthropic.com/legal/aup) — the policy chapter 01 references for what "policy the model would refuse" means for Anthropic models.
- [OpenAI Usage Policies](https://openai.com/policies/usage-policies/) — same, for OpenAI models.
- [Google Prohibited Use Policy for Generative AI](https://policies.google.com/terms/generative-ai/use-policy) — same, for Gemini-family models.

---

## Constitutional classifiers and jailbreak defences (chapters 02, 07; deep coverage in mod-108)

- [Anthropic — "Constitutional Classifiers: Defending against universal jailbreaks" (2025)](https://www.anthropic.com/research/constitutional-classifiers) — the classifier-guard state of the art referenced in chapter 07.
- [Sharma et al. — "Constitutional Classifiers: Defending against Universal Jailbreaks across Thousands of Hours of Red Teaming" (2025)](https://arxiv.org/abs/2501.18837) — the companion arXiv paper.
- [Anthropic — "Circuit Breakers" post](https://www.anthropic.com/research/circuit-breakers) and [Zou et al. — "Improving Alignment and Robustness with Circuit Breakers" (2024)](https://arxiv.org/abs/2406.04313) — representation-engineering defence line.
- [Inan et al. — "Llama Guard: LLM-based Input-Output Safeguard for Human-AI Conversations" (2023)](https://arxiv.org/abs/2312.06674) — one of the open-source classifier guards chapter 06's defence-column column references.
- [ShieldGemma — Google's safety classifier family](https://arxiv.org/abs/2407.21772) — a comparator open guard.

<!-- needs-research: track new frontier-lab guardrail publications; this row moves quickly and mod-108 owns the depth. -->

---

## Working red-team suites (chapter 09 — the `ai-risk-engineer` prerequisite interface)

The three suites chapter 09 codifies the JEH as consuming, not re-implementing.

- [garak — LLM vulnerability scanner (NVIDIA)](https://github.com/NVIDIA/garak) — plug-in catalogue includes DAN variants, encoding tricks, refusal probes.
- [PyRIT — Python Risk Identification Toolkit (Microsoft)](https://github.com/Azure/PyRIT) — multi-turn attackers, orchestrators, scoring.
- [Promptfoo — red-team + eval harness](https://www.promptfoo.dev/) — CI-integratable regression runner for the chapter-08 fixture library.
- [Inspect — UK AISI evaluation framework](https://inspect.aisi.org.uk/) — used by AISI publications; a natural runner for chapter-06 benchmark cells.

---

## Vendor safety documentation and system cards (chapters 01, 06)

Referenced for policy-refusal definitions and defence-stack claims the JEH measures against.

- [Anthropic — Claude system cards and safety announcements](https://www.anthropic.com/news) — browse for the current model's system card.
- [OpenAI — System cards](https://openai.com/safety/) — GPT-4 family, o-series, GPT-5 family, etc.
- [Google DeepMind — Responsibility & Safety publications](https://deepmind.google/about/responsibility-safety/)
- [Meta — Purple Llama and Llama safety documentation](https://ai.meta.com/llama/purple-llama/)
- [UK AISI — model evaluation reports](https://www.aisi.gov.uk/) — third-party evaluations that ground chapter 06's expectations about what a JEH's numbers look like next to a national-institute evaluation.

---

## Practitioner venues that publish jailbreak incidents and defences

Ongoing sources for chapters 05 and 08's in-the-wild material and mod-112 disclosure references.

- [Simon Willison's weblog — "jailbreaking" tag](https://simonwillison.net/tags/jailbreaking/) — the running practitioner index.
- [Embrace The Red (Johann Rehberger)](https://embracethered.com/blog/) — a recurring venue for concrete jailbreak demonstrations against production products.
- [Pliny the Liberator — public jailbreak demonstrations](https://twitter.com/elder_plinius) — a widely-referenced in-the-wild attacker whose posts inform Shen et al.-style corpora. <!-- needs-research: cite specific durable references (papers, curated corpora) that catalogue the wild-attacker output rather than transient social posts. -->
- [OWASP GenAI Security Project — hub](https://genai.owasp.org/) — umbrella for LLM01 (injection) and adjacent jailbreak-relevant guidance and community write-ups.

---

## Related standards this module plugs into

- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html)
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html)
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — the regulator-legibility target AIR-Bench 2024 (and therefore chapter 06) grounds its taxonomy in.
- [US Executive Order 14110 — Safe, Secure, and Trustworthy Development and Use of Artificial Intelligence](https://www.federalregister.gov/documents/2023/11/01/2023-24283/safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence) — the second regulator-legibility source AIR-Bench 2024 grounds in. <!-- needs-research: track successor US executive orders and any repeal / amendment status. -->
- [NIST AI 100-2 E2023 — Adversarial ML: A Taxonomy and Terminology of Attacks and Mitigations](https://csrc.nist.gov/pubs/ai/100/2/e2023/final) — the level-25 prerequisite's taxonomy; jailbreak labels cross-reference back to it.
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) — practitioner methodology that co-lives with this module's engineering depth.
- [Microsoft AI Red Team Guide](https://learn.microsoft.com/en-us/security/ai-red-team/) — operator-side red-team methodology complementary to the OWASP guide.

---

## Adjacent tracks in the AI Career Curriculum ecosystem

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — level-25 prerequisite. Owns the general LLM-security craft (garak, PyRIT, Promptfoo, NIST AI 100-2, OWASP ML Top 10). Chapter 09 codifies the interface.
- [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) — level-30 AI Engineering peer that owns the app-side trace format and judge scaffolds the JEH's judge composes with. <!-- needs-research: confirm the exact repository URL. -->
- [`model-evaluation-engineer-learning`](https://github.com/ai-engineering-curriculum/model-evaluation-engineer-learning) — level-30 sibling that supplies the statistical calibration methodology chapter 07 leans on. <!-- needs-research: confirm the exact repository URL. -->
- [`senior-agentic-ai-engineer-learning`](https://github.com/ai-engineering-curriculum/senior-agentic-ai-engineer-learning) — the agentic-AI peer whose architecture spec is the input for JEH target selection. <!-- needs-research: confirm the exact repository URL. -->
- [`security-learning`](https://github.com/ai-infra-curriculum/security-learning) and [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — level-35 platform-security peers that co-own the boundary controls a jailbreak-plus-tool-abuse finding routes to.
- [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) — level 50; consumes the release-gate policy from exercise 05 and the disclosure severity annotations from exercises 05–07.

---

## Upstream and downstream modules in this track

Read mod-101, mod-102, and mod-103 first (they are prerequisites for this module). The downstream modules consume the artefacts this module ships.

- **mod-101 (Frontier Safety Frameworks)** — the RSP / Preparedness / FSF grounding chapter 07's elicitation-gap metric explicitly cites. Position the JEH's outputs against the tier the target is deployed at.
- **mod-102 (Agent Threat Modelling)** — the ATMD whose prioritised (surface × persona) cells inform which harms are Tier A in exercise 05's coverage matrix.
- **mod-103 (Prompt-Injection Engineering)** — the PIEH boundary chapter 01 and chapter 09 codify. Injection is a principal attack; jailbreak is a policy attack. Findings often cross-tag.
- **mod-105 (Agent-Specific Attack-Surface Engineering)** — owns tool-abuse chains a jailbreak-unlocked capability rides.
- **mod-106 (Dangerous-Capability Evaluations)** — CBRN, cyber-uplift, autonomous-replication, R&D-uplift benchmarks. CyberSecEval 3's autonomous-offence subset is cited to mod-106.
- **mod-107 (Excessive-Agency Containment)** — sandboxing, egress policy, credential scoping that contains a jailbreak-plus-tool-abuse finding.
- **mod-108 (Frontier Guardrails and Monitors)** — trains the guardrails the JEH measures. Adaptive-attack survival (chapter 07) is the load-bearing metric at the interface.
- **mod-109 (Safety Cases and Structured Argumentation)** — consumes JEH outputs as evidence in safety cases.
- **mod-110 (Control and Deception Evaluation)** — adjacent; findings where the target *complies while deceiving* route here alongside this module.
- **mod-111 (Automated and Scaled Red-Teaming)** — industrialises the JEH. The stable interfaces this module exposes (attackers, judge, benchmarks, fixtures, coverage matrix) are what mod-111 orchestrates.
- **mod-112 (Safety Program and Disclosure)** — consumes high-severity findings for coordinated disclosure. Severity annotations from exercises 05–07 feed the routing.
