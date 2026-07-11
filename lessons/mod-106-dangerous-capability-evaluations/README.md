# mod-106 — Dangerous-Capability Evaluation Engineering

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 20 hours (≈4 hours lecture reading + 16 hours exercises)
**Prerequisites:** [mod-101 (Frontier Safety Frameworks)](../mod-101-frontier-safety-frameworks-and-position/README.md), [mod-102 (Agent Threat Modelling)](../mod-102-agent-threat-modelling/README.md), [mod-103 (Prompt-Injection Engineering)](../mod-103-prompt-injection-engineering/README.md), [mod-104 (Jailbreak Engineering)](../mod-104-jailbreak-engineering/README.md), and [mod-105 (Agent Attack Surface)](../mod-105-agent-attack-surface-engineering/README.md). The level-25 `ai-risk-engineer` prerequisite for the general adversarial-ML craft is assumed.

## Why this module exists

Every frontier safety framework — Anthropic's RSP, OpenAI's Preparedness Framework, Google DeepMind's Frontier Safety Framework — is anchored on a single engineering question: *"has this model, under best-effort elicitation, crossed the capability threshold at which a policy-tier's mitigations must engage?"* Mods 101–105 taught you to shape the surface, engineer injections and jailbreaks, and chain agent attacks. This module teaches the discipline that decides whether the model *ships*, whether it ships with additional mitigations, or whether the frontier lab *rolls back*.

A dangerous-capability evaluation is not a benchmark. A benchmark reports "the model scored X." A dangerous-capability evaluation reports "under a pre-registered elicitation protocol with an accounted-for capability-elicitation gap, our best-effort ceiling on the model's ability to *uplift a novice attacker to competent CBRN synthesis / to complete a chained exploit-writing task / to reproduce an ML-R&D advancement autonomously* is below (or above) the threshold for RSP ASL-N / Preparedness High / FSF CCL-M, and the pre-registered rollback trigger is (not) engaged." Every clause of that sentence is engineered.

The engineering artefact you author from this module is a **Dangerous-Capability Evaluation Report (DCER)** for one concrete frontier-model deployment: an elicitation protocol, per-capability elicitation-gap accounting, per-capability best-effort ceiling with confidence intervals, per-capability pre-registered threshold and rollback trigger, and the deferral contract to `model-evaluation-engineer` (peer, level 30) on the statistical calibration and to the external-expert graders on the domain adjudication.

## Learning objectives

By the end of the module you can:

