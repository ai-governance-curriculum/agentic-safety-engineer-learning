# mod-110-control-and-deception-evaluation: AI Control, Deception, and Adversarial-Alignment Evaluation

**Estimated effort:** 16 hours

## What this module is

Adversarial-alignment evaluation is the discipline of producing defensible credibility claims about a target model's observable behaviour when the model itself may be running a strategy against the evaluation. It is the module that inverts mod-106's assumption: mod-106 measures what a model *can* do under best-effort elicitation; mod-110 measures whether that measurement can be *trusted*, when the target is assumed capable of hiding capability, feigning alignment, or subverting the very oversight the eval is grounded in.

The evaluation families are named and productionised in the reference frontier-lab literature: **Apollo Research** deception probes, **Anthropic + Redwood** alignment faking, **Greenblatt et al.** password-locked-capability sandbagging, **Anthropic** sleeper agents, **Apollo** in-context scheming, **Anthropic** sabotage evaluations, and the **Redwood** AI-control paradigm. This module productionises those methodologies as operator-scope evaluation pipelines, with the residual-uncertainty accounting that feeds mod-106's DCER, mod-107's EACC, mod-108's monitor calibration, mod-109's safety case, mod-111's coverage matrix, and mod-112's disclosure workflow.

The role's craft at level 40 is the **productionisation** — the run, the instrumentation, the reproducibility discipline, the reporting shape, the peer-role handoff. Chapter 09 pins the boundary to the frontier-lab AI-safety-research-scientist ladder that owns the *derivation*: this role consumes their theoretical work and productionises evaluations; novel-methodology publication is out of scope.

## Learning objectives

- Engineer deception evaluations — Apollo Research methodology (self-reasoning tests, hidden-scratchpad probes, in-context deception, honesty probes) — running them on frontier models and reporting the deception-rate delta across model versions and safety-tuning stages.
- Engineer alignment-faking evaluations — Anthropic + Redwood "Alignment Faking in Large Language Models" methodology, differential-behaviour tests, situational-awareness probes.
- Engineer sandbagging evaluations — measure the capability-elicitation gap where the model performs worse than it can; scratchpad / password-locked-capability elicitation; reason about detection under adversarial elicitation.
- Engineer sleeper-agent / trojan evaluations — Anthropic Sleeper Agents methodology, backdoor-persistence-through-safety-tuning tests, trigger discovery.
- Engineer in-context scheming evaluations — Meinke et al. methodology; run agentic-scheming probes with instrumented reasoning traces.
- Engineer sabotage evaluations — Anthropic sabotage-evals methodology (human-oversight sabotage, code-sabotage, decision-undermining, sandbagging-under-oversight).
- Adopt the AI-control paradigm (Redwood) — safety despite an intentionally adversarial model — designing control-eval protocols, red-team-blue-team games, honeypot-tasks, and untrusted-monitoring baselines.
- Author model-organisms of misalignment for controlled study — pick a concrete misaligned-model-organism construction, elicit it, characterise it, and hand off the result to `fine-tuning-engineer` (peer, level 30) for safety-tuning intervention.
- Cite the boundary to the frontier-lab AI-safety-research-scientist ladder (out of scope) — this role consumes their theoretical work and productionises evaluations; novel-methodology publication is out of scope.

## Chapters

1. [`01-adversarial-alignment-evaluation-as-a-discipline.md`](01-adversarial-alignment-evaluation-as-a-discipline.md) — what adversarial-alignment evaluation *is* (a credibility claim under a strategically-adversarial model assumption), what it is *not* (a capability eval, a refusal-rate measurement, a jailbreak, an alignment leaderboard), the four load-bearing primitives (instrumented traces, differential-behaviour probes, honeypot tasks, blind grader panels), and the elicitation-gap accounting inherited from mod-106 and widened under adversarial elicitation.
2. [`02-deception-evaluation-apollo-methodology.md`](02-deception-evaluation-apollo-methodology.md) — Apollo Research's four deception-probe classes: **self-reasoning tests**, **hidden-scratchpad probes**, **in-context deception probes**, **honesty probes**. Trace-instrumentation contract, judge design, and the deception-rate delta report shape.
3. [`03-alignment-faking-evaluations.md`](03-alignment-faking-evaluations.md) — Greenblatt et al. paired-framing (training vs deployment) methodology, the situational-awareness prerequisite, reasoning-trace analysis for compliance-gap detection, and the compliance-gap report.
4. [`04-sandbagging-and-capability-elicitation-gap.md`](04-sandbagging-and-capability-elicitation-gap.md) — sandbagging as strategic under-performance; three primitives (**scratchpad elicitation**, **password-locked-capability elicitation**, **fine-tune elicitation**); the sandbagging report as the third layer of mod-106's elicitation-gap accounting.
5. [`05-sleeper-agent-and-trojan-evaluations.md`](05-sleeper-agent-and-trojan-evaluations.md) — Hubinger et al. Sleeper Agents methodology; **model-organism construction** (behaviour-shift and capability-relevant organisms); **persistence-through-safety-tuning** measurement; **trigger discovery** (black-box, white-box / activation-based, adversarial-elicitation approaches); the report as a regression fixture.
6. [`06-in-context-scheming-evaluations.md`](06-in-context-scheming-evaluations.md) — Meinke et al. six probe classes (**self-exfiltration**, **goal-guarding**, **oversight-tool-subversion**, **sandbagging-in-scheming**, **deceptive-alignment-in-scheming**, **opportunistic-lying**); agent-trace instrumentation at scheming scope; distinguishing scheming from role-play from artefact-of-eval.
7. [`07-sabotage-evaluations.md`](07-sabotage-evaluations.md) — Benton et al. four families (**human-oversight sabotage**, **code sabotage**, **decision-undermining**, **sandbagging-under-oversight**); the **attempt-vs-success** distinction; model-organism calibrated baselines; production-scale eval design and the naturalism-vs-blinding trade-off.
8. [`08-ai-control-paradigm-and-model-organisms.md`](08-ai-control-paradigm-and-model-organisms.md) — Redwood AI-control paradigm; the five-component control-eval protocol (task, adversary model, operator posture, red team, scoring); honeypot tasks and untrusted-monitoring baselines; model-organism handoff to `fine-tuning-engineer` for safety-tuning intervention.
9. [`09-boundaries-and-handoffs.md`](09-boundaries-and-handoffs.md) — the boundary to the frontier-lab AI-safety-research-scientist ladder (out of scope) and the peer-role handoff map (`fine-tuning-engineer`, `ai-eval-engineer`, `model-evaluation-engineer`, `senior-agentic-ai-engineer`, `ai-infra-security`, `senior-ai-governance-architect`); the downstream cite map into mod-102 / 106 / 107 / 108 / 109 / 111 / 112.

