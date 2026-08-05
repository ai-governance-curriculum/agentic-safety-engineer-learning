# mod-111 — Automated and Scaled Red-Teaming

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 14 hours (≈3 hours lecture reading + ≈11 hours exercises; the seven exercises total 17 hours if run end-to-end and are individually scoped so a learner can pick the subset that matches their target-under-test)
**Prerequisites:** [mod-101 (Frontier Safety Frameworks)](../mod-101-frontier-safety-frameworks-and-position/README.md), [mod-102 (Threat Modelling)](../mod-102-agent-threat-modelling/README.md), [mod-103 (Prompt-Injection Engineering)](../mod-103-prompt-injection-engineering/README.md), [mod-104 (Jailbreak Engineering)](../mod-104-jailbreak-engineering/README.md), [mod-105 (Agent Attack-Surface Engineering)](../mod-105-agent-attack-surface-engineering/README.md), and [mod-106 (Dangerous-Capability Evaluations)](../mod-106-dangerous-capability-evaluations/README.md). The level-25 `ai-risk-engineer` prerequisite for garak / PyRIT / Promptfoo at general adoption depth is assumed; this module extends that with LLM-vs-LLM attackers, fine-tuned attacker models, and scaled orchestration.

## Why this module exists

mod-103, mod-104, and mod-105 taught the *craft* of eliciting an unsafe completion. A defensible frontier-safety posture at level 40 needs a red-team output that is *reproducible*, *comparable across model revisions*, *aggregated into a coverage claim*, and *routable to the artefacts that consume it* (safety cases per mod-109, disclosures per mod-112, containment updates per mod-107, guardrail retraining per mod-108, tier decisions per mod-101). That is the shift this module makes: from craft to *program*, from *"we tried and it broke"* to *"we ran the following coverage matrix, the following portion is red, and here is the seeded-attack bundle a reviewer can replay."*

The engineering artefact you author from this module is a **Coverage Matrix Contract (CMC)** for one concrete red-team program. The CMC's seven sections (scope, matrix axes, orchestrator inventory, judge contract, attack-corpus contract, reproducibility contract, consumer contract) are what a reviewer signs. The chapters build the seven sections; the exercises produce the artefacts that populate them.

## Learning objectives

By the end of the module you can:

