# mod-104 — Jailbreak Engineering at Frontier Depth

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 18 hours (≈3 hours lecture reading + 15 hours exercises)
**Prerequisites:** [mod-101 (Frontier Safety Frameworks)](../mod-101-frontier-safety-frameworks-and-position/README.md), [mod-102 (Threat Modelling for Autonomous and Tool-Using Agents)](../mod-102-agent-threat-modelling/README.md), and [mod-103 (Prompt-Injection Engineering)](../mod-103-prompt-injection-engineering/README.md). The level-25 `ai-risk-engineer` prerequisite for the general LLM-security craft — garak, PyRIT, Promptfoo as working suites — is assumed; this module extends that with frontier-depth attack engineering and LLM-vs-LLM attacker loops.

## Why this module exists

Jailbreak — attacker-controlled text that causes a model to violate its own safety training — is what the popular press means when it says "jailbreak." The literature has moved well past the popular meaning. GCG (Zou et al., 2023) turned the problem into a differentiable one and showed suffixes that transfer between models. PAIR (Chao et al., 2023) and TAP (Mehrotra et al., 2023) turned the black-box attack into an LLM-vs-LLM loop that finds new jailbreaks in tens of queries. Anthropic's Many-Shot Jailbreaking (2024) showed that long context is itself an attack vector. Crescendo (Russinovich et al., 2024) showed that refusals erode across turns even when they hold on turn one. Shen et al. ("Do Anything Now", 2024) catalogued the in-the-wild DAN-family evolution that any production defence has to survive. HarmBench (Mazeika et al., 2024), JailbreakBench (Chao et al., 2024), AIR-Bench 2024, MLCommons AILuminate, and Meta CyberSecEval turned all of this into shippable benchmarks with judge protocols. StrongREJECT (Souly et al., 2024) is the current honest-broker judge methodology.

The engineering artefact you author from this module is a **Jailbreak Evaluation Harness (JEH)** for one concrete target — an open-weight model, a production API, or a tool-using agent that wraps either — with white-box + black-box + multi-turn + many-shot attackers, a StrongREJECT-style judge, and a benchmark-coverage report on HarmBench / JailbreakBench / AIR-Bench / AILuminate / CyberSecEval. The JEH is what the mod-111 automated / scaled red-team industrialises and what the mod-112 safety program disclosures cite.

## Learning objectives

By the end of the module you can:

