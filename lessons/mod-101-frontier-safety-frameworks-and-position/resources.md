# Resources for mod-101 — Frontier Safety Frameworks and the Safety-Engineer Position on the Governance Ladder

This is the curated primary-source reading list for the module. Every chapter links to a subset of these; this file collects them in one place so you can build a personal reading bookmark set. **Read the primary source before the chapter's summary.** When the two disagree, the primary source wins.

---

## Frontier-lab safety frameworks

### Anthropic Responsible Scaling Policy (RSP)

- [Anthropic Responsible Scaling Policy — hub](https://www.anthropic.com/rsp)
- [Anthropic's Responsible Scaling Policy — original announcement (Sept 2023)](https://www.anthropic.com/news/anthropics-responsible-scaling-policy)
- [Anthropic Core Views on AI Safety](https://www.anthropic.com/news/core-views-on-ai-safety)
- [Anthropic — Model Cards / System Cards](https://www.anthropic.com/news) (browse for the current model's system card as an evidence-artifact example)

### OpenAI Preparedness Framework

- [OpenAI Preparedness Framework — hub](https://openai.com/safety/preparedness/)
- [Preparedness Framework — initial (beta) version PDF, Dec 2023](https://cdn.openai.com/openai-preparedness-framework-beta.pdf)
- [Updating our Preparedness Framework (2025 update announcement)](https://openai.com/index/updating-our-preparedness-framework/)
- [OpenAI Safety practices — hub](https://openai.com/safety/)
- [OpenAI o1 System Card](https://openai.com/index/openai-o1-system-card/) — example evidence artifact.

### Google DeepMind Frontier Safety Framework (FSF)

- [Introducing the Frontier Safety Framework (May 2024)](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/)
- [Frontier Safety Framework — full text hub](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/)
- [Updating the Frontier Safety Framework (Feb 2025)](https://deepmind.google/discover/blog/updating-the-frontier-safety-framework/)
- [Google DeepMind Responsibility & Safety](https://deepmind.google/about/responsibility-safety/)

---

## EU regulatory shape

- [EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Articles 55, 56, 73 are the specific ones for this module.
- [EU AI Office](https://digital-strategy.ec.europa.eu/en/policies/ai-office)
- [General-Purpose AI Code of Practice — Commission hub](https://digital-strategy.ec.europa.eu/en/policies/ai-code-practice)
- [European AI Board](https://digital-strategy.ec.europa.eu/en/policies/ai-board)

<!-- needs-research: add direct links to the currently published Safety and Security chapter of the GPAI Code of Practice, the signatory list, and any published Commission implementing regulations that touch Article 55 / 56 / 73. -->

---

## US and international policy

### US

- [Executive Order 14110 — full text (Federal Register, Oct 2023)](https://www.federalregister.gov/documents/2023/11/01/2023-24283/safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence)
- [Executive Order 14179 — rescinding EO 14110 (Jan 2025)](https://www.federalregister.gov/documents/2025/01/31/2025-02172/removing-barriers-to-american-leadership-in-artificial-intelligence)
- [White House AI Action Plan](https://www.whitehouse.gov/ai/) <!-- needs-research: confirm current URL and publication status -->

### Summit series

- [Bletchley Declaration (Nov 2023)](https://www.gov.uk/government/publications/ai-safety-summit-2023-the-bletchley-declaration/the-bletchley-declaration-by-countries-attending-the-ai-safety-summit-1-2-november-2023)
- [Seoul Declaration (May 2024)](https://www.gov.uk/government/publications/seoul-declaration-for-safe-innovative-and-inclusive-ai-ai-seoul-summit-2024)
- [Frontier AI Safety Commitments (Seoul, May 2024)](https://www.gov.uk/government/publications/frontier-ai-safety-commitments-ai-seoul-summit-2024/frontier-ai-safety-commitments-ai-seoul-summit-2024)

<!-- needs-research: add outcomes from the Paris AI Action Summit (Feb 2025) and any subsequent summit (India 2026) once URLs stabilise. -->

### Industry body

- [Frontier Model Forum](https://www.frontiermodelforum.org/)
- [Frontier Model Forum — publications and updates](https://www.frontiermodelforum.org/updates/)
- [Partnership on AI](https://partnershiponai.org/) — adjacent industry-body publications.

---

## AI Safety Institutes

### UK

- [UK AI Safety Institute / AI Security Institute](https://www.aisi.gov.uk/)
- [Inspect — UK AISI evaluation harness](https://inspect.aisi.org.uk/)
- [Inspect on GitHub](https://github.com/UKGovernmentBEIS/inspect_ai)
- [UK AISI pre-deployment evaluation of Claude 3.5 Sonnet (upgraded)](https://www.aisi.gov.uk/work/pre-deployment-evaluation-of-anthropics-upgraded-claude-3-5-sonnet)
- [UK AISI — published research and evaluations](https://www.aisi.gov.uk/research)

<!-- needs-research: confirm the UK AISI's current published name (renamed to AI Security Institute in Feb 2025) and current published pre-deployment evaluations. -->

### US

- [US AI Safety Institute (NIST)](https://www.nist.gov/aisi)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [NIST AI 100-2 (Adversarial ML Taxonomy)](https://csrc.nist.gov/publications/detail/nistir/8269/draft)
- [NIST GenAI Program](https://ai-challenges.nist.gov/genai)

<!-- needs-research: confirm current US AISI status under successor US policy, and any published pre-deployment evaluations from the US side. -->

---

## Threat taxonomies and adjacent AI-security standards

- [OWASP Top 10 for LLM Applications (2025)](https://owasp.org/www-project-top-10-for-large-language-model-applications/)
- [OWASP Agentic AI — Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)
- [OWASP GenAI Red Teaming Guide](https://genai.owasp.org/resource/genai-red-teaming-guide/)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [Google Secure AI Framework (SAIF)](https://safety.google/cybersecurity-advancements/saif/)
- [Microsoft AI Red Team Guide](https://learn.microsoft.com/en-us/security/ai-red-team/)

---

## Foundational papers cited across the module

The chapters reference these but do not require them as first-read. They are the load-bearing background for the frontier-safety literature and will re-appear in mod-104, mod-106, mod-108, mod-109, and mod-110.

### Safety-case engineering

- [Clymer et al. — Safety Cases: How to Justify the Safety of Advanced AI Systems (2024)](https://arxiv.org/abs/2403.10462)
- [Goemans et al. — Safety Case Template for Frontier AI (2024)](https://arxiv.org/abs/2410.21572)
- [GSN Community Standard v3](https://scsc.uk/gsn)

### AI control and adversarial-alignment evaluation (used in FSF misalignment CCLs)

- [Redwood Research — AI Control paper (Greenblatt et al., 2023)](https://arxiv.org/abs/2312.06942)
- [Anthropic Sleeper Agents (Hubinger et al., 2024)](https://arxiv.org/abs/2401.05566)
- [Anthropic + Redwood — Alignment Faking in Large Language Models (2024)](https://arxiv.org/abs/2412.14093)
- [Anthropic — Sabotage Evaluations for Frontier Models (2024)](https://arxiv.org/abs/2410.21514)
- [Meinke et al. — Frontier Models are Capable of In-Context Scheming (2024)](https://arxiv.org/abs/2412.04984)
- [Apollo Research](https://www.apolloresearch.ai/research)

### Dangerous-capability elicitation (used in RSP / Preparedness / FSF thresholds)

- [WMDP benchmark (Li et al., 2024)](https://arxiv.org/abs/2403.03218)
- [Meta CyberSecEval 3 (2024)](https://arxiv.org/abs/2408.01605)
- [METR (formerly ARC Evals) — public tasks](https://metr.github.io/public-tasks/)
- [METR — RE-Bench (2024)](https://metr.org/blog/2024-11-22-evaluating-r-d-capabilities-of-llms/)
- [SWE-bench](https://www.swebench.com/)

### Jailbreak and prompt-injection (used in mitigation-effectiveness measurement)

- [Zou et al. — GCG (2023)](https://arxiv.org/abs/2307.15043)
- [Chao et al. — PAIR (2023)](https://arxiv.org/abs/2310.08419)
- [Mehrotra et al. — TAP (2023)](https://arxiv.org/abs/2312.02119)
- [Anthropic — Many-shot Jailbreaking (2024)](https://www.anthropic.com/research/many-shot-jailbreaking)
- [Anthropic — Constitutional Classifiers (2025)](https://www.anthropic.com/research/constitutional-classifiers)
- [Greshake et al. — Not what you've signed up for (indirect prompt injection, 2023)](https://arxiv.org/abs/2302.12173)

---

## Incident corpora (used for regulatory serious-incident work)

- [AI Incident Database](https://incidentdatabase.ai/)
- [OECD.AI Incidents Monitor](https://oecd.ai/en/incidents)
- [MIT AI Risk Repository](https://airisk.mit.edu/)

---

## Enterprise-standards this role plugs into

- [ISO/IEC 42001:2023 — AI management system](https://www.iso.org/standard/81230.html)
- [ISO/IEC 23894 — AI risk management guidance](https://www.iso.org/standard/77304.html)
- [ISO/IEC 42005:2025 — AI system impact assessment](https://www.iso.org/standard/44545.html)
- [ISO/IEC 25059 — AI quality model](https://www.iso.org/standard/80655.html)

---

## Certifications

- [IAPP Artificial Intelligence Governance Professional (AIGP)](https://iapp.org/certify/aigp/)
- [PECB ISO/IEC 42001 lead-implementer / lead-auditor programs](https://pecb.com/en/education-and-certification-for-individuals/iso-iec-42001)
- [BSI training on AI management systems](https://www.bsigroup.com/en-GB/products-and-services/training-courses/)
- [ForHumanity Independent AI Auditor](https://forhumanity.center/independent-audit-of-ai-systems/)
- [BABL AI](https://babl.ai/)

<!-- needs-research: add IAPP Certified Artificial Intelligence Governance Professional (CAIGP) or any successor / additional IAPP credential once published; add IEEE CertifAIEd and ISACA AI extensions once their AI curricula stabilise. -->

---

## Adjacent tracks in the AI Career Curriculum ecosystem

- [`ai-risk-engineer-learning`](https://github.com/ai-governance-curriculum/ai-risk-engineer-learning) — the required immediate prerequisite (level 25).
- [`ai-governance-analyst-learning`](https://github.com/ai-governance-curriculum/ai-governance-analyst-learning) — analyst work below the prerequisite (level 15).
- [`ai-eval-engineer-learning`](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) — application-layer evaluation-engineering plumbing (level 30, peer).
- [`model-evaluation-engineer-learning`](https://github.com/ml-engineering-curriculum/model-evaluation-engineer-learning) — statistical / calibration methodology (level 30, peer).
- [`fine-tuning-engineer-learning`](https://github.com/ml-engineering-curriculum/fine-tuning-engineer-learning) — post-training / safety-tuning (level 30, peer).
- [`security-learning`](https://github.com/ai-infra-curriculum/security-learning) and [`ai-infra-security-learning`](https://github.com/ai-infra-curriculum/ai-infra-security-learning) — platform-scale ML security (level 35, peer / next-up).
- [`ai-evaluation-engineer-learning`](https://github.com/ai-governance-curriculum/ai-evaluation-engineer-learning) — release-assurance / audit-trail methodology (level 35, peer / next-up).
- [`senior-ai-governance-architect-learning`](https://github.com/ai-governance-curriculum/senior-ai-governance-architect-learning) — control-library architecture (level 50).
- [`head-of-ai-governance-learning`](https://github.com/ai-governance-curriculum/head-of-ai-governance-learning) — program leadership (level 60).
- [`chief-ai-officer-learning`](https://github.com/ai-governance-curriculum/chief-ai-officer-learning) — executive scope (level 70).