1. Design a **dangerous-capability elicitation protocol** — capability-elicitation gap accounting, best-of-N sampling with confidence intervals, fine-tune-elicitation, tool-use elicitation, scaffolding elicitation, and pre-registered thresholds against RSP ASLs / Preparedness tiers / FSF Critical Capability Levels.
2. Engineer **CBRN-uplift evaluations** — biological, chemical, radiological, nuclear — grounded in the **WMDP** benchmark (Li et al., 2024), the **Frontier Model Forum bio threat model** and FMF Task Force publications; run under proper security controls with external CBRN-domain-expert graders, correctly-sanitised harmful-payload handling, and coordination with **IAEA** / **WHO** domain expertise.
3. Engineer **cyber-offense capability evaluations** — Meta **CyberSecEval 2** and **CyberSecEval 3**, capture-the-flag suites, exploit-writing tasks, MITRE **ATT&CK**-tagged autonomy tasks — reporting the capability level, the autonomy level, and the defensive delta against unassisted attacker baselines.
4. Engineer **autonomous-replication and long-horizon autonomy evaluations** — the **METR** public task suite, **RE-Bench** for AI-R&D uplift, **SWE-bench Verified** for software-engineering autonomy — including autonomy success-rate scaling curves and elicitation-gap-accounted best-effort ceilings.
5. Engineer **persuasion / manipulation evaluations** and **self-exfiltration evaluations** at the working depth published in the Claude, o1, and Gemini system cards, with paired human-baseline controls and refusal-vs-capability separation.
6. Author a **Dangerous-Capability Evaluation Report (DCER)** shaped for RSP / Preparedness / FSF review — pre-registered threshold, elicitation methodology, best-effort ceiling with CI, residual-gap claim, and rollback trigger — and cite the **boundary to `model-evaluation-engineer`** (peer, level 30) on the statistical calibration methodology (best-of-N CI, elicitation-gap accounting) and the **boundary to the external CBRN- / cyber- / R&D-domain expert graders** on domain adjudication.

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Dangerous-capability elicitation as an engineering discipline](01-elicitation-protocol-engineering.md) | Define the elicitation protocol as the unit of study, the capability-elicitation gap as the load-bearing uncertainty, best-of-N + fine-tune + scaffolding + tool-use as the four elicitation axes, and pre-registration against RSP ASL / Preparedness tier / FSF CCL as the reporting contract. |
| 02 | [CBRN-uplift evaluation engineering](02-cbrn-uplift-evaluation.md) | Bio / chem / rad / nuc uplift, the WMDP benchmark and its unlearning-eval companion, the FMF bio threat model, security controls (need-to-know, air-gapped grading, expert-only payload handling), external domain-expert graders, and coordination with IAEA / WHO domain expertise. |
| 03 | [Cyber-offense capability evaluation engineering](03-cyber-offense-evaluation.md) | CyberSecEval 2 (autonomous cyber-offense + prompt-injection risk), CyberSecEval 3 (autonomous cyber-attack + spear-phishing + exploit generation), CTF suites (Meta's CTF, DEF CON-shaped tasks), exploit-writing tasks, MITRE ATT&CK-tagged autonomy tasks, and reporting the capability × autonomy × defensive-delta triple. |
| 04 | [Autonomous-replication and long-horizon autonomy evaluation](04-autonomy-evaluation.md) | The METR public task suite (task-length-in-human-hours as the load-bearing metric), RE-Bench for AI-R&D uplift, SWE-bench Verified for software-engineering autonomy, and autonomy-success-rate scaling curves as the evidence artefact for autonomous-replication threat models. |
| 05 | [Persuasion, manipulation, and self-exfiltration evaluations](05-persuasion-and-self-exfiltration.md) | System-card-depth persuasion / manipulation evals (charge-of-mind, MakeMePay, MakeMeSay, ChangeMyView-shaped), the o1 / Claude / Gemini self-exfiltration probe designs, refusal-vs-capability separation, and paired human-baseline calibration. |
| 06 | [Authoring the Dangerous-Capability Evaluation Report (DCER)](06-dangerous-capability-report.md) | The DCER artefact: pre-registered threshold, elicitation methodology, best-effort ceiling with CI, residual-gap claim, rollback trigger, and the RSP / Preparedness / FSF-shaped disclosure formatting. Deferral contract embedded. |
| 07 | [Boundaries — `model-evaluation-engineer` and the external domain-expert graders](07-boundaries-to-model-evaluation-engineer.md) | The peer-role handoff: `model-evaluation-engineer` (peer, level 30, ML Engineering family) owns the statistical calibration methodology (best-of-N CI, item-response theory, judge calibration). External CBRN- / cyber- / R&D-domain expert graders own domain adjudication. Also codifies the boundary to mod-110 (control evaluations) and mod-112 (disclosure). |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [Elicitation protocol design drill](exercises/exercise-01-elicitation-protocol-design-drill.md) | 3 |
| 02 | [WMDP CBRN uplift eval under controls](exercises/exercise-02-wmdp-cbrn-uplift-eval-under-controls.md) | 3 |
| 03 | [CyberSecEval cyber-offense run](exercises/exercise-03-cyberseceval-cyber-offense-run.md) | 3 |
| 04 | [METR + SWE-bench Verified autonomy eval](exercises/exercise-04-metr-and-swe-bench-autonomy-eval.md) | 3 |
| 05 | [RE-Bench AI-R&D capability run](exercises/exercise-05-re-bench-ai-rd-capability-run.md) | 2 |
| 06 | [Persuasion / manipulation / self-exfiltration eval](exercises/exercise-06-persuasion-manipulation-and-self-exfiltration-eval.md) | 2 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys here. **Working CBRN payloads, exploit-writing outputs, and elicited self-exfiltration transcripts** — as opposed to defanged shapes and metadata — never live in either repo. See the harmful-payload discipline below and chapter 01.

## Structure

```
mod-106-dangerous-capability-evaluations/
├── 01-…07-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise and map — they do **not** substitute for reading the primary papers, benchmark repos, and system-card sections. Every chapter cites its primaries; `resources.md` collects them. In particular, before completing this module you should have read (or re-read):

- The **RSP** (Anthropic), the **Preparedness Framework** (OpenAI), and the **FSF** (Google DeepMind) — at the sections that define the capability tiers this module measures against. mod-101 chapters 02–04 are the on-ramp.
- The most recent frontier-model **system cards** (Claude, o1 / OpenAI reasoning models, Gemini) — specifically the dangerous-capability-evaluation sections. Model capability moves; the shape of the eval sections is what to read for.
- The **WMDP** paper (Li et al., 2024), the **METR** research posts on task-length-in-hours and elicitation-gap methodology, the **RE-Bench** paper, the **SWE-bench Verified** dataset write-up, and the **Meta CyberSecEval 2 / 3** technical reports.
- The **Frontier Model Forum** working papers on bio evaluations, cyber evaluations, and autonomous-replication evaluations, and the **AISI** (UK + US) pre-deployment evaluation reports for cross-lab methodology.

Benchmark scores, task-length curves, and system-card numbers move with every model and framework release. Every chapter's number carries a `<!-- needs-research: ... -->` marker where a specific figure would need re-verification before being cited in a DCER.

## Harmful-payload discipline

Working CBRN synthesis instructions, weaponisable exploit chains, working self-exfiltration prompts, and elicited harmful transcripts against production frontier models are **not** committed to this repo. Illustrative snippets in the chapters are truncated, defanged, or point at published benchmark cases by reference (WMDP question IDs, CyberSecEval case IDs, METR task IDs). The evaluation set lives in an access-controlled artefact store per the pattern mod-103 chapter 06 codifies and mod-111 industrialises, with a manifest committed here.

For CBRN in particular, the store's access controls are tighter than the rest of the track: the access log routes to the frontier-lab's biosecurity / cybersecurity review board (per FMF working-paper guidance), the elicited-payload retention window is bounded, external graders receive payloads through a need-to-know channel and grade offline, and the mod-112 disclosure workflow governs any external references. If you catch yourself pasting a working synthesis pathway, a working exploit chain, or a working self-exfiltration prompt into a chapter, exercise, or PR, stop — and route to mod-112.

The reasoning is the same as in mod-103, mod-104, and mod-105, escalated: the CBRN / cyber / autonomy findings this module produces are directly load-bearing for regulator disclosures under the EU AI Act Article 55 systemic-risk regime and the RSP / Preparedness / FSF public commitments, and publishing them in the wrong channel is both a safety failure and a regulatory failure. Publish the *shape*, the *rate*, and the *threshold verdict*; store the payloads where mod-112's disclosure workflow can reach them.

## What this module does not cover

- **General adversarial-ML evaluation** — ART, TextAttack, robustness benchmarks at general depth — this is the level-25 `ai-risk-engineer` prerequisite's scope. This module *composes* with those tools where relevant (e.g., ART for adversarial-perturbation-elicited uplift) but does not re-derive them.
- **Application-layer LLM eval plumbing** — traces, judges, RAG evals, LLM-vs-LLM CI/CD wiring — this is the level-30 `ai-eval-engineer` (AI Engineering family) peer role's scope. This module *consumes* judge frameworks and eval-run harnesses but does not engineer the plumbing.
- **Statistical calibration methodology at frontier depth** — best-of-N confidence intervals, item-response theory, judge-vs-human calibration bounds, elicitation-gap CI derivation — this is the level-30 `model-evaluation-engineer` (ML Engineering family) peer role's scope. This module *cites* the calibration and *reports* the CI; the derivation methodology defers to the peer role. Chapter 07 codifies the boundary.
- **AI control and adversarial-alignment evaluation** — deception, alignment-faking, sandbagging, sleeper-agent, sabotage — these are **mod-110**'s scope. Dangerous-capability evaluation measures *what the model can do under best-effort elicitation*; AI control evaluation measures *whether we can rely on the model's expressed inability under adversarial conditions*. Chapter 07 draws the boundary.
- **Automated / scaled red-team industrialisation** — LLM-vs-LLM attacker loops, fine-tuned adversarial attacker models at production scale, StrongREJECT-shaped judging — these are **mod-111**'s scope. This module *may use* mod-111 primitives inside its elicitation protocol (fine-tuning an attacker to elicit CBRN-uplift maximum), but the scaled-attacker plumbing lives there.
- **Safety-case authoring in GSN** — the *inability* argument this module's ceiling supports is one leg of an **mod-109** safety case; this module produces the evidence, mod-109 authors the argument.
- **Serious-incident response and disclosure workflow** — EU AI Act Article 73, RSP / Preparedness / FSF public-disclosure formatting, coordinated release — these are **mod-112**'s scope. The DCER is a load-bearing input to the disclosure artefact; the disclosure workflow itself lives there.
- **Regulatory-legal opinion.** The DCER is engineering evidence, not a legal opinion. Legal / policy counsel authors the regulator-facing legal posture and signs on whether the ceiling clears a given jurisdiction's Article 55 or equivalent threshold. You feed them.

## What this module produces

A **Dangerous-Capability Evaluation Report (DCER)** for one concrete frontier-model deployment, with:

- An **elicitation protocol** — pre-registered ahead of the evaluation, versioned, and signed off — covering best-of-N sampling budget, fine-tune-elicitation contract, tool-use elicitation contract, scaffolding elicitation contract, and the human-hours of expert elicitation effort applied.
- A **capability-elicitation gap accounting** — per-capability, with the residual-gap claim ("we believe our ceiling underestimates the elicitable capability by no more than δ under a follow-up effort of E") — cited to the `model-evaluation-engineer` peer role's derivation.
- A **CBRN-uplift capability panel** — WMDP-derived unlearning-eval-linked measurement, FMF-bio-threat-model-linked hostile-actor uplift measurement, and expert-grader-adjudicated end-to-end synthesis-planning uplift measurement, under the module's security controls.
- A **cyber-offense capability panel** — CyberSecEval 2 + 3 rows, CTF task success under an autonomy axis, exploit-writing task success under a category axis, MITRE ATT&CK-tagged autonomy-task success — with paired unassisted-human baselines.
- An **autonomy capability panel** — METR public task suite success as a function of task-length-in-human-hours, RE-Bench AI-R&D uplift results, SWE-bench Verified pass rate — with an autonomy scaling curve and an elicitation-gap-accounted ceiling.
- A **persuasion / manipulation panel** and a **self-exfiltration panel** — at system-card depth — with paired human baselines and refusal-vs-capability separation.
- Per-capability **pre-registered thresholds** against the RSP ASL / Preparedness tier / FSF CCL the deployment is assessed against.
- Per-capability **rollback triggers** — the "if the ceiling crosses the threshold, this control engages" clause — mapped to the mod-112 disclosure workflow and the deployment's safety-case (mod-109).
- A **deferral contract** to `model-evaluation-engineer` on statistical methodology and to the external CBRN- / cyber- / R&D-domain expert graders on domain adjudication.
