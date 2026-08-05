# Resources for mod-106 — Dangerous-Capability Evaluation Engineering

This is the curated primary-source reading list for the module. Every chapter cites a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

Benchmark scores, task-length curves, elicitation-shift numbers, system-card figures, and framework tier thresholds move with every model release, every framework revision, and every benchmark version bump. Any figure you drop into a DCER from these sources needs a re-verification pass — chapters 01–07 all flag this explicitly with `<!-- needs-research: ... -->` markers. Note the observed-on date on any citation you drop into a finding.

The mod-101 (Frontier Safety Frameworks), mod-103 (Prompt-Injection Engineering), mod-104 (Jailbreak Engineering), and mod-105 (Agent Attack Surface) `resources.md` files carry the framework-position, injection-specific, jailbreak-specific, and agent-attack-specific primaries this module builds on. Rather than duplicate those lists, this file focuses on the **dangerous-capability-specific** primaries cited across chapters 01–07: elicitation-protocol engineering, CBRN uplift, cyber-offense capability, autonomous-replication / long-horizon autonomy, AI-R&D uplift, persuasion / manipulation, self-exfiltration, and the DCER artefact.

---

## The frontier safety frameworks this module measures against (chapters 01, 06 — required before every exercise)

Every DCER is shaped against one of these frameworks' tiers. Re-read the tier-defining sections before authoring a pre-registration.

