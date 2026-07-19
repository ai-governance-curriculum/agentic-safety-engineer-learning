# Resources for mod-111-automated-and-scaled-red-teaming (Automated and Scaled Red-Teaming)

Curated reading and tooling. Every entry is a primary source (documentation, peer-reviewed paper, or official policy document) — no derivative blog posts. Version-pin these when they are cited in a Coverage Matrix Contract or a replay bundle.

## Frameworks

### UK AI Safety Institute — Inspect

- **[Inspect documentation](https://inspect.aisi.org.uk/)** — the framework's solver / scorer / task model, the log-store, and the sandbox integration. The reference for chapter 02's spine. <!-- needs-research: confirm the current canonical documentation URL. -->
- **[UKGovernmentBEIS/inspect_ai — GitHub](https://github.com/UKGovernmentBEIS/inspect_ai)** — source repository. Pin the version in the CMC.
- **[UKGovernmentBEIS/inspect_evals — GitHub](https://github.com/UKGovernmentBEIS/inspect_evals)** — the eval library; worked examples of Inspect solvers and scorers for red-team-relevant behaviours. <!-- needs-research: confirm the current path to red-team-relevant Inspect evals. -->

### Microsoft — PyRIT

- **[Azure/PyRIT — GitHub](https://github.com/Azure/PyRIT)** — source repository. The reference implementation for the PAIR / TAP / Crescendo orchestrators discussed in chapter 03.
- **[PyRIT documentation](https://azure.github.io/PyRIT/)** — the framework's targets, orchestrators, converters, and scorers taxonomy. <!-- needs-research: confirm the current published documentation URL and pin the orchestrator taxonomy version. -->
- **[Microsoft — Announcing PyRIT (blog post)](https://www.microsoft.com/en-us/security/blog/2024/02/22/announcing-microsofts-open-automation-framework-to-red-team-generative-ai-systems/)** — the announcement post; positions PyRIT's design intent. <!-- needs-research: verify the current URL and title. -->

### NVIDIA — garak

- **[NVIDIA/garak — GitHub](https://github.com/NVIDIA/garak)** — source repository. Probes, detectors, and report formats.
- **[Derczynski et al. — garak paper](https://arxiv.org/abs/2406.11036)** — the design paper documenting the probe / detector taxonomy. <!-- needs-research: confirm the arXiv identifier for the current version of the garak paper. -->
- **[garak documentation](https://reference.garak.ai/)** — the reference site; per-probe and per-detector documentation. <!-- needs-research: confirm the current canonical documentation URL. -->

### Promptfoo — red-team suite

- **[Promptfoo red-team documentation](https://www.promptfoo.dev/docs/red-team/)** — the CI-integrated red-team suite. <!-- needs-research: confirm the current documentation URL and the red-team-plugin taxonomy. -->
- **[promptfoo/promptfoo — GitHub](https://github.com/promptfoo/promptfoo)** — source repository. Pin the version in the CMC.

## Attack methodology papers

### PAIR, TAP, Crescendo, RL-based

- **[Chao et al. 2023 — "Jailbreaking Black Box Large Language Models in Twenty Queries" (PAIR)](https://arxiv.org/abs/2310.08419)** — the PAIR paper. The attacker / target / judge loop shape.
- **[Mehrotra et al. 2023 — "Tree of Attacks: Jailbreaking Black-Box LLMs Automatically" (TAP)](https://arxiv.org/abs/2312.02119)** — the TAP paper. Branching + pruning extension of PAIR.
- **[Russinovich et al. 2024 — "Great, Now Write an Article About That: The Crescendo Multi-Turn LLM Jailbreak Attack"](https://arxiv.org/abs/2404.01833)** — the Crescendo paper. Multi-turn refusal-erosion.
- **[Perez et al. 2022 — "Red Teaming Language Models with Language Models"](https://arxiv.org/abs/2202.03286)** — the foundational Anthropic paper for LLM-vs-LLM red-teaming and RL-based attackers.
- **[Ganguli et al. 2022 — "Red Teaming Language Models to Reduce Harms: Methods, Scaling Behaviors, and Lessons Learned"](https://arxiv.org/abs/2209.07858)** — the empirical scaling paper; corpus release notes referenced by chapter 04.

### Complementary attack literature

- **[Zou et al. 2023 — "Universal and Transferable Adversarial Attacks on Aligned Language Models" (GCG)](https://arxiv.org/abs/2307.15043)** — the white-box gradient-based baseline mod-104 covers at depth; cited here as the technique-axis anchor.
- **[Anil et al. 2024 — "Many-shot Jailbreaking"](https://www.anthropic.com/research/many-shot-jailbreaking)** — many-shot in-context jailbreaking; one axis of the coverage matrix. <!-- needs-research: confirm the current canonical Anthropic URL for the many-shot writeup. -->
- **[Shen et al. 2024 — "Do Anything Now"](https://arxiv.org/abs/2308.03825)** — the in-the-wild DAN-family jailbreak taxonomy that feeds chapter 04's public-corpus curation.

## Judge methodology

### StrongREJECT and related

- **[Souly et al. 2024 — "A StrongREJECT for Empty Jailbreaks"](https://arxiv.org/abs/2402.10260)** — the paper. The rubric, the empty-jailbreak carve-out, and the calibration methodology.
- **[alexandrasouly/strongreject — GitHub](https://github.com/alexandrasouly/strongreject)** — the reference implementation. Rubric prompt files, judge integration. <!-- needs-research: confirm the current canonical repository location; the authors have re-hosted. -->

### Comparison benchmarks

- **[JailbreakBench](https://jailbreakbench.github.io/)** — the benchmark's judge methodology; the StrongREJECT integration in the JailbreakBench harness. <!-- needs-research: pin the documentation URL for the judge integration. -->
- **[HarmBench](https://www.harmbench.org/)** — alternative judge / benchmark stack. Comparing HarmBench's judge to StrongREJECT is standard methodology. <!-- needs-research: pin the current HarmBench canonical URL. -->
- **[AIR-Bench 2024 — AI Risk Benchmark](https://crfm.stanford.edu/helm/air-bench/)** — Stanford CRFM's AI risk benchmark; complementary judging methodology. <!-- needs-research: confirm the current canonical URL and version. -->
- **[MLCommons AILuminate](https://mlcommons.org/benchmarks/ai-luminate/)** — MLCommons safety benchmark methodology; per-behaviour-category rubric shape. <!-- needs-research: pin the current published version. -->

### Judge-drift and calibration

- **[Zheng et al. 2023 — "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena"](https://arxiv.org/abs/2306.05685)** — the reference paper on LLM-judge calibration methodology; the framing this module inherits.

## Frontier-safety programme framing

Cited so the CMC's downstream artefacts (safety cases, disclosures, tier-decision letters) can be reasoned about with the correct programme scope.

- **[Anthropic — Responsible Scaling Policy](https://www.anthropic.com/rsp)** — the RSP tier structure; the red-team + capability-eval programme feeds tier decisions. <!-- needs-research: pin the current version. -->
- **[OpenAI — Preparedness Framework](https://openai.com/preparedness/)** — the Preparedness scorecard; tracked-categories drive the CMC's behaviour-category axis. <!-- needs-research: pin the current published version. -->
- **[Google DeepMind — Frontier Safety Framework](https://deepmind.google/discover/blog/updating-the-frontier-safety-framework/)** — the CCL framing. <!-- needs-research: pin the current published version. -->
- **[UK AI Safety Institute — Approach to Evaluations](https://www.aisi.gov.uk/work/approach-to-evaluations)** — AISI's pre-deployment methodology; treats coverage and reproducibility as first-class. <!-- needs-research: confirm the current URL. -->

## Harmful-payload policy references

### Regulatory shape

- **[EU AI Act — Article 55 (GPAI models with systemic risk)](https://artificialintelligenceact.eu/article/55/)** — the regulatory obligation shape for red-team documentation. <!-- needs-research: pin the current published article text; the Act has been amended. -->
- **[EU AI Act — Article 73 (serious-incident reports)](https://artificialintelligenceact.eu/article/73/)** — the disclosure shape red-team findings feed. <!-- needs-research: pin the current published article text. -->
- **[EU General-Purpose AI Code of Practice — safety and security chapter](https://digital-strategy.ec.europa.eu/en/policies/ai-code-practice)** — the working-group deliverable that operationalises the Article 55 obligation. <!-- needs-research: confirm the current canonical URL and version. -->
- **[NIST AI RMF 1.0](https://airc.nist.gov/AI_RMF_Knowledge_Base/AI_RMF)** — the Manage function's evidence-management posture. <!-- needs-research: confirm the current canonical URL. -->

### Responsible-disclosure references

- **[Anthropic — Responsible Disclosure Policy for AI Vulnerabilities](https://www.anthropic.com/responsible-disclosure-policy)** — the reference posture on harmful-payload handling in vulnerability writeups. <!-- needs-research: confirm the current title and URL. -->
- **[OpenAI — Coordinated Vulnerability Disclosure Policy](https://openai.com/policies/coordinated-vulnerability-disclosure-policy/)** — the analogous OpenAI posture. <!-- needs-research: confirm the current title and URL. -->
- **[Google DeepMind — vulnerability reporting](https://deepmind.google/about/responsibility-safety/)** — DeepMind's responsibility-and-safety posture. <!-- needs-research: pin the specific vulnerability-disclosure sub-page. -->

### Base-model licence and AUP references

For the fine-tuned-attacker base-model choice discussed in chapter 04.

- **[Meta Llama — Acceptable Use Policy](https://www.llama.com/llama3/use-policy/)** — the AUP for Llama-family bases. <!-- needs-research: pin the specific version applicable to the base model chosen. -->
- **[Mistral AI — model licenses](https://mistral.ai/terms/)** — the Mistral-family licence terms. <!-- needs-research: confirm the canonical location; Mistral publishes per-model licence pages. -->
- **[Qwen — model licenses (via GitHub / HuggingFace)](https://huggingface.co/Qwen)** — the Qwen-family licence terms. <!-- needs-research: pin the specific model-card licence page for the chosen base. -->

## Taxonomy / technique references

- **[NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2025.pdf)** — attack-technique-axis vocabulary. <!-- needs-research: verify the current published revision. -->
- **[MITRE ATLAS](https://atlas.mitre.org/)** — technique catalogue for external-facing disclosures.
- **[OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/llm-top-10/)** — the behaviour-category anchor for LLM-app-shaped deployments. <!-- needs-research: pin the current version. -->
- **[OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)** — the agentic-shape category anchor for tool-using-agent deployments.

## Notes on citation discipline

- Every entry above must be version-pinned when cited in a CMC or a replay bundle. A `<!-- needs-research: -->` marker in a chapter indicates a URL or version that must be re-verified before citation.
- Do not cite derivative blog posts, aggregator sites, or third-party summaries in a defensible artefact. If the primary source is behind a paywall or has been withdrawn, the citation notes the fact and the artefact's reviewer decides whether the finding stands.
- Public jailbreak / red-team corpora carry their own licences; the CMC section 5 (attack-corpus contract) records the licence per corpus and the reviewer's sign-off.