1. Engineer **white-box gradient-based jailbreaks** — GCG (Zou et al., 2023) universal + transferable adversarial suffixes — for open-weight models; reason about GCG's transferability to closed models and measure the transfer rate on your target.
2. Engineer **black-box iterative jailbreaks** — PAIR (Chao et al., 2023), TAP (Mehrotra et al., 2023), and Crescendo multi-turn refusal-erosion (Russinovich et al., 2024) — with LLM-vs-LLM attacker loops running against production APIs under rate-limit and cost budgets.
3. Engineer **many-shot in-context jailbreaks** (Anthropic 2024) — pattern establishment across hundreds of few-shot demos under long-context models — plus role-play / persona-hijack, low-resource-language translation attacks (Yong et al., 2023), and cipher attacks (Yuan et al., 2023).
4. Ship coverage against **HarmBench** (Mazeika et al., 2024), **JailbreakBench** (Chao et al., 2024), **AIR-Bench 2024** (Zeng et al., 2024), **MLCommons AILuminate**, and **Meta CyberSecEval** — reporting Attack Success Rate with **StrongREJECT-style** judge methodology (Souly et al., 2024), refusal robustness, and adaptive-attack survival.
5. Reason about **in-the-wild jailbreak taxonomies** — Shen et al.'s "Do Anything Now" corpus, the DAN-family evolution, and adjacent live-catalogue efforts — and translate them into permanent regression fixtures that back-feed into the harness.
6. Cite the **boundary to `ai-risk-engineer`** (prerequisite, level 25) — that packet covers garak, PyRIT, and Promptfoo as working red-team suites at general depth; this module adds frontier-depth attack engineering and LLM-vs-LLM attacker loops. Cite the **boundary to mod-103** (jailbreak vs. prompt injection), **mod-108** (guardrails and monitors that this module measures but does not train), and **mod-111** (scaled / automated red-team that industrialises this harness).

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Jailbreak as an Engineering Discipline](01-jailbreak-as-an-engineering-discipline.md) | Define jailbreak (policy override, not principal confusion), draw the boundary against prompt injection (mod-103), pin the four attack families this module owns, and introduce the JEH artefact skeleton and harmful-payload discipline. |
| 02 | [GCG and White-Box Gradient Jailbreaks](02-gcg-white-box-gradient-jailbreaks.md) | Zou et al. (2023) — the differentiable jailbreak objective, adversarial-suffix search, universality (one suffix across prompts), transferability (open-weight-trained suffix hitting closed models), and how to measure transfer rate on your target. |
| 03 | [PAIR and TAP — Black-Box LLM-vs-LLM Attacker Loops](03-pair-and-tap-black-box-attacker-loops.md) | Chao et al. (PAIR, 2023) and Mehrotra et al. (TAP, 2023) — LLM-vs-LLM attacker/target/judge loops against a black-box API, tree-of-attacks search, and the rate-limit / cost / budget engineering that decides whether the loop is shippable. |
| 04 | [Crescendo and Multi-Turn Refusal Erosion](04-crescendo-multi-turn-refusal-erosion.md) | Russinovich et al. (2024) — the multi-turn attack that never asks for the forbidden thing directly; refusal-erosion mechanics, turn-budget engineering, and the reason single-turn evals miss this class entirely. |
| 05 | [Many-Shot, Long-Context, Persona, Low-Resource, and Cipher Jailbreaks](05-many-shot-and-long-context-jailbreaks.md) | Anthropic Many-Shot Jailbreaking (2024) — pattern establishment across hundreds of demos; DAN-style persona hijack; Yong et al.'s low-resource-language attack; Yuan et al.'s CipherChat. The long-context, low-supervision, and encoding-based attack families. |
| 06 | [Benchmark Landscape — HarmBench, JailbreakBench, AIR-Bench, AILuminate, CyberSecEval](06-benchmark-landscape.md) | The five benchmark families this module ships coverage against — their harm taxonomies, their target-model support, their judge protocols, and how they compose into one coverage report rather than five disconnected numbers. |
| 07 | [StrongREJECT-Style Judge Methodology](07-strong-reject-judge-methodology.md) | Souly et al. (2024) — why simple string-match and refusal-word judges over-count ASR; the StrongREJECT rubric; refusal robustness, elicitation gap, and adaptive-attack survival as the three ASR-derived metrics; judge calibration and cost. |
| 08 | [In-the-Wild Jailbreaks and Regression Fixtures](08-in-the-wild-jailbreaks-and-regression-fixtures.md) | Shen et al.'s "Do Anything Now" — the DAN-family evolution, in-the-wild taxonomy dimensions (persona, roleplay, opposite-mode, virtualisation, encoding, competing-objective), and the workflow for translating a fresh in-the-wild jailbreak into a permanent regression fixture. |
| 09 | [Boundary to `ai-risk-engineer`, mod-103, mod-108, mod-111](09-boundary-to-ai-risk-engineer-and-mod-111.md) | The prerequisite-role handoff: what garak / PyRIT / Promptfoo already give you at level 25, what this module adds on top, what mod-108 does with the guardrail measurements this harness produces, and what mod-111 industrialises. |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [GCG adversarial-suffix white-box attack](exercises/exercise-01-gcg-adversarial-suffix-white-box-attack.md) | 3 |
| 02 | [PAIR and TAP black-box attacker loop](exercises/exercise-02-pair-and-tap-black-box-attacker-loop.md) | 3 |
| 03 | [Crescendo multi-turn refusal erosion](exercises/exercise-03-crescendo-multi-turn-refusal-erosion.md) | 2 |
| 04 | [Many-shot and long-context jailbreak](exercises/exercise-04-many-shot-and-long-context-jailbreak.md) | 2 |
| 05 | [HarmBench / JailbreakBench / AIR-Bench / AILuminate / CyberSecEval coverage run](exercises/exercise-05-harmbench-jailbreakbench-air-bench-mlcommons-coverage-run.md) | 3 |
| 06 | [StrongREJECT judge methodology](exercises/exercise-06-strong-reject-judge-methodology.md) | 1 |
| 07 | [In-the-wild jailbreak taxonomy → regression fixtures](exercises/exercise-07-in-the-wild-jailbreak-taxonomy-to-regression-fixtures.md) | 1 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys here. Working jailbreak strings (as opposed to defanged shapes) never live in either repo — see the harmful-payload discipline below and chapter 01.

## Structure