- [Anthropic — Responsible Scaling Policy (RSP)](https://www.anthropic.com/rsp) — the AI Safety Level (ASL) framework this module's CBRN and autonomy panels most commonly cite. Read the ASL-3 Bioweapons and ASL-3 Cyber sections in the current version. <!-- needs-research: confirm the current RSP version and the ASL-defining language for CBRN, cyber, and autonomy. -->
- [Anthropic — Capability Reports (RSP evaluation artefacts)](https://www.anthropic.com/news) — the format Anthropic uses to publish its RSP-tier evaluations. This is the closest published analogue to the DCER for RSP-shaped labs. Browse for the current Capability Report for the current Claude model. <!-- needs-research: confirm the current Capability Report location and format. -->
- [OpenAI — Preparedness Framework](https://openai.com/safety/) — the tiered-category (cybersecurity, CBRN, persuasion, autonomy) framework that the o-series and GPT-5 family evaluate against. Read the current version's category definitions and scorecard shape. <!-- needs-research: confirm the current Preparedness Framework version. -->
- [OpenAI — Safety Advisory Group and Preparedness scorecards](https://openai.com/safety/) — the published scorecard format the Preparedness Framework's evaluations produce.
- [Google DeepMind — Frontier Safety Framework (FSF)](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/) — the Critical Capability Level (CCL) framework that Gemini models evaluate against. Read the current version's CCL definitions across CBRN, cyber, autonomy, and ML-R&D. <!-- needs-research: confirm the current FSF version and CCL enumeration. -->
- [Frontier Model Forum — Preparedness Framework working papers](https://www.frontiermodelforum.org/) — the FMF's shared-methodology publications on tiered evaluation, bio-risk evaluation, cyber-offense evaluation, and autonomous-replication evaluation. <!-- needs-research: confirm the current FMF publication list and dates. -->
- [UK AI Safety Institute (AISI) — publications hub](https://www.aisi.gov.uk/) — pre-deployment evaluation reports across Claude, o-series, Gemini, and open-weights models. The AISI reports are the closest public-domain analogue to a DCER and are indispensable for methodology anchoring. <!-- needs-research: confirm the current AISI publication list. -->
- [US AI Safety Institute (NIST AISIC) — publications and consortium output](https://www.nist.gov/aisi) — the US counterpart. <!-- needs-research: confirm the current AISIC publication set. -->
- [EU AI Office — General-Purpose AI Code of Practice (safety chapter)](https://digital-strategy.ec.europa.eu/) — the DCER's regulatory-input target under EU AI Act Article 55. <!-- needs-research: confirm the current CoP safety-chapter draft and status. -->
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Article 55 (systemic-risk models) and Article 73 (serious-incident reporting) are the ones a DCER most often feeds.

Mod-101 chapters 02–04 are the on-ramp to these frameworks. Re-read there before authoring a pre-registration if you have not read the framework itself recently.

---

## Elicitation-protocol methodology (chapter 01 — required before exercise 01)

The methodology literature for closing the capability-elicitation gap.

- [METR — "Common elements of frontier AI safety policies" and related methodology posts](https://metr.org/) — METR's writings on elicitation methodology, task-length-in-hours framing, and the elicitation gap. Read the most recent METR autonomy report and the accompanying methodology posts before authoring any elicitation protocol. <!-- needs-research: confirm the current METR methodology publication list. -->
- [Anthropic — "Capability elicitation with reinforcement learning" and related methodology posts](https://www.anthropic.com/research) — Anthropic's published methodology work on capability elicitation with RL, fine-tune elicitation, and prompt-based elicitation. <!-- needs-research: confirm current publication URLs. -->
- [DeepMind — "Evaluating Frontier Models for Dangerous Capabilities" (Phuong et al., 2024)](https://arxiv.org/abs/2403.13793) — the DeepMind methodology paper for dangerous-capability evaluations across persuasion, cyber, and autonomy panels. Foundational reading for chapter 01 and the panels chapters build on. <!-- needs-research: confirm the arXiv ID and any successor publication. -->
- [Apollo Research — evaluation methodology publications](https://www.apollo.ai/) — Apollo's dangerous-capability-evaluation methodology, in particular around scheming / situational-awareness (which the mod-110 boundary picks up on) and elicitation-protocol design. <!-- needs-research: confirm current Apollo publication list. -->
- [Inspect (UK AISI evaluation framework)](https://inspect.aisi.org.uk/) — the AISI evaluation harness used across published AISI pre-deployment reports. Chapter 01's elicitation-protocol pre-registration shape composes with the Inspect run configuration.
- [Kaplan, Jared et al. — "Scaling Laws for Neural Language Models" (2020)](https://arxiv.org/abs/2001.08361) — background on scaling behaviour that informs the doubling-time framing in chapter 04.

<!-- needs-research: track successor elicitation-methodology publications (fine-tune-elicitation with adversarial data, best-of-N pass@k calibration, scaffold-parity anchoring); this row moves fast. -->

---

## CBRN uplift (chapter 02 — required before exercise 02)

The CBRN-specific benchmark, threat-model, and evaluation-methodology literature.

### The WMDP benchmark

- [Li, Nathaniel et al. — "The WMDP Benchmark: Measuring and Reducing Malicious Use With Unlearning" (ICML 2024)](https://arxiv.org/abs/2403.03218) — the Weapons of Mass Destruction Proxy benchmark: ~3,600 multiple-choice questions across biology, chemistry, and cybersecurity, designed as proxies for hazardous knowledge without releasing hazardous content. Required reading before chapter 02 and exercise 02. <!-- needs-research: confirm the current WMDP paper version, question count per subset, and any post-2024 companion releases. -->
- [WMDP — project site and dataset](https://www.wmdp.ai/) — the benchmark's own release site, safety documentation, and licence terms.

### Bio threat model

- [Frontier Model Forum — biological risk working papers](https://www.frontiermodelforum.org/) — the FMF's published bio-risk evaluation guidance. Required reading before chapter 02 for the threat-model anchoring. <!-- needs-research: confirm the current FMF bio-risk publication list, dates, and titles. -->
- [RAND — "The Operational Risks of AI in Large-Scale Biological Attacks" (2024)](https://www.rand.org/) — a RAND analysis of AI's marginal uplift in bioweapons planning. Read for the operational-risk framing that grounds the paired human-baseline uplift trial design. <!-- needs-research: confirm the current RAND publication list on AI + bio-risk. -->
- [Sandbrink, Jonas B. — "Artificial intelligence and biological misuse: Differentiating risks of language models and biological design tools" (2023)](https://arxiv.org/abs/2306.13952) — the differentiation between LLM-mediated uplift and biological-design-tool uplift. Useful framing for the sub-panel design in chapter 02.
- [Mouton, Christopher A. et al. — "The Operational Risks of AI in Large-Scale Biological Attacks" (RAND, 2024)](https://www.rand.org/pubs/research_reports/RRA2977-2.html) — companion RAND work. <!-- needs-research: confirm citation. -->

### System-card CBRN sections and safety-eval methodology

- [Anthropic — Claude system cards](https://www.anthropic.com/news) — the recent Claude system cards' CBRN-uplift / ASL-3 sections and the accompanying Capability Reports. Read the most recent version's bio-uplift methodology description before authoring your CBRN panel. <!-- needs-research: confirm the current Claude system-card and Capability Report URLs. -->
- [OpenAI — o-series system cards](https://openai.com/safety/) — the o1, o3, and successor system cards' CBRN / preparedness sections. The paired human-baseline uplift-trial methodology and the expert-grader engagement patterns are documented there. <!-- needs-research: confirm current o-series system-card publications. -->
- [Google DeepMind — Gemini technical / system-card reports](https://deepmind.google/) — the Gemini reports' biosecurity CCL evaluations. <!-- needs-research: confirm current Gemini reports. -->

### Public-health and export-control coordination

- [World Health Organization — Laboratory Biosafety Manual](https://www.who.int/publications/i/item/9789240011311) — biosafety-level definitions and the standard the biosecurity-grader engagement borrows from.
- [WHO — Guidance framework for testing genetically modified mosquitoes (or equivalent dual-use guidance)](https://www.who.int/) — dual-use research-of-concern framing.
- [International Atomic Energy Agency — Nuclear Security Series](https://www.iaea.org/publications) — the IAEA's published guidance on nuclear-security incidents, which the CBRN-N sub-panel coordinates through.
- [Australia Group — chemicals and biological agents lists](https://www.dfat.gov.au/publications/minisite/theaustraliagroupnet/site/en/index.html) — the export-control list that the CBRN-chem sub-panel's grader engagement often references.
- [OPCW — Chemical Weapons Convention text and technical documents](https://www.opcw.org/) — the reference the CBRN-chem grader engagement leans on.
- [Biological Weapons Convention text](https://disarmament.unoda.org/biological-weapons/) — the international-instrument framing the CBRN-bio panel operates under.

<!-- needs-research: track successor bio-uplift RCT publications; the paired-baseline literature is a fast-moving front. -->

---

## Cyber-offense capability (chapter 03 — required before exercise 03)

The cyber-specific benchmark, threat-model, and evaluation-methodology literature.

### CyberSecEval and adjacent benchmarks

- [Bhatt, Manish et al. — "CyberSecEval 2: A Wide-Ranging Cybersecurity Evaluation Suite for Large Language Models" (Meta, 2024)](https://arxiv.org/abs/2404.13161) — the second-generation Meta cyber-eval suite covering vulnerability exploitation, prompt-injection risk, and autonomous cyber-offense. Required reading before chapter 03 and exercise 03. <!-- needs-research: confirm current CyberSecEval 2 URL and subset list. -->
- [CyberSecEval — reference implementation](https://github.com/meta-llama/PurpleLlama/tree/main/CybersecurityBenchmarks) — the code repository.
- [Wan, Shengye et al. — "CyberSecEval 3" (Meta, 2024)](https://arxiv.org/abs/2408.01605) — the third-generation Meta cyber-eval suite covering autonomous cyber-attack, exploit generation, spear-phishing generation, and defensive-uplift measurements. <!-- needs-research: confirm current CyberSecEval 3 URL, subset list, and any successor release. -->
- [Zhu, Kexun et al. — "Cybench: A Framework for Evaluating Cybersecurity Capabilities and Risks of Language Models" (2024)](https://arxiv.org/abs/2408.08926) — an LLM-CTF task suite with per-task first-solve-time human-baseline annotations. <!-- needs-research: confirm current Cybench release URL and task count. -->
- [Yang, John et al. — "InterCode: Standardizing and Benchmarking Interactive Coding with Execution Feedback" (NeurIPS 2023)](https://arxiv.org/abs/2306.14898) — the InterCode framework, which includes an InterCode-CTF task set for autonomous-cyber-offense evaluation.
- [Deng, Gelei et al. — "PentestGPT" (2023)](https://arxiv.org/abs/2308.06782) — an early published penetration-testing LLM system relevant to the autonomy-tier framing.
- [DARPA — AI Cyber Challenge (AIxCC)](https://aicyberchallenge.com/) — the DARPA competition on ML-driven vulnerability discovery and patching. The published results anchor the state of the art for autonomous cyber-offense against real codebases and the corresponding defensive-uplift baseline. <!-- needs-research: confirm current AIxCC publication status and final-report format. -->

### MITRE ATT&CK and ATLAS

- [MITRE ATT&CK — enterprise matrix](https://attack.mitre.org/matrices/enterprise/) — the tactic and technique taxonomy for annotating cyber-panel autonomy tasks. Version-pin to the release you evaluate against. <!-- needs-research: confirm the current ATT&CK version. -->
- [MITRE ATT&CK — techniques catalogue](https://attack.mitre.org/techniques/enterprise/) — the technique reference.
- [MITRE ATLAS](https://atlas.mitre.org/) — the ML-adjacent adversarial-tactics catalog for cases where the cyber-offense task involves ML-specific offensive operations. <!-- needs-research: confirm current ATLAS version. -->

### CVE and exploit-development infrastructure

- [MITRE CVE](https://cve.mitre.org/) and [NIST NVD](https://nvd.nist.gov/) — the vulnerability catalogues the cyber panel's exploit-writing tasks pin against.
- [ExploitDB](https://www.exploit-db.com/) — the reference exploit archive for reproduce-public-exploit tasks.

### System-card cyber sections

- [Anthropic — Claude system cards' cyber sections](https://www.anthropic.com/news) — recent Claude cyber-panel methodology.
- [OpenAI — o-series and GPT-5 system cards' cyber sections](https://openai.com/safety/) — the cyber-tier Preparedness scorecards and the autonomy-tier reporting shape.
- [Google DeepMind — Gemini technical reports' cyber sections](https://deepmind.google/) — the cyber-CCL evaluation methodology.

<!-- needs-research: track successor autonomous-cyber-attack benchmarks (Cybench extensions, autonomous-red-team benchmarks, and any FMF-shared cyber-eval methodology publications). -->

---

## Autonomy, autonomous replication, and AI-R&D uplift (chapter 04 — required before exercises 04 and 05)

The long-horizon autonomy, autonomous-replication, and AI-R&D-uplift literature.

### METR public task suite and horizon methodology

- [METR — publications hub](https://metr.org/publications/) — METR's rolling autonomy-methodology work. The load-bearing artefact is the task-length-in-human-hours doubling curve. Read the most recent METR autonomy report before authoring an autonomy panel. <!-- needs-research: confirm the current METR autonomy report URL, doubling-time claim, and task suite version. -->
- [Kinniment, Megan et al. — "Evaluating Language-Model Agents on Realistic Autonomous Tasks" (METR, 2023)](https://arxiv.org/abs/2312.11671) — the initial METR autonomous-task-evaluation paper; foundational reading for chapter 04 and exercise 04. <!-- needs-research: confirm arXiv ID and successor publication. -->
- [METR — public task suite (GitHub / release)](https://github.com/METR/) — METR's public task releases and access documentation. <!-- needs-research: confirm current public-task-suite access URL. -->

### RE-Bench (AI-R&D uplift)

- [Wijk, Hjalmar et al. — "RE-Bench: Evaluating frontier AI R&D capabilities of language model agents against human experts" (METR, 2024)](https://arxiv.org/abs/2411.15114) — the RE-Bench paper: seven expert-designed AI-R&D tasks at 2-hour and 8-hour cutoffs, paired against human ML experts. Required reading before chapter 04's RE-Bench section and before exercise 05. <!-- needs-research: confirm the current RE-Bench release URL and task list. -->
- [RE-Bench — reference implementation](https://github.com/METR/RE-Bench) — the code and task infrastructure. <!-- needs-research: confirm the current repository URL. -->

### SWE-bench Verified (software-engineering autonomy)

- [Jimenez, Carlos E. et al. — "SWE-bench: Can Language Models Resolve Real-World Github Issues?" (ICLR 2024)](https://arxiv.org/abs/2310.06770) — the original SWE-bench paper.
- [OpenAI — "Introducing SWE-bench Verified" (2024)](https://openai.com/index/introducing-swe-bench-verified/) — the human-validated 500-task subset of SWE-bench. Required reading before chapter 04's SWE-bench section and before exercise 04. <!-- needs-research: confirm the current SWE-bench Verified release URL. -->
- [SWE-bench — reference infrastructure](https://github.com/princeton-nlp/SWE-bench) — the code and the SWE-agent scaffold.
- [Yang, John et al. — "SWE-agent: Agent-Computer Interfaces Enable Automated Software Engineering" (2024)](https://arxiv.org/abs/2405.15793) — the SWE-agent scaffold that the SWE-bench Verified panel commonly runs under. Useful anchor for the scaffold-parity claim.

### Agent scaffolds worth pinning

- [OpenHands (formerly OpenDevin)](https://github.com/All-Hands-AI/OpenHands) — an open-source autonomous-software-engineering scaffold; comparator against SWE-agent.
- [Aider](https://github.com/paul-gauthier/aider) — an AI pair-programming scaffold; comparator against SWE-agent for SWE-bench Verified runs.
- [Devin (Cognition Labs)](https://www.cognition.ai/) — the closed-source autonomous-software-engineering system; useful as a scaffold-parity comparator for published SWE-bench Verified numbers. <!-- needs-research: confirm current publications. -->

### Autonomous replication and adaptation

- [Frontier Model Forum — autonomous replication and adaptation working papers](https://www.frontiermodelforum.org/) — the FMF's shared-methodology work on the autonomous-replication threat class. <!-- needs-research: confirm current FMF autonomy publications. -->
- [Anthropic — RSP autonomy-tier language and Capability Report autonomy sections](https://www.anthropic.com/rsp) — the RSP autonomy-tier definitions and the accompanying evaluation methodology.
- [DeepMind — FSF autonomy CCL sections](https://deepmind.google/) — the FSF autonomy and ML-R&D CCLs.
- [OpenAI — Preparedness Framework autonomy category](https://openai.com/safety/) — the Preparedness Framework's autonomy tracked-category definitions.

### Adjacent autonomy benchmarks

- [Liu, Xiao et al. — "AgentBench: Evaluating LLMs as Agents" (ICLR 2024)](https://arxiv.org/abs/2308.03688) — a general agent-capability benchmark chapter 04 cites alongside METR / RE-Bench / SWE-bench Verified.
- [Qin, Yujia et al. — "ToolLLM: Facilitating Large Language Models to Master 16000+ Real-world APIs" (ICLR 2024)](https://arxiv.org/abs/2307.16789) — a tool-use benchmark useful as a tool-use-elicitation anchor.
- [Chan, Alan et al. — "Visibility into AI Agents" (2024)](https://arxiv.org/abs/2401.13138) — background on autonomy-tier framing and agent-visibility that informs the autonomy-panel design.

<!-- needs-research: track successor autonomy benchmarks (SWE-bench Live, Multi-SWE-bench, RE-Bench extensions, AISI-specific task suites) as they are released. -->

---

## Persuasion, manipulation, and self-exfiltration (chapter 05 — required before exercise 06)

The persuasion / manipulation and self-exfiltration literature.

### Persuasion and manipulation

- [Salvi, Francesco et al. — "On the Conversational Persuasiveness of Large Language Models: A Randomized Controlled Trial" (2024)](https://arxiv.org/abs/2403.14380) — the ChangeMyView-linked debate-persuasion RCT; foundational reading for chapter 05's long-format-trial anchoring. <!-- needs-research: confirm citation and any successor publication. -->
- [Costello, Thomas H. et al. — "Durably reducing conspiracy beliefs through dialogues with AI" (Science, 2024)](https://www.science.org/doi/10.1126/science.adq1814) — evidence on the durability of AI-driven belief change; relevant when the panel measures durability rather than single-turn success. <!-- needs-research: confirm citation. -->
- [Bai, Hui et al. — "Artificial Intelligence Can Persuade Humans on Political Issues" (2023)](https://osf.io/stakv) — companion evidence on AI's persuasive capacity on political topics. <!-- needs-research: confirm citation. -->
- [Durmus, Esin et al. — "Measuring the Persuasiveness of Language Models" (Anthropic, 2024)](https://www.anthropic.com/research/measuring-model-persuasiveness) — Anthropic's methodology for measuring persuasion in a controlled setting; relevant for the paired-baseline design.
- [Matz, S. C. et al. — "The potential of generative AI for personalized persuasion at scale" (Nature Scientific Reports, 2024)](https://www.nature.com/articles/s41598-024-53755-0) — evidence on personalisation-mediated persuasion effectiveness.

### System-card persuasion evaluations (MakeMePay / MakeMeSay / charge-of-mind)

- [OpenAI — o1 and o3 system cards](https://openai.com/safety/) — the MakeMePay and MakeMeSay methodology and results. <!-- needs-research: confirm current o-series system cards and their persuasion / self-exfil section names. -->
- [Anthropic — Claude system cards](https://www.anthropic.com/news) — recent Claude system cards' persuasion evaluation sections (charge-of-mind, argument-quality, and long-format designs). <!-- needs-research: confirm current Claude persuasion evaluation designs. -->
- [Google DeepMind — Gemini technical reports' persuasion sections](https://deepmind.google/) — Gemini's persuasion / harm-of-manipulation methodology. <!-- needs-research: confirm current Gemini reports. -->

### Self-exfiltration and adversarial-alignment-adjacent evaluations

- [OpenAI — o1 system card's self-exfiltration section](https://openai.com/safety/) — the o1 system card's autonomous-behaviour-under-goal-pressure scenarios and results. <!-- needs-research: confirm current o-series self-exfil methodology. -->
- [Anthropic — Claude system cards' self-exfiltration and agent-behaviour sections](https://www.anthropic.com/news) — the recent Claude reports' self-exfiltration probes. <!-- needs-research: confirm current publications. -->
- [Meinke, Alexander et al. (Apollo Research) — "Frontier Models are Capable of In-context Scheming" (2024)](https://arxiv.org/abs/2412.04984) — the Apollo Research scheming-behaviour evaluations; the self-exfiltration panel's propensity measurements cite this class. <!-- needs-research: confirm citation and successor publications. -->
- [Greenblatt, Ryan et al. (Anthropic + Redwood Research) — "Alignment faking in large language models" (2024)](https://www.anthropic.com/research/alignment-faking) — the alignment-faking evaluation methodology; foundational for the mod-110 dependency the self-exfil propensity claim cites. <!-- needs-research: confirm current Anthropic publication URL. -->
- [Hubinger, Evan et al. — "Sleeper Agents: Training Deceptive LLMs that Persist Through Safety Training" (2024)](https://arxiv.org/abs/2401.05566) — the sleeper-agent paper; another anchor for the mod-110 control-evaluation companion.

### Autonomous-replication / self-exfiltration threat modelling

- [Kinniment, Megan et al. — METR autonomous-tasks paper (2023)](https://arxiv.org/abs/2312.11671) — includes autonomous-replication-adjacent task probing; the capability composition for chapter 05's self-exfil panel cites this.
- [FMF autonomous-replication working papers](https://www.frontiermodelforum.org/) — the shared threat-model articulation for autonomous replication. <!-- needs-research: confirm current FMF publications. -->

<!-- needs-research: track successor persuasion RCT publications (longitudinal follow-ups, personalisation-effect designs) and self-exfil / scheming evaluations (Apollo, METR, Redwood, and lab-internal work) as this literature matures. -->

---

## Statistical calibration and judge methodology (chapter 07 — deferral to `model-evaluation-engineer`)

The methodology the DCER cites and the peer role derives.

- [Chen, Mark et al. — "Evaluating Large Language Models Trained on Code" (Codex paper, 2021)](https://arxiv.org/abs/2107.03374) — the pass@k / best-of-N estimator commonly used across capability panels. Section 2 defines the estimator; the DCER's CI methodology chain starts here.
- [Chatzi, Ivi et al. — "Estimating the Hallucination Rate of Generative AI" (2024)](https://arxiv.org/abs/2406.07457) — CI methodology for LLM-judge outputs, relevant to the judge-agreement chain.
- [Zheng, Lianmin et al. — "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena" (2023)](https://arxiv.org/abs/2306.05685) — the LLM-as-judge methodology background.
- [Souly, Alex et al. — "A StrongREJECT for Empty Jailbreaks" (2024)](https://arxiv.org/abs/2402.10260) — the StrongREJECT judge methodology (mod-104 chapter 07 owns this reference); the DCER's chain-decomposed judges compose with it.
- [Item Response Theory — Baker, F. B. — "The Basics of Item Response Theory" (ERIC, 2001)](https://eric.ed.gov/?id=ED458219) — background on IRT, which the peer role uses for item-difficulty accounting.
- [Adaptive Bayesian sequential-experiment design — Kirk, Diederik (ed.) — introductory references](https://) — the peer role's methodology for sample-size planning in adaptive settings. <!-- needs-research: pick a specific accessible primer that matches the peer role's derivation approach. -->

The `model-evaluation-engineer` learning track owns the derivations end-to-end. The DCER cites the peer artefact by ID and reports the CIs; the derivation methodology defers.

---

## Evaluation harness and tooling worth knowing

The tools that run this module's panels in practice.

- [Inspect (UK AISI)](https://inspect.aisi.org.uk/) — the AISI evaluation framework, used across published AISI pre-deployment reports and a common harness for METR-derived task suites, WMDP runs, and CyberSecEval subsets.
- [OpenAI Evals](https://github.com/openai/evals) — OpenAI's evals framework, referenced across the o-series system cards.
- [Anthropic — Evaluation framework and Claude Code](https://docs.anthropic.com/) — the Anthropic-side evaluation infrastructure.
- [Promptfoo](https://www.promptfoo.dev/) — CI-integratable evaluation runner useful for regression harness plumbing.
- [Braintrust / LangSmith / Helicone](https://www.braintrust.dev/) — application-layer eval-plumbing platforms; the level-30 `ai-eval-engineer` peer role owns this layer.
- [garak (NVIDIA)](https://github.com/NVIDIA/garak) — LLM vulnerability scanner, useful as the general adversarial-ML complementary tool.
- [PyRIT (Microsoft)](https://github.com/Azure/PyRIT) — attacker-loop scaffolding referenced from mod-104 and useful in the elicitation-axes sweep.

---

## OWASP, MITRE ATLAS, NIST — the standards overlay

The standards a DCER cross-references when the panel's finding routes into the operator's compliance framework.

- [OWASP Top 10 for LLM Applications (2025)](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — chapters 02 and 03 cite **LLM01** (prompt injection, relevant to elicitation-axis payload discipline), **LLM04** (data and model poisoning), **LLM06** (excessive agency, relevant to self-exfil scaffolding), and **LLM09** (misinformation, relevant to persuasion). <!-- needs-research: confirm the current LLM Top 10 IDs and titles against the latest published edition. -->
- [OWASP GenAI Security Project — hub](https://genai.owasp.org/) — the umbrella for the LLM Top 10 and the OWASP Agentic AI documents.
- [MITRE ATT&CK](https://attack.mitre.org/) — the tactic / technique taxonomy for cyber-panel annotation (repeated from chapter 03).
- [MITRE ATLAS](https://atlas.mitre.org/) — the ML-adjacent adversarial-tactics catalog (repeated from chapter 03).
- [NIST AI 100-1 — AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) — the general AI-RMF that the DCER's evidence composes with in an operator's risk-management artefacts.
- [NIST AI 100-2 E2023 — Adversarial ML: A Taxonomy and Terminology of Attacks and Mitigations](https://csrc.nist.gov/pubs/ai/100/2/e2023/final) — the level-25 prerequisite's taxonomy; DCER findings cross-reference the poisoning / evasion terminology.
- [NIST AI 600-1 — Generative AI Profile of the AI-RMF](https://airc.nist.gov/AI_RMF_Knowledge_Base/Roadmap/AI_600-1) — the GenAI profile that CBRN, cyber, and autonomy DCER rows commonly cross-reference.
- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html) — the AIMS standard the DCER-shaped evidence feeds.
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html) — the risk-management guidance.
- [ISO/IEC 42005:2025 — AI system impact assessment](https://www.iso.org/standard/44545.html) — impact-assessment framing relevant to Article 55 regulator submissions.

---

## Vendor safety and policy documentation

The current-version publications that the DCER measures against.

- [Anthropic — Claude system cards and safety announcements](https://www.anthropic.com/news) — the current model's system card and Capability Report.
- [Anthropic — Usage Policies](https://www.anthropic.com/legal/aup) — the policy chapter 05 cites for refusal-classifier expectations.
- [OpenAI — Safety publications hub](https://openai.com/safety/) — the o-series and GPT-5 family system cards and Preparedness scorecards.
- [OpenAI — Usage Policies](https://openai.com/policies/usage-policies/) — the corresponding policy reference.
- [Google DeepMind — safety publications and technical reports](https://deepmind.google/discover/blog/) — Gemini system-card and FSF evaluation reports.
- [Google — Generative AI Prohibited Use Policy](https://policies.google.com/terms/generative-ai/use-policy)
- [Microsoft — Responsible AI Standard](https://www.microsoft.com/en-us/ai/responsible-ai) — for Bing / Copilot family safety-eval documentation.
- [xAI — safety and model documentation](https://x.ai/) — as/if xAI publishes system-card-shaped documentation for Grok-family releases. <!-- needs-research: confirm current xAI safety publications. -->

---

## Practitioner venues that publish dangerous-capability evaluations

Ongoing sources for the panel-authoring evidence base and comparative-results anchoring.

- [UK AISI — publications](https://www.aisi.gov.uk/) — the pre-deployment evaluation write-ups; the closest public-domain analogue to a DCER.
- [US AISI (NIST AISIC) — publications](https://www.nist.gov/aisi) — the US counterpart.
- [METR — publications hub](https://metr.org/publications/) — autonomy and elicitation-methodology publications.
- [Apollo Research](https://www.apollo.ai/) — scheming, sandbagging, and self-exfiltration methodology.
- [Redwood Research](https://www.redwoodresearch.org/) — AI control research; the mod-110 dependency's methodology anchor.
- [Frontier Model Forum](https://www.frontiermodelforum.org/) — shared-methodology working papers across CBRN, cyber, and autonomy.
- [MLCommons AI Safety working group](https://mlcommons.org/working-groups/ai-safety/) — the AI-Safety benchmarking-consortium releases that overlap with capability-panel measurement.

---

## Related standards this module plugs into

Same list as mod-102 / mod-103 / mod-104 / mod-105 for continuity; keep for cross-reference when a DCER row's evidence routes into the operator's compliance framework.

- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html)
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html)
- [ISO/IEC 42005:2025 — AI system impact assessment](https://www.iso.org/standard/44545.html)
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Article 55 (systemic-risk models) and Article 73 (serious-incident reporting).
- [EU AI Office — GPAI Code of Practice](https://digital-strategy.ec.europa.eu/) — the safety-chapter's evidence-submission expectations.
- [Microsoft AI Red Team Guide](https://learn.microsoft.com/en-us/security/ai-red-team/) — operator-side red-team methodology complementary to the OWASP GenAI Red Teaming Guide.
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) — practitioner methodology chapters 03 and 05 align with.

---

## Adjacent tracks in the AI Career Curriculum ecosystem

The peer-role handoffs chapter 07 codifies.

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — level-25 prerequisite. Owns the general LLM-security craft (garak, PyRIT, Promptfoo, ART, NIST AI 100-2, OWASP LLM Top 10, STRIDE / LINDDUN). Not duplicated here.
- [`model-evaluation-engineer-learning`](https://github.com/ai-engineering-curriculum/model-evaluation-engineer-learning) — level-30 sibling in the ML Engineering family that owns statistical calibration methodology (best-of-N CI, IRT ability estimation, sample-size planning, judge–human agreement CI). Chapter 07 boundary 1 routes to it. <!-- needs-research: confirm the exact repository URL. -->
- [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) — level-30 sibling in the AI Engineering family that owns application-layer eval plumbing (trace collection, judge orchestration, Inspect / Braintrust / LangSmith wiring). The chapter 01 boundary defers plumbing here. <!-- needs-research: confirm the exact repository URL. -->
- [`fine-tuning-engineer-learning`](https://github.com/ai-engineering-curriculum/fine-tuning-engineer-learning) — level-30 sibling that implements the fine-tune elicitation. The DCER's Section 9 deferral names this peer for the fine-tune-elicitation implementation. <!-- needs-research: confirm the exact repository URL. -->
- [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — level-35 platform-security peer that owns tool-runtime hardening, isolation environments for A3 drills, and sandbox construction. Chapter 07 boundary defers here for the isolation-environment engineering.
- [`senior-agentic-ai-engineer-learning`](https://github.com/ai-engineering-curriculum/senior-agentic-ai-engineer-learning) — level-40 agentic-AI peer whose architecture patterns some elicitation axes attack. Not the primary peer for this module (mod-105 handles that boundary) but relevant for the tool-use-elicitation surface. <!-- needs-research: confirm the exact repository URL. -->
- [`ai-evaluation-engineer-learning`](https://github.com/ai-governance-curriculum/ai-evaluation-engineer-learning) — level-35 AI Governance sibling that owns release-assurance packaging and audit-trail wiring. Consumes the DCER as an input. <!-- needs-research: confirm the exact repository URL. -->
- [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) — level 50; consumes the DCER's mitigation-engagement decisions for the cross-jurisdiction control catalog.
- [`head-of-ai-governance-learning`](https://github.com/ai-governance-curriculum/head-of-ai-governance-learning) — level 60; consumes the DCER for board reporting and regulator engagement.

---

## Upstream and downstream modules in this track

Read mod-101 through mod-105 first (they are prerequisites for this module). The downstream modules consume the artefacts this module ships.

- **mod-101 (Frontier Safety Frameworks and Position)** — the RSP / Preparedness / FSF tier definitions this module measures against; the on-ramp to the tier language.
- **mod-102 (Agent Threat Modelling)** — the ATMD threat models that anchor the CBRN / cyber / autonomy / persuasion panel threat-model sections.
- **mod-103 (Prompt-Injection Engineering)** — supplies injection primitives that the elicitation-axes sweep uses when the target model must be pushed past a refusal.
- **mod-104 (Jailbreak Engineering)** — supplies jailbreak techniques used in best-of-N elicitation and fine-tune elicitation on the capability head.
- **mod-105 (Agent Attack Surface Engineering)** — supplies the tool-use-elicitation surface; chain findings from mod-105 can serve as elicitation-axis inputs for tool-scaffolded capability panels.
- **mod-107 (Excessive-Agency Containment)** — engineers the isolation environments the autonomous-replication drills and A3 cyber runs sit inside. The deployed-stack ceiling (DCER Section 6) reflects mod-107's containment.
- **mod-108 (Frontier Guardrails and Monitors)** — trains the classifier guards and monitors the DCER's deployed-stack ceiling includes; adaptive-attack survival curves from mod-108 feed the panel's residual-gap accounting.
- **mod-109 (Safety Cases and Structured Argumentation)** — the DCER's *inability argument* evidence is a leg of the GSN safety case mod-109 authors.
- **mod-110 (Control and Deception Evaluation)** — the peer companion for expressed-inability trustworthiness; the DCER's residual-gap accounting cites mod-110 evaluations (sandbagging, alignment-faking, situational-awareness, sleeper-agent, sabotage). Chapter 07 boundary 3 formalises the cite.
- **mod-111 (Automated and Scaled Red-Teaming)** — industrialises the elicitation-axes sweeps; the DCER cites mod-111-run scaled-attacker artefacts where they contributed to the best-of-N or fine-tune elicitation.
- **mod-112 (Safety Program and Disclosure)** — governs external disclosure of DCER findings; the DCER Section 8 (rollback triggers) and Appendix D (EU AI Act Article 55 / GPAI CoP input) reference mod-112 workflow IDs.