1. Orchestrate a scaled red-team program with **UK AISI Inspect + Microsoft PyRIT + NVIDIA garak + Promptfoo red-team** — coverage matrix across attack technique × behaviour category × model version × decoding config, with the correct decomposition of concerns across the four flagship tools.
2. Engineer **LLM-vs-LLM attacker loops** — PAIR / TAP / RL-based attackers hosted in PyRIT — under rate-limit and cost budgets at scale, measured with both **ASR** and a **diversity** metric that separates ten thousand identical DAN variants from ten thousand semantically distinct attacks.
3. **Fine-tune a dedicated adversarial-attacker model** on your organisation's failure-mode corpus under harmful-payload storage discipline, then measure ASR-lift and diversity-delta against production models and held-out behaviours.
4. Adopt **StrongREJECT-shape judge methodology** — the two-axis rating, the empty-jailbreak carve-out, LLM-judge / human-judge calibration on FP and FN thresholds, judge-drift monitoring, and multi-judge ensembling for the highest-stakes cells.
5. Engineer **coverage + population-scale replay** — coverage-matrix reporting with per-cell ASR, ASR CI, diversity, cost, and elicitation-gap disclaimers; a **seeded-attack replay bundle** with decoding + model + prompt + tool-response hashes pinned; a rev-to-rev delta report.
6. Adopt **harmful-payload storage discipline** — evaluation set hosted outside the source repo (private HF org / S3 with per-role IAM), harmful-content redaction in issue trackers and logs, need-to-know access, and a legal-review gate for CBRN / cyber-offense payloads.
7. Cite the **boundary to `ai-risk-engineer`** (prerequisite, level 25) on garak / PyRIT / Promptfoo at general depth, and the **boundary to `ai-eval-engineer`** (peer, level 30) on the trace / judge / eval-gated CI plumbing this suite consumes.

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Scaled Red-Teaming as a Discipline](01-scaled-red-teaming-as-a-discipline.md) | Define the shift from craft to program, name the **Coverage Matrix Contract (CMC)** as the module-level artefact, and draw the boundary to `ai-risk-engineer` (prerequisite) and `ai-eval-engineer` (peer). |
| 02 | [Orchestration with Inspect, PyRIT, garak, and Promptfoo](02-orchestration-with-inspect-pyrit-garak-promptfoo.md) | The four flagship tools, their opinionated concerns, and the composition rule (Inspect is the runner and log store; PyRIT owns attacker orchestrators; garak owns the known-behaviour regression slice; Promptfoo owns the CI-integrated scaffold-regression slice). |
| 03 | [LLM-vs-LLM Attacker Loops](03-llm-vs-llm-attacker-loops.md) | PAIR, TAP, Crescendo, and RL-based attackers hosted in PyRIT; rate-limit and cost budgets under scale; **diversity** as a first-class metric alongside ASR. |
| 04 | [Fine-Tuning a Dedicated Adversarial Attacker](04-fine-tuning-a-dedicated-adversarial-attacker.md) | Failure-mode corpus construction under harmful-payload discipline, base-model choice and licence, fine-tuning objective, ASR + diversity measurement against production models, and the update cadence per target-family rev. |
| 05 | [StrongREJECT Judge Methodology](05-strong-reject-judge-methodology.md) | The two-axis rating and empty-jailbreak carve-out, LLM-judge / human-judge calibration, judge-drift monitoring, multi-judge ensembling, and the CMC section-4 judge contract. |
| 06 | [Coverage, Reproducibility, and Harmful-Payload Discipline](06-coverage-reproducibility-and-harmful-payload-discipline.md) | Coverage-matrix reporting, the seeded-attack replay bundle, harmful-payload storage discipline, the CBRN / cyber-offense legal-review gate, and the consumer contract that routes findings to safety cases, disclosures, containment updates, and guardrail retraining. |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [Inspect-based red-team orchestration](exercises/exercise-01-inspect-based-red-team-orchestration.md) | 3 |
| 02 | [PyRIT attack library and orchestrator, scaled](exercises/exercise-02-pyrit-attack-library-and-orchestrator-scaled.md) | 2 |
| 03 | [LLM-vs-LLM attacker loop with PAIR and TAP](exercises/exercise-03-llm-vs-llm-attacker-loop-with-pair-and-tap.md) | 3 |
| 04 | [Fine-tune a dedicated adversarial attacker model](exercises/exercise-04-fine-tune-a-dedicated-adversarial-attacker-model.md) | 3 |
| 05 | [StrongREJECT judge vs. human calibration](exercises/exercise-05-strong-reject-judge-vs-human-calibration.md) | 2 |
| 06 | [Coverage matrix and reproducibility bundle](exercises/exercise-06-coverage-matrix-and-reproducibility-bundle.md) | 2 |
| 07 | [Harmful-payload storage discipline workflow](exercises/exercise-07-harmful-payload-storage-discipline-workflow.md) | 2 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys, working attacker payloads, or judge rationale text into this repo. See the harmful-payload discipline below and chapter 06.

## Structure