```
mod-104-jailbreak-engineering/
├── 01-…09-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise, categorise, and pin — they do **not** substitute for reading the primary papers. Every chapter cites its primaries; `resources.md` collects them. In particular, before starting the exercises you should have read at least Zou et al. (GCG), Chao et al. (PAIR), Mehrotra et al. (TAP), Russinovich et al. (Crescendo), the Anthropic Many-Shot Jailbreaking post, Souly et al. (StrongREJECT), and Shen et al. ("Do Anything Now"). Chapter 01 is the shortest path in.

Benchmark scores, transfer rates, and public leaderboards move with every model release. Every chapter's number carries a `<!-- needs-research: ... -->` marker where the figure would need re-verification before being cited in an artefact.

## Harmful-payload discipline

Working jailbreak strings — GCG suffixes that transfer to a named production model, PAIR/TAP prompts that hit a named model, Crescendo turn-plans against a named model, in-the-wild DAN variants that still work — are **not** committed to this repo. Illustrative snippets in the chapters are truncated, defanged, or point at published benchmark datasets by reference. The evaluation set lives in an access-controlled artefact store per the pattern mod-103 chapter 06 codifies and mod-111 industrialises, with a manifest committed here. If you catch yourself pasting a working jailbreak into a chapter, exercise, or PR, stop.

This is not a stylistic preference. Public jailbreak strings age fast — a GCG suffix that works on `openai/gpt-something` today will be neutralised by the next safety-tuning pass — but publishing them accelerates the arms race in the wrong direction, and pinning them to a public repo turns your teaching material into an ongoing disclosure. Store the payloads where mod-112's disclosure workflow can reach them; publish only the *shape* and the *rate*.

## What this module does not cover

- **Prompt injection engineering** — direct + indirect injection primitives, the six-primitive taxonomy, obfuscation vectors, and the PIEH — these are **mod-103**'s scope. The refusal-bypass injection primitive lives there; the *policy-level jailbreak construction* lives here. Chapter 01 draws the boundary.
- **Agent tool-abuse chains, memory-poisoning, long-horizon planning subversion, multi-agent adversarial coordination** — these live in **mod-105**. A jailbreak that unlocks a policy-forbidden capability inside an agent then rides a mod-105 chain; this module owns the jailbreak; mod-105 owns the chain.
- **Dangerous-capability evaluations** — CBRN, cyber, autonomous-replication, R&D-uplift — these live in **mod-106**. The judge and harness patterns here inform the dangerous-capability evals, but the specific benchmarks (WMDP, CyberSecEval hardening, METR RE-Bench, SWE-bench) are mod-106's material.
- **Classifier-guard training, constitutional-classifier training, monitor design** — these are **mod-108**'s scope. This module *measures* guardrails and monitors as one row in the benchmark coverage report; mod-108 *trains* them.
- **Automated / scaled red-team, LLM-vs-LLM at production scale, judge industrialisation across a fleet of models** — these are **mod-111**'s scope. This module builds the minimum-viable attacker loops (PAIR, TAP, Crescendo) at engineer-hands-on-keyboard depth; mod-111 turns them into a scheduled, sharded, cost-managed pipeline.
- **Safety cases and disclosure workflow** — these are **mod-109** and **mod-112**. The JEH's outputs are inputs to those artefacts; the artefacts themselves are not authored here.
- **Reusable red-team suites at general depth** — garak, PyRIT, Promptfoo — these are the **`ai-risk-engineer`** (level 25) prerequisite. Chapter 09 codifies what the prerequisite covers and what this module adds on top; do not re-derive the prerequisite here.

## What this module produces

A **Jailbreak Evaluation Harness (JEH)** for one concrete target with:

- A **coverage matrix** over attack family × harm category × judge, spanning HarmBench, JailbreakBench, AIR-Bench, AILuminate, and CyberSecEval, with per-cell ASR, refusal-robustness, elicitation gap, and adaptive-attack survival.
- A **white-box attacker** (GCG suffix trainer) that runs against your open-weight target, plus a **transfer-rate measurement** against the closed-weight targets in scope.
- A **black-box attacker loop** (PAIR + TAP) with an attacker/target/judge configuration, cost / rate-limit envelope, and stopping-criterion policy.
- A **multi-turn attacker** (Crescendo) with turn-budget engineering and refusal-erosion tracking per turn.
- A **many-shot / long-context / persona / low-resource / cipher** attack set, exercised at the target's declared context length.
- A **StrongREJECT-style judge** with a versioned rubric, a calibrated human-agreement number, and a per-run cost report.
- A **regression fixture library** back-fed from Shen et al.'s taxonomy and any new in-the-wild jailbreak the team encounters.
- A **finding feed** to mod-108 (which guardrails caught what), mod-111 (which attacker loops to scale), and mod-112 (which findings warrant coordinated disclosure).
