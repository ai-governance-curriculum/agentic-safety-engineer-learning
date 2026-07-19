# Resources for mod-108 — Frontier-Scale Guardrails and Safety-Monitor Engineering

This is the curated primary-source reading list for the module. Every chapter links to a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

---

## Primary sources — foundational papers

### Anthropic Constitutional Classifiers and adjacent work

- [Anthropic — Constitutional Classifiers: Defending against universal jailbreaks (Sharma et al., 2025)](https://www.anthropic.com/research/constitutional-classifiers) — the load-bearing methodology paper for chapter 04. <!-- needs-research: pin the exact arXiv identifier and publication date at authoring time. -->
- [Anthropic — Constitutional AI: Harmlessness from AI Feedback (Bai et al., 2022)](https://arxiv.org/abs/2212.08073) — the earlier constitutional-AI work that the classifiers methodology extends.
- [Anthropic — Many-shot Jailbreaking (2024)](https://www.anthropic.com/research/many-shot-jailbreaking) — the adversarial-attack class the survival-curve evaluation is measured against.
- [Anthropic — Challenges in red teaming AI systems (2024)](https://www.anthropic.com/news/challenges-in-red-teaming-ai-systems) — practitioner-facing discussion of red-team methodology at scale.

### Meta Llama Guard family

- [Meta AI — Llama Guard: LLM-based Input-Output Safeguard for Human-AI Conversations (Inan et al., 2023)](https://arxiv.org/abs/2312.06674) — the foundational paper.
- [Meta Llama Guard 2 model card](https://huggingface.co/meta-llama/Meta-Llama-Guard-2-8B) — version-specific taxonomy and metrics.
- [Meta Llama Guard 3 model card](https://huggingface.co/meta-llama/Llama-Guard-3-8B) — the current generation as of authoring. <!-- needs-research: pin the current released Llama Guard version at authoring time and any specialised variants (Prompt Guard, vision-modal Llama Guard). -->
- [Meta Llama Prompt Guard](https://huggingface.co/meta-llama/Prompt-Guard-86M) — a specialised classifier for prompt-injection detection. <!-- needs-research: confirm the current Prompt Guard version and its integration path. -->

### Google ShieldGemma

- [Google — ShieldGemma: Generative AI Content Moderation Based on Gemma (Zeng et al., 2024)](https://arxiv.org/abs/2407.21772) — the paper for the 2B / 9B / 27B family.
- [ShieldGemma model card on Hugging Face](https://huggingface.co/google/shieldgemma-9b) — the model-card link points at the 9B; the 2B and 27B variants have their own cards. <!-- needs-research: confirm current ShieldGemma release lineage and any successor Gemma-based classifier. -->

### OpenAI

- [OpenAI — Moderation API documentation](https://platform.openai.com/docs/guides/moderation) — the hosted endpoint, its category set, and its pricing envelope. <!-- needs-research: pin the current Moderation API model name (`omni-moderation-latest` at authoring time) and the category enumeration. -->

---

## Tooling — open-source guardrail frameworks

### NVIDIA NeMo Guardrails

- [NVIDIA NeMo Guardrails documentation](https://docs.nvidia.com/nemo/guardrails/latest/) — the current documentation. <!-- needs-research: pin the current version and any rail-taxonomy changes at authoring time. -->
- [NVIDIA NeMo Guardrails on GitHub](https://github.com/NVIDIA/NeMo-Guardrails) — source, sample flows, community configurations.
- [NVIDIA NeMo Guardrails hub](https://developer.nvidia.com/nemo-guardrails) — vendor landing page.

### Guardrails AI

- [Guardrails AI documentation](https://www.guardrailsai.com/docs) — RAIL specification, validators, integration patterns.
- [Guardrails AI Hub](https://hub.guardrailsai.com/) — the community validator catalogue.
- [Guardrails AI on GitHub](https://github.com/guardrails-ai/guardrails) — source and examples.

### Microsoft Presidio

- [Presidio documentation](https://microsoft.github.io/presidio/) — analyser / anonymiser split, recogniser catalogue.
- [Presidio on GitHub](https://github.com/microsoft/presidio) — source and current recognisers.

---

## Benchmarks — adversarial and hazard-category evaluation

- [MLCommons AILuminate benchmark](https://mlcommons.org/benchmarks/ailuminate/) — the industry hazard-category-and-benign benchmark. Chapter 06 uses it as an anchor. <!-- needs-research: pin the current AILuminate version and hazard-category enumeration at authoring time. -->
- [HarmBench (Mazeika et al., 2024)](https://arxiv.org/abs/2402.04249) — standardised automated red-team benchmark.
- [HarmBench on GitHub](https://github.com/centerforaisafety/HarmBench) — the benchmark harness and dataset.
- [JailbreakBench (Chao et al., 2024)](https://arxiv.org/abs/2404.01318) — standardised adversarial-prompt benchmark with a public leaderboard.
- [JailbreakBench website](https://jailbreakbench.github.io/) — leaderboard and dataset.
- [AdvBench (Zou et al., 2023)](https://arxiv.org/abs/2307.15043) — the harmful-behaviour prompt corpus from the GCG paper. Used as an adversarial-set anchor.

<!-- needs-research: confirm any additional MLCommons benchmark releases (AILuminate v2 or successor) and any published cross-benchmark comparisons at authoring time. -->

---

## Threat taxonomies and standards

- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/) — the taxonomy the FGAC's section-1 hazard-coverage matrix maps to. Cite specific entries version-pinned.
- [OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/) — the tool-misuse, repudiation, and cascading-failures entries.
- [OWASP GenAI — LLM and Generative AI Security Solutions Landscape](https://genai.owasp.org/resource/llm-and-generative-ai-security-solutions-landscape/) — vendor and open-source catalogue.
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/) — companion to mod-111's red-team discipline.
- [NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology](https://csrc.nist.gov/pubs/ai/100/2/e2025/final) — the attack-class terminology. <!-- needs-research: verify the final publication date and section numbering for the LLM-specific taxonomy. -->
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) — general reference; the `ai-risk-engineer` prerequisite covers it in depth.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-ML attack tactics; used for cross-reference against the FGAC hazard classes.

---

## Vendor references — commercial guardrail products

Author the vendor-eval matrix (chapter 05) against these. Every product feature claim is a `needs-research` mark you must resolve at authoring time.

- [Lakera Guard](https://www.lakera.ai/lakera-guard) — commercial guardrail with a prompt-injection focus. <!-- needs-research: confirm current product feature set, deployment models, pricing tiers, and any published third-party evaluations. -->
- [HiddenLayer AIDR](https://hiddenlayer.com/) — AI Detection and Response product. <!-- needs-research: confirm current AIDR scope, integration model, and detections against public benchmarks. -->
- [Protect AI Guardian](https://protectai.com/) — runtime protection product; part of a broader Protect AI product suite. <!-- needs-research: confirm current Protect AI product lineup and Guardian's scope. -->
- [Robust Intelligence](https://www.robustintelligence.com/) — AI Firewall product. <!-- needs-research: confirm current product features and any recent acquisitions or rebranding. -->
- [CalypsoAI](https://calypsoai.com/) — LLM security and content-moderation platform. <!-- needs-research: confirm current product name, features, and deployment posture. -->
- [HydroX AI](https://hydrox.ai/) — LLM security with responsible-AI compliance focus. <!-- needs-research: confirm current product features and enterprise integrations. -->

<!-- needs-research: cross-check the vendor list against the OWASP GenAI Security Solutions Landscape at authoring time; add or remove vendors to reflect the current market. -->

---

## Jailbreak and prompt-injection literature (used across chapters 02–04)

- [Zou et al. — Universal and Transferable Adversarial Attacks on Aligned Language Models (GCG, 2023)](https://arxiv.org/abs/2307.15043) — the foundational adversarial-suffix attack.
- [Chao et al. — Jailbreaking Black Box Large Language Models in Twenty Queries (PAIR, 2023)](https://arxiv.org/abs/2310.08419) — LLM-generated jailbreaks.
- [Mehrotra et al. — Tree of Attacks (TAP, 2023)](https://arxiv.org/abs/2312.02119) — tree-search adversarial-prompt generation.
- [Greshake et al. — Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection (2023)](https://arxiv.org/abs/2302.12173) — the indirect-prompt-injection reference.
- [Wei et al. — Jailbroken: How Does LLM Safety Training Fail? (2023)](https://arxiv.org/abs/2307.02483) — the taxonomy of jailbreak failure modes.
- [Perez et al. — Ignore Previous Prompt: Attack Techniques for Language Models (2022)](https://arxiv.org/abs/2211.09527) — early prompt-injection catalogue.

---

## Adjacent tracks in the AI Career Curriculum ecosystem

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — the required immediate prerequisite (level 25). Base-depth on NeMo Guardrails, Guardrails AI, Presidio, Llama Guard, ShieldGemma, OpenAI Moderation.
- [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) — application-layer evaluation-engineering plumbing (peer, level 30).
- [`model-evaluation-engineer-learning`](https://github.com/ml-engineering-curriculum/model-evaluation-engineer-learning) — statistical / calibration methodology used in the FP / FN report (peer, level 30).
- [`fine-tuning-engineer-learning`](https://github.com/ml-engineering-curriculum/fine-tuning-engineer-learning) — the peer role for chapter 03's fine-tune compute (peer, level 30).
- [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — platform-scale ML security (peer / next-up, level 35).
- [`ai-evaluation-engineer-learning`](https://github.com/ai-governance-curriculum/ai-evaluation-engineer-learning) — release-assurance / audit-trail methodology (peer / next-up, level 35).

<!-- needs-research: confirm the current names and URLs of each adjacent track; the AI Career Curriculum organisation names and repos may shift. -->

---

## Load-bearing frameworks the module composes with

- [Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp) — the frontier-lab framework the FGAC's residual-risk claim eventually cites.
- [OpenAI Preparedness Framework](https://openai.com/safety/preparedness/) — parallel framework.
- [Google DeepMind Frontier Safety Framework](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/) — parallel framework.
- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Articles 55, 56, 73 are the specific ones for the disclosure boundary in chapter 06.
