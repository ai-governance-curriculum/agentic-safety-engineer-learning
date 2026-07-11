# mod-101 — Frontier Safety Frameworks and the Safety-Engineer Position on the Governance Ladder

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 14 hours (≈1 hour lecture reading + 13 hours exercises)

## Why this module exists

Before you write a single jailbreak prompt or fine-tune a classifier guard, you have to know **what evidence you are producing, for whom, and against what threshold**. A frontier-safety engineer at level 40 is not shipping generic red-team artifacts — you are producing artifacts that plug into a specific ladder of safety commitments (Anthropic RSP, OpenAI Preparedness, Google DeepMind FSF), a specific regulatory shape (EU AI Act Articles 55 / 56 / 73, EU GPAI Code of Practice safety chapter), and a specific public-policy scaffolding (US EO 14110 legacy, Bletchley Declaration, Seoul Statement of Intent, UK + US AI Safety Institutes, Frontier Model Forum). Reading these documents *as an engineer* — mapping each tier to concrete elicitation protocols, tripwires, rollback contracts, and evidence artifacts — is the substrate the rest of the track builds on.

This module does two things:

1. **Framework fluency** — reads the frontier-lab safety frameworks, the EU regulatory shape, the US + UK policy shape, and the Government AI Safety Institute methodology as an engineer, and forces a concrete map from tier → engineering artifact.
2. **Role positioning** — draws the exact scope line between this role and its neighbours on the ladder. The `ai-risk-engineer-learning` (level 25) packet is treated as a **prerequisite** and is not re-taught. `ai-evaluation-engineer` (peer / next-up, level 35), `senior-ai-governance-architect` (level 50), and `head-of-ai-governance` (level 60) inherit or extend this packet upward; the boundary is codified so you know when to stop and defer.

## Learning objectives

By the end of the module you can:

1. Read Anthropic RSP, OpenAI Preparedness Framework, and Google DeepMind FSF as an engineer, and map each **ASL / Preparedness tier / Critical Capability Level** to concrete elicitation protocols, tripwires, rollback contracts, and evidence artifacts.
2. Read the EU AI Office General-Purpose AI Code of Practice safety chapter and EU AI Act Articles 55, 56, 73 as the regulatory shape you feed evidence into.
3. Read US Executive Order 14110 (and its January 2025 rescission), the Bletchley Declaration and Seoul Statement of Intent, UK AISI + US AISI (NIST) pre-deployment testing methodology, and Frontier Model Forum working papers as the public-policy context for frontier-safety commitments.
4. Diagram this role's exact scope on the level ladder — what `ai-risk-engineer` (level 25) already owns, what this role owns, and what `ai-evaluation-engineer` (level 35), `senior-ai-governance-architect` (level 50), and `head-of-ai-governance` (level 60) inherit or extend.
5. Position the ai-risk-engineer (level 25) packet as the prerequisite, and enumerate what this curriculum does **not** re-teach.
6. Read the certifications portfolio — IAPP AIGP, ISO/IEC 42001 lead-implementer / lead-auditor, ForHumanity Independent AI Auditor, BABL AI — and know which are load-bearing in senior AI-safety hiring.

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Position on the governance ladder](01-position-on-the-governance-ladder.md) | Draws the level-40 boundary against the level-25 prerequisite, level-35 peers, and level-50/60 next-up. Sets the ownership rule this module reinforces. |
| 02 | [Anthropic Responsible Scaling Policy (RSP)](02-anthropic-responsible-scaling-policy.md) | Reads the RSP as an engineer: AI Safety Levels, Capability Thresholds, Required Safeguards, evaluation cadence, and the artifacts you produce for each. |
| 03 | [OpenAI Preparedness Framework](03-openai-preparedness-framework.md) | Reads the Preparedness Framework: Tracked Categories, capability thresholds, pre- and post-mitigation scorecard, and the Preparedness Committee / Safety Advisory Group loop. |
| 04 | [Google DeepMind Frontier Safety Framework (FSF)](04-google-deepmind-frontier-safety-framework.md) | Reads the FSF: Critical Capability Levels, misuse and misalignment CCLs, mitigation levels, review cadence, and evidence artifacts. |
| 05 | [Comparing the three frameworks](05-comparing-the-three-frameworks.md) | Cross-reads RSP × Preparedness × FSF as a common shape (evaluation → threshold → tripwire → mitigation → disclosure) and calls out where they diverge. |
| 06 | [EU AI Act Articles 55 / 56 / 73 and the GPAI Code of Practice safety chapter](06-eu-ai-act-and-gpai-code-of-practice.md) | The regulatory shape you feed evidence into: systemic-risk GPAI obligations, adversarial testing, serious-incident reporting, and the Code of Practice as the Article 56 implementation lane. |
| 07 | [US policy, summit outcomes, and the Frontier Model Forum](07-us-policy-summits-and-frontier-model-forum.md) | US EO 14110 (and the January 2025 rescission), Bletchley Declaration, Seoul Statement of Intent, Frontier AI Safety Commitments, and the FMF as the industry-body publication channel. |
| 08 | [AISI pre-deployment testing methodology](08-aisi-pre-deployment-methodology.md) | UK AISI + US AISI (NIST) evaluation methodology, the Inspect harness, published pre-deployment evaluations, and the engineering contract for a lab that ships to an AISI window. |
| 09 | [Certifications portfolio](09-certifications-portfolio.md) | IAPP AIGP, ISO/IEC 42001 lead-implementer / lead-auditor, ForHumanity Independent AI Auditor, BABL AI — which are load-bearing at level 40 vs. above. |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [RSP × Preparedness × FSF comparative read](exercises/exercise-01-rsp-preparedness-fsf-comparative-read.md) | 3 |
| 02 | [ASL / Preparedness tier / CCL → engineering artifact map](exercises/exercise-02-asl-and-critical-capability-level-to-engineering-artifact-map.md) | 3 |
| 03 | [EU GPAI Code of Practice safety chapter walkthrough](exercises/exercise-03-eu-gpai-code-of-practice-safety-chapter-walkthrough.md) | 2 |
| 04 | [AISI pre-deployment methodology read](exercises/exercise-04-aisi-pre-deployment-methodology-read.md) | 2 |
| 05 | [Role scope + deferral contract vs. `ai-risk-engineer`](exercises/exercise-05-role-scope-and-deferral-contract-vs-ai-risk-engineer.md) | 2 |
| 06 | [Certifications portfolio planner](exercises/exercise-06-certifications-portfolio-planner.md) | 1 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys here.

## Structure

```
mod-101-frontier-safety-frameworks-and-position/
├── 01-…09-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise and map — they do **not** substitute for reading the primary documents. Every chapter links to the canonical URL. **Read the primary source once, then re-read it against the chapter.** If a chapter and the primary document disagree, the primary document wins — file an issue.
