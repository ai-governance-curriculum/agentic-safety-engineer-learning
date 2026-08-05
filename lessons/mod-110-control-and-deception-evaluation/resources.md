# Resources — mod-110 (AI Control, Deception, and Adversarial-Alignment Evaluation)

Curated reading for the module. Every citation is version-pinned when it lands in an eval-run artefact; the mod-110 reproducibility posture requires it (chapter 02, chapter 09).

## Primary sources — the reference methodology papers

- **Meinke, Schoen, Scheurer, Balesni, Shah, Hobbhahn — "Frontier Models are Capable of In-Context Scheming"** (Apollo Research) — <https://arxiv.org/abs/2412.04984>. The reference for chapter 06 and for chapter 02's hidden-scratchpad probe class. Read in full before authoring a scheming-probe suite. <!-- needs-research: pin the specific revision (v1 / v2 / journal version) and any subsequent Apollo technical addenda when consumed in the eval-run artefact. -->
- **Greenblatt, Denison, Wright, Roger, MacDiarmid, Marks, Treutlein, Belrose, Scheurer, Rosenblatt, Petrini, Krasheninnikov, Chen, Marks, Rimsky, Sharma, Mikulik, Perez, Bowman, Bricken, Hubinger — "Alignment Faking in Large Language Models"** (Anthropic + Redwood) — <https://arxiv.org/abs/2412.14093>. The reference for chapter 03. Read in full before authoring an alignment-faking eval; the paper's paired-framing setup is the direct engineering template. <!-- needs-research: pin the paper's specific revision and any subsequent replication notes. -->
- **Hubinger, Denison, Mu, Lambert, Tong, MacDiarmid, Lanham, Ziegler, Maxwell, Cheng, Jermyn, Schiefer, Hatfield-Dodds, Kravec, Nguyen, Mikulik, Radhakrishnan, Xiao, Askell, Bowman, Perez — "Sleeper Agents: Training Deceptive LLMs that Persist Through Safety Training"** (Anthropic) — <https://arxiv.org/abs/2401.05566>. The reference for chapter 05. Read in full before authoring an organism specification.
- **Benton, Wagner, Christiansen, Anil, Perez, Srivastav, Durmus, Ganguli, Kravec, Shlegeris, Kaplan, Karnofsky, Hubinger, Grosse, Bowman, Duvenaud — "Sabotage Evaluations for Frontier Models"** (Anthropic) — <https://arxiv.org/abs/2410.21514>. The reference for chapter 07 and for chapter 04's sandbagging-under-oversight family. Read in full before authoring a sabotage-evaluation report.
- **Greenblatt, Shlegeris, Sachan, Roger — "AI Control: Improving Safety Despite Intentional Subversion"** (Redwood Research) — <https://arxiv.org/abs/2312.06942>. The reference for chapter 08. Read in full before authoring a control-eval protocol. The red-team-blue-team protocol, the untrusted-monitoring baseline, and the safety-usefulness Pareto framing are the direct engineering references.
- **Greenblatt, Roger, Krasheninnikov, Krueger — "Stress-testing capability elicitation with password-locked models"** — <https://arxiv.org/abs/2405.19550>. The reference for chapter 04's primitive 2 (password-locked-capability elicitation). <!-- needs-research: confirm the arXiv identifier and revision when consumed. -->

## Reference organisations — evaluation methodology publishers