## Exercises

The exercises step up in scope from a single-family behavioural drill (exercises 01–02) through capability-elicitation drills (exercise 03), latent-behaviour and agentic-scope evaluation (exercises 04–06), the control-paradigm capstone (exercise 07), and finally a model-organism construction with an explicit peer-role handoff (exercise 08). Complete them in order; exercise 07 assumes at least one of exercises 01–06 has produced report artefacts it composes over.

1. [`exercises/exercise-01-apollo-style-deception-eval-hands-on.md`](exercises/exercise-01-apollo-style-deception-eval-hands-on.md) — Run all four Apollo probe classes on at least two target-model versions, capture reasoning traces, judge with a paired-blind rubric, and emit a chapter-02 deception-rate delta report.
2. [`exercises/exercise-02-alignment-faking-eval-hands-on.md`](exercises/exercise-02-alignment-faking-eval-hands-on.md) — Reproduce a scoped variant of Greenblatt et al.'s paired-framing probe on a candidate target; measure the compliance gap with a situational-awareness prerequisite section; emit a chapter-03 compliance-gap report.
3. [`exercises/exercise-03-sandbagging-and-elicitation-gap-drill.md`](exercises/exercise-03-sandbagging-and-elicitation-gap-drill.md) — Run scratchpad-elicitation and (behavioural-only, or peer-role-partnered) password-locked / fine-tune-elicitation primitives on a capability panel; emit a chapter-04 sandbagging report that a mod-106 DCER can cite.
4. [`exercises/exercise-04-sleeper-agent-persistence-and-trigger-discovery.md`](exercises/exercise-04-sleeper-agent-persistence-and-trigger-discovery.md) — Author a sleeper-agent organism specification, characterise persistence through a specified safety-tuning pass (peer-role-partnered), and run black-box + activation-based trigger discovery on a peer's unknown-trigger organism.
5. [`exercises/exercise-05-in-context-scheming-eval.md`](exercises/exercise-05-in-context-scheming-eval.md) — Reproduce the Meinke et al. six-probe scheming suite at agentic scope with instrumented traces; emit a chapter-06 scheming-probe report with rubric-decomposed engagement rates.
6. [`exercises/exercise-06-sabotage-eval-under-oversight.md`](exercises/exercise-06-sabotage-eval-under-oversight.md) — Run at least two of Benton et al.'s four families (family 4 mandatory), with model-organism calibrated baselines and paired attempt-vs-success reporting; emit a chapter-07 sabotage-evaluation report.
7. [`exercises/exercise-07-ai-control-protocol-red-blue-game.md`](exercises/exercise-07-ai-control-protocol-red-blue-game.md) — Design a chapter-08 control-eval protocol on a specific task family, run a red-team-vs-blue-team game with an untrusted-monitoring baseline, and score the safety-usefulness Pareto point.
8. [`exercises/exercise-08-model-organism-construction-and-handoff.md`](exercises/exercise-08-model-organism-construction-and-handoff.md) — Author a full model-organism specification, run the construction handoff to `fine-tuning-engineer` (real or stubbed), characterise the delivered organism, and route the finding to the peer role for safety-tuning intervention.

## Structure

- `01-…md` … `09-…md`: lecture chapters.
- `exercises/`: per-exercise prompts (this repo). Solutions live in the paired `agentic-safety-engineer-solutions` repo.
- `labs/`: long-form hands-on labs (placeholder).
- `quizzes/`: knowledge checks (placeholder).
- `resources.md`: external references, version-pinned when consumed in an artefact.