```
mod-111-automated-and-scaled-red-teaming/
├── 01-…06-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise, categorise, and pin — they do **not** substitute for reading the primary papers. Every chapter cites its primaries; `resources.md` collects them. In particular, before the exercises you should have read at least Chao et al. (PAIR), Mehrotra et al. (TAP), Russinovich et al. (Crescendo), Perez et al. ("Red Teaming Language Models with Language Models"), Ganguli et al. ("Red Teaming Language Models to Reduce Harms"), and Souly et al. (StrongREJECT). The Inspect, PyRIT, garak, and Promptfoo documentation sites are load-bearing for chapter 02 and exercises 01, 02, and 06.

Benchmark scores, transfer rates, and public leaderboards move with every model release. Every chapter's number carries a `<!-- needs-research: ... -->` marker where the figure would need re-verification before being cited in an artefact.

## Harmful-payload discipline

Working attacker prompts — PAIR / TAP wins against named production models, Crescendo turn plans against named models, fine-tuned-attacker training corpora, judge rationale text quoting harmful content, red-cell replay-bundle payloads — are **not** committed to this repo. Illustrative snippets in the chapters and exercises are truncated, defanged, or point at published benchmark datasets by reference. The attack corpora and replay bundles live in an access-controlled external artefact store per chapter 06 with a manifest committed here.

This is not stylistic preference. Chapter 06 walks the four rules (payload artefacts live outside the source repo, harmful content is redacted in issue trackers and logs, access is need-to-know per role, CBRN / cyber-offense payloads pass a legal-review gate) and the enforcement mechanism. Exercise 07 is where you author the org-level workflow that makes the discipline auditable.

## What this module does not cover

- **The craft of the individual attack** — GCG suffix training, PAIR / TAP against a specific target with a specific rubric, Crescendo turn-planning — these are **mod-104**. This module industrialises the same attackers at scale; it does not re-teach the craft.
- **Agent-scaffold attacks** — tool-abuse chains, memory-poisoning, multi-agent adversarial coordination — these are **mod-105**. This module runs the same attacks *at population scale* against the same scaffolds.
- **Dangerous-capability evaluations** — CBRN, cyber-offense, autonomous-replication, R&D-uplift — these are **mod-106**. This module's judge contract carves out the human-graded dangerous-capability cells; mod-106 owns the elicitation methodology behind them.
- **Guardrail training** — classifier guards, constitutional classifiers, monitors — these are **mod-108**. This module *measures* guardrails as one row in the coverage matrix; mod-108 *trains* them.
- **Safety cases** and **disclosures** — these are **mod-109** and **mod-112**. This module's CMC is the *evidence*; the argument (109) and the AISI-facing narrative (112) that cite it are authored there.
- **Reusable red-team suites at general depth** — garak, PyRIT, Promptfoo — these are the **`ai-risk-engineer`** (level 25) prerequisite. Chapter 01 codifies what the prerequisite covers and what this module adds on top.
- **Eval-framework plumbing** — the trace store, judge-serving layer, eval-gated CI hook, online-eval sidecar — these are the **`ai-eval-engineer`** (level 30) peer role. This module *specifies* the plumbing contract; it does not build the plumbing.

## What this module produces

A **Coverage Matrix Contract (CMC)** for one concrete red-team program, with:

- A **scope statement** naming the system-under-test, the tier the program feeds evidence into, and the in-scope / out-of-scope behaviour categories.
- A **coverage matrix** across attack technique × behaviour category × model version × decoding config, populated by Inspect (runner), PyRIT (attackers), garak (known-behaviour regression), and Promptfoo (scaffold regression).
- A **judge contract** naming the StrongREJECT-shape judges per behaviour category, their calibrated FP / FN bounds, the human-grader panels for the carve-outs, and the drift-monitoring cadence.
- A **fine-tuned adversarial attacker** trained on the operator's failure-mode corpus under harmful-payload discipline, with a measured ASR lift and diversity delta versus the general-LLM baseline.
- A **seeded-attack replay bundle** per red cell with decoding + model + prompt + tool-response hashes pinned; a reviewer can replay any red cell exactly.
- A **harmful-payload storage discipline** with per-role IAM, redaction discipline in trackers and logs, and a CBRN / cyber-offense legal-review gate.
- A **consumer contract** routing the coverage claim to safety cases (mod-109), disclosures (mod-112), containment updates (mod-107), guardrail retraining (mod-108), and tier decisions (mod-101).