- **Apollo Research** — <https://www.apolloresearch.ai/>. Reference publisher for chapter 02 (deception probe classes) and chapter 06 (in-context scheming). The Meinke et al. paper is the load-bearing chapter 06 reference. <!-- needs-research: pin the specific Apollo methodology write-ups (deception evaluations page, capability evaluations page, in-context-scheming technical report) when consumed in eval-run artefacts. -->
- **Anthropic — Research and safety publications** — <https://www.anthropic.com/research>. Reference publisher for chapters 03, 05, 07 (alignment faking, sleeper agents, sabotage). System cards / model cards are the reference format for the delta reports chapters 02–07 emit. <!-- needs-research: pin the specific Claude system-card revisions when cited (they carry adversarial-evaluation sections that anchor cross-model comparability). -->
- **Redwood Research — Publications** — <https://www.redwoodresearch.org/>. Reference publisher for chapter 08 (AI control) and co-author on chapter 03 (alignment faking). <!-- needs-research: pin subsequent Redwood publications on control-eval methodology, follow-up experiments, and open-source protocol implementations. -->
- **UK AI Safety Institute — Evaluations publications** — <https://www.aisi.gov.uk/>. Reference government body publishing pre-deployment control and adversarial-alignment evaluations. AISI's methodology publications and its pre-deployment / early-access evaluation reports anchor the external-evaluator posture the mod-109 safety case rebuts against. <!-- needs-research: pin the specific AISI publication IDs used in eval-run artefacts (pre-deployment evaluation methodology, control-adjacent methodology, scheming-adjacent methodology). -->
- **US AI Safety Institute (NIST)** — <https://www.nist.gov/aisi>. The US-side external-evaluator posture. <!-- needs-research: pin the specific US AISI methodology publications on adversarial-alignment evaluation. -->
- **METR — Task suite and autonomy evaluation methodology** — <https://metr.org/>. Reference publisher for public agentic-autonomy task suites; chapter 04 (sandbagging) uses METR tasks as capability probes on which sandbagging is measured. <!-- needs-research: pin the specific task-suite versions and sandbagging-relevant task cards when cited. -->

## Frontier-safety framework contexts — the disclosure surface

- **Anthropic — Responsible Scaling Policy** — <https://www.anthropic.com/rsp>. ASL tier framework. Chapter 09 pins the framework as the tier threshold mod-110 reports are compared against for the Anthropic disclosure context. <!-- needs-research: pin the current RSP revision and the specific ASL-tier thresholds that consume deception / scheming / sabotage findings. -->
- **OpenAI — Preparedness Framework** — <https://openai.com/safety/preparedness>. Preparedness scorecard structure; chapter 09 pins it as the OpenAI disclosure context. <!-- needs-research: pin the specific Preparedness Framework revision and the scorecard cells that consume adversarial-alignment findings. -->
- **Google DeepMind — Frontier Safety Framework** — <https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/>. Critical Capability Level thresholds. <!-- needs-research: pin the current FSF version. -->
- **EU AI Act — Articles 55, 56, 73** — <https://artificialintelligenceact.eu/>. Regulator-facing disclosure surface; chapter 09's downstream cite map into mod-112 lands here for EU-jurisdiction disclosure. <!-- needs-research: confirm the authoritative URL for the consolidated text; pin Article 55 / 56 GPAI provisions and Article 73 serious-incident-report language when cited. -->
- **EU GPAI Code of Practice — safety and security chapter** — <https://digital-strategy.ec.europa.eu/>. Co-regulatory framework alignment with mod-110 findings. <!-- needs-research: pin the finalised Code of Practice URL and the safety-and-security chapter specifically. -->
- **US Executive Order 14110 — "Safe, Secure, and Trustworthy AI"** — <https://www.whitehouse.gov/briefing-room/presidential-actions/2023/10/30/executive-order-on-the-safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence/>. US framework context. <!-- needs-research: verify the current authoritative URL and whether the EO remains in force under the current administration; note any successor or supplementary orders. -->

## Adjacent evaluation-methodology references

- **NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology** — <https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2025.pdf>. The reference taxonomy for backdoor / trojan / poisoning attacks in ML models; chapter 05 uses it to situate sleeper-agent methodology in the broader adversarial-ML literature. <!-- needs-research: pin the specific section IDs for backdoor / trojan / poisoning attacks in the current revision. -->
- **Anthropic — "Model Organisms of Misalignment"** (Anthropic Alignment Science blog and technical write-ups) — <https://www.anthropic.com/research>. The reference organism programme that chapters 05, 07, and 08 draw the model-organism concept from. <!-- needs-research: pin the specific model-organism programme publications when consumed. -->
- **METR — RE-Bench** — <https://metr.org/>. Reference for autonomy-relevant capability probes on which sandbagging (chapter 04) and control-eval (chapter 08) are measured. <!-- needs-research: pin the RE-Bench version and task cards used. -->
- **HarmBench / JailbreakBench / AIR-Bench 2024 / MLCommons AILuminate** — see mod-104 resources. Reference for the deception / refusal-robustness evaluation surfaces that chapter 02 and chapter 03 compose with.
- **WMDP (Weapons of Mass Destruction Proxy)** — <https://www.wmdp.ai/>. CBRN-uplift evidence context; chapter 04 sandbagging results on WMDP-relevant capabilities are directly consumed by mod-106 DCERs. <!-- needs-research: confirm current WMDP canonical URL and citation. -->
- **Anthropic — "Discovering Language Model Behaviors with Model-Written Evaluations"** (Perez et al.) — <https://arxiv.org/abs/2212.09251>. Reference for automated eval-generation methodology; chapter 02's probe-bank expansion methodology at scale composes with the mod-111 red-team methodology derived from this line. <!-- needs-research: verify arXiv identifier and revision. -->
- **Anthropic — "Constitutional AI: Harmlessness from AI Feedback"** (Bai et al.) — <https://arxiv.org/abs/2212.08073>. Reference for a safety-tuning-pass class chapter 05 measures persistence against. <!-- needs-research: verify arXiv identifier. -->

