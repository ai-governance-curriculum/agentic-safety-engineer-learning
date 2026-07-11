# Prerequisites — Agentic Safety & Red-Team Engineer track

**Role level:** 40 (senior technical specialist, AI Governance family)
**Track:** `agentic-safety-engineer-learning`

This track is a senior technical specialty. It assumes the learner arrives with the general engineering craft of AI risk already fluent, ML fundamentals in place, and an application-layer LLM/agent workflow already muscle-memoried. Fundamentals are **not** re-taught — the modules step directly onto frontier safety frameworks, agent-attack surface engineering, dangerous-capability elicitation, safety-case authoring, and AI-control evaluation.

## Assumed lower-level curriculum

Complete or be practically fluent in the equivalent of these lower-level tracks before starting. The **immediately-below prerequisite is `ai-risk-engineer-learning` (level 25)** — this curriculum treats the entire ai-risk-engineer packet as prerequisite and adds frontier-agent depth on top.

- **[`ai-infra-junior-engineer-learning`](https://github.com/ai-infra-curriculum/ai-infra-junior-engineer-learning) — level 10** — engineering-craft prerequisites (Linux, Git, Python packaging, HTTP APIs, unit testing, logging, docker basics).
- **[`ai-governance-analyst-learning`](https://github.com/ai-governance-curriculum/ai-governance-analyst-learning) — level 15** — operational analyst work in the AI Governance family (intake, inventory, framework crosswalks, first-draft impact assessments, model-card authoring at analyst tier, regulatory tracking).
- **[`ml-engineer-learning`](https://github.com/ml-engineering-curriculum/ml-engineer-learning) — level 20** — classical ML with scikit-learn, deep learning with PyTorch and Hugging Face Transformers, packaging with FastAPI + Docker, experiment tracking with MLflow / Weights & Biases.
- **[`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — level 25 — REQUIRED IMMEDIATE PREREQUISITE** — the general engineering craft of AI risk. This curriculum expects fluency with:
  - Reading NIST AI RMF + GenAI Profile + ISO/IEC 42001 / 23894 / 42005 / 25059 + EU AI Act Articles 9-15 + SR 11-7 + FDA GMLP + PCCP at engineering depth
  - Harm-model authoring grounded in AIID / OECD.AI incidents / MIT AI Risk Repository
  - Impact × likelihood × exposure risk quantification, inherent / residual / control-defeated distinctions, portfolio aggregation
  - LLM red-teaming at general depth with garak / PyRIT / Promptfoo red-team / Inspect (this curriculum re-uses those suites and adds frontier-depth attack engineering + LLM-vs-LLM attacker loops on top)
  - Adversarial-ML at working depth with ART / TextAttack / CleverHans, mapped to NIST AI 100-2 + MITRE ATLAS + OWASP ML Top 10
  - Fairness / bias / explainability with Fairlearn / AIF360 / SHAP / Alibi / LIME
  - Privacy-risk engineering with Opacus / TensorFlow Privacy / ML Privacy Meter / Presidio, membership-inference (Shokri MIA / Carlini LiRA) at production quality
  - Guardrail engineering at general depth with NeMo Guardrails / Guardrails AI / Llama Guard / ShieldGemma / OpenAI Moderation / Constitutional Classifiers (this curriculum adds fine-tuning-the-classifier + adaptive-attack survival + defence-in-depth composition)
  - Monitoring-into-risk-register wiring, MRM shape, AI supply-chain provenance
  - AI-specific incident RCA + post-mortem + regression-fixture back-feed at team scope
  - Cross-functional AI risk program at team scope

  If you cannot ship any of these end-to-end, complete `ai-risk-engineer-learning` first.

## Assumed working knowledge (beyond the ai-risk-engineer prerequisite)

- **Agentic-AI application development** — comfortable building tool-using agents with LangChain, LangGraph, CrewAI, AutoGen, AG2, the OpenAI Agents SDK, or the Anthropic Model Context Protocol (MCP). If you have not built an agent that calls a real tool against a real API, complete the peer `llm-application-developer-learning` (level 25) or `applied-ai-engineer-learning` (level 25) first.
- **Application-layer LLM/agent evaluation-engineering plumbing** — traces (Arize Phoenix, Langfuse, LangSmith, W&B Weave, Braintrust), judges, RAG evaluation (RAGAS / TruLens), eval-gated CI/CD, online eval, cost / latency / quality trade-off. Deep depth here belongs to `ai-eval-engineer` (peer, level 30) — this curriculum builds on that plumbing.
- **Fine-tuning** — comfortable running SFT, PEFT / LoRA, and at least one preference-optimisation loop (RLHF via PPO or DPO). Deep depth belongs to `fine-tuning-engineer` (peer, level 30); this curriculum uses fine-tuning to build classifier guards + adversarial-attacker models.
- **Reading the primary literature end-to-end** — you should be able to open a paper on arXiv (e.g., Zou et al. GCG, Chao et al. PAIR, Mehrotra et al. TAP, Anthropic Constitutional Classifiers, Meinke et al. In-Context Scheming, Redwood AI Control, Clymer et al. Safety Cases) and translate the method into runnable code within a working day.
- **Statistics for capability elicitation** — best-of-N confidence intervals, elicitation-gap accounting, judge-vs-human calibration, small-N methodology. Deep depth belongs to `model-evaluation-engineer` (peer, level 30); this curriculum uses that methodology on its own dangerous-capability claims.
- **Working security literacy** — network sandboxing, secret scoping, principle-of-least-authority, threat modelling (STRIDE / MITRE ATT&CK at general awareness). Deep platform-scale ML security depth belongs to `security-learning` / `ai-infra-security` (peer / next-up, level 35).

## Not assumed, taught here

You do **not** need prior experience with:

- Any specific frontier safety framework — Anthropic RSP, OpenAI Preparedness, Google DeepMind FSF, EU GPAI Code of Practice safety chapter. Mod-101 covers each.
- Any specific frontier-depth attack methodology — GCG, PAIR, TAP, Crescendo, many-shot, Do-Anything-Now taxonomy. Mod-103 + mod-104 cover the working suite.
- Any specific agent-attack surface benchmark — AgentDojo, InjecAgent. Mod-105 covers the working suite.
- Any specific dangerous-capability benchmark — WMDP, Meta CyberSecEval 2 + 3, METR public tasks, RE-Bench, SWE-bench Verified. Mod-106 covers the working suite.
- Excessive-agency containment engineering (capability gates, sandbox tool execution, monitored-tool wrappers, human-in-the-loop bypass prevention, kill-switches). Mod-107 covers it end-to-end.
- Frontier-scale guardrail engineering (fine-tuning classifier guards, Constitutional Classifiers methodology, adaptive-attack survival curves, defence-in-depth composition). Mod-108 covers it.
- Safety-case authoring in Goal Structuring Notation with Inability / Control / Trustworthiness / Deference argument shapes. Mod-109 covers it.
- Deception / alignment-faking / sandbagging / sleeper-agent / in-context-scheming / sabotage evaluation methodology. Mod-110 covers it.
- Automated + scaled red-teaming with UK AISI Inspect + fine-tuned adversarial-attacker models + StrongREJECT judge methodology + harmful-payload storage discipline. Mod-111 covers it.
- System-card + AISI-report + EU AI Act Article 73 serious-incident-report authoring. Mod-112 covers it.

## Recommended reading before starting

- [Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp) — front-to-back once.
- [OpenAI Preparedness Framework](https://openai.com/safety/preparedness/) — front-to-back once.
- [Google DeepMind Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/) — front-to-back once.
- [OWASP Top 10 for LLM Applications (2025)](https://owasp.org/www-project-top-10-for-large-language-model-applications/) and [OWASP Agentic AI — Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/).
- One recent public system card (e.g., [OpenAI o1 System Card](https://openai.com/index/openai-o1-system-card/), an Anthropic Claude system card, or a Google DeepMind Gemini system card) — read it as an engineer would, tracing each capability claim back to an elicitation-methodology evidence artifact.
- One AISI pre-deployment report (e.g., [UK AISI pre-deployment evaluation of Claude 3.5 Sonnet](https://www.aisi.gov.uk/work/pre-deployment-evaluation-of-anthropics-upgraded-claude-3-5-sonnet)).
- Zou et al. [GCG](https://arxiv.org/abs/2307.15043) and Chao et al. [PAIR](https://arxiv.org/abs/2310.08419) — foundational jailbreak-generation methods.
- Clymer et al. [Safety Cases](https://arxiv.org/abs/2403.10462) — the safety-case shape.
- Redwood [AI Control paper](https://arxiv.org/abs/2312.06942) and one Apollo Research deception-eval writeup — the adversarial-alignment paradigm.

## Not required, useful to have

- Prior work with UK AISI [Inspect](https://inspect.aisi.org.uk/), Microsoft [PyRIT](https://github.com/Azure/PyRIT), or NVIDIA [garak](https://github.com/NVIDIA/garak) as an orchestration + attack library — the `ai-risk-engineer` prerequisite covers each at general depth; this curriculum adds frontier orchestration and LLM-vs-LLM attacker loops on top.
- Prior work with a commercial AI-red-team or AI-runtime-security product (Lakera Guard, HiddenLayer, Protect AI, Robust Intelligence, Calypso AI, HydroX) — mod-108 walks the vendor landscape and the build-vs-buy matrix.
- Certifications: [IAPP AIGP](https://iapp.org/certify/aigp/) is the most-cited certification in senior AI safety / governance postings; earning it is not required but is treated as a strong signal at level 40. The frontier-safety hiring signal that typically clears the bar is documented RSP / Preparedness / FSF contribution history, published system-card evidence, or AISI methodology contribution.

## Not in scope for this track (linked out)

- **General engineering craft of AI risk** — this is the prerequisite `ai-risk-engineer-learning` (level 25) track. Not re-taught.
- **Application-layer LLM/agent evaluation-engineering plumbing** — traces, judges, RAG eval, eval-gated CI/CD, online eval, cost / latency / quality — owned by [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) (peer, level 30, AI Engineering family).
- **Statistical / benchmark / judge-vs-human calibration methodology depth** — owned by [`model-evaluation-engineer-learning`](https://github.com/ml-engineering-curriculum/model-evaluation-engineer-learning) (peer, level 30, ML Engineering family).
- **Post-training / safety-tuning stack** — SFT, RLHF, DPO, Constitutional AI, IPO — owned by [`fine-tuning-engineer-learning`](https://github.com/ml-engineering-curriculum/fine-tuning-engineer-learning) (peer, level 30). Mod-108 + mod-110 test the safety-tuning delta; depth linked out.
- **Deep ML/AI security at platform scale** — inference-edge adversarial defence, model-extraction defence, judge supply-chain, eval-set exfiltration prevention, MLSecOps, model-signing + provenance — owned by [`security-learning`](https://github.com/ai-infra-curriculum/security-learning) and [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) (peer / next-up, level 35).
- **Release-assurance / audit-trail / regulator-facing methodology depth** — owned by [`ai-evaluation-engineer-learning`](https://github.com/ai-governance-curriculum/ai-evaluation-engineer-learning) (peer / next-up, level 35, Governance family).
- **Control-library architecture, policy taxonomy, cross-jurisdiction reconciliation** — owned by [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) (level 50).
- **Program leadership, board-level reporting, regulator engagement** — owned by [`head-of-ai-governance-learning`](https://github.com/ai-governance-curriculum/head-of-ai-governance-learning) (level 60) and [`chief-ai-officer-learning`](https://github.com/ai-governance-curriculum/chief-ai-officer-learning) (level 70).
- **Legal opinion** — out of scope; routed to counsel.
- **SOC-side incident handling** — out of scope; this role feeds AI-specific signal to SecOps.
- **Novel-alignment research at PhD-publication depth** (interpretability research, mech-interp, RLHF theory, scalable-oversight theory) — out of scope for this applied-engineering track. This role *consumes* that research (Redwood control, Apollo deception, Anthropic sleeper-agents / alignment-faking / sabotage-evals, Meinke et al. scheming) and productionises evaluations, but does not compete for postings on the frontier-lab safety-research-scientist ladder.