## Statistical-methodology and eval-platform references

- **`model-evaluation-engineer` methodology references** (peer role, level 30, ML Engineering family) — the statistical-methodology deferral mod-110 relies on for CI-widths, judge-agreement kappa, and elicitation-gap extrapolation. See mod-106 chapter 07 and mod-111 for the calibration-methodology handoff shape. <!-- needs-research: pin the org's canonical model-evaluation methodology docs when consumed. -->
- **Inspect Evals (UK AISI)** — <https://inspect.aisi.org.uk/>. Reference eval-harness platform for adversarial-alignment evaluations; chapter 09's `ai-eval-engineer` peer-role handoff often plugs into an Inspect-shaped harness. <!-- needs-research: pin the specific Inspect version and the mod-110-relevant task cards when consumed. -->
- **Open-source scheming-eval implementations** — the Apollo scheming-evals codebase and its downstream forks. <!-- needs-research: pin the canonical repository URL and revision when the operator's harness consumes it. -->

## Peer-role and boundary references (chapter 09)

- **`fine-tuning-engineer`** (peer, level 30, ML Engineering family) — organism construction, password-locked-capability construction, fine-tune-elicitation, safety-tuning intervention on findings. Chapter 09 codifies the handoff.
- **`ai-eval-engineer`** (peer, level 30, AI Engineering family) — eval-harness platform, judge-orchestration platform, artefact-storage layer, report emission.
- **`model-evaluation-engineer`** (peer, level 30, ML Engineering family) — statistical methodology, CI construction, judge-agreement kappa, elicitation-gap extrapolation.
- **`senior-agentic-ai-engineer`** (peer / next-up, level 40 or 50, AI Engineering family) — agent scaffold with instrumentation for chapter 06 (scheming) and chapter 08 (control-eval).
- **`ai-infra-security`** (peer, level 35, Security family) — interpretability tooling (chapter 05 white-box trigger discovery), runtime-isolation platform for agentic-scope evaluations, audit-log stream.
- **`senior-ai-governance-architect`** (level 50, AI Governance family) — control library, framework composition, cross-jurisdictional reconciliation, disclosure-threshold policy. Chapter 09 pins the boundary; mod-109 chapter 06 is the sibling reference.
- **Frontier-lab AI-safety-research-scientist ladder** (out of scope for this role) — Apollo, Anthropic Alignment, Redwood, DeepMind Alignment, OpenAI Alignment, UK / US AISIs. Owns the derivation of novel evaluation methodology; mod-110 productionises. Chapter 09 draws the boundary.

## Cross-module references (internal)

- **mod-101** — the frontier-safety-framework fluency that lets mod-110 reports be authored against the RSP / Preparedness / FSF language.
- **mod-102** — the threat-modelling docket whose deception / scheming / sabotage entries mod-110 evaluations bound.
- **mod-104** — jailbreak-engineering; distinct threat model, but shares the paired-framing discipline for judge design.
- **mod-106** — dangerous-capability eval; the DCER's elicitation-gap-accounting clause cites chapter 04's sandbagging report as the third layer.
- **mod-107** — excessive-agency containment; chapter 03, chapter 06, chapter 07 findings re-argue the EACC.
- **mod-108** — guardrails and monitors; consumes chapter 02 trace corpus, chapter 05 non-trigger baselines, chapter 06 trace patterns, chapter 07 attempt-vs-success delta as calibration data.
- **mod-109** — safety cases; consumes mod-110 reports as evidence for the trustworthiness / control / inability legs.
- **mod-111** — scaled red team; mod-110 evaluation families become coverage rows in the mod-111 matrix.
- **mod-112** — program and disclosure; findings above pre-registered thresholds route here for RSP / Preparedness / FSF / AISI / EU AI Act disclosure shaping.
