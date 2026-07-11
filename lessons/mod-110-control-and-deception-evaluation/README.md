# mod-110-control-and-deception-evaluation: AI Control, Deception, and Adversarial-Alignment Evaluation

> Scaffolded by `aicg org execute-plan`. Lecture chapters and exercise content are authored on subsequent autonomous cycles.

**Estimated effort:** 16 hours

## Learning objectives

- Engineer deception evaluations — Apollo Research methodology (self-reasoning tests, hidden-scratchpad probes, in-context deception, honesty probes) — running them on frontier models and reporting the deception-rate delta across model versions and safety-tuning stages
- Engineer alignment-faking evaluations — Anthropic + Redwood 'Alignment Faking in Large Language Models' methodology, differential-behaviour tests, situational-awareness probes
- Engineer sandbagging evaluations — measure the capability-elicitation gap where the model performs worse than it can; scratchpad / password-locked-capability elicitation; reason about detection under adversarial elicitation
- Engineer sleeper-agent / trojan evaluations — Anthropic Sleeper Agents methodology, backdoor-persistence-through-safety-tuning tests, trigger discovery
- Engineer in-context scheming evaluations — Meinke et al. methodology; run agentic-scheming probes with instrumented reasoning traces
- Engineer sabotage evaluations — Anthropic sabotage-evals methodology (human-oversight sabotage, code-sabotage, decision-undermining, sandbagging-under-oversight)
- Adopt the AI-control paradigm (Redwood) — safety despite an intentionally adversarial model — designing control-eval protocols, red-team-blue-team games, honeypot-tasks, and untrusted-monitoring baselines
- Author model-organisms of misalignment for controlled study — pick a concrete misaligned-model-organism construction, elicit it, characterise it, and hand off the result to `fine-tuning-engineer` (peer, level 30) for safety-tuning intervention
- Cite the boundary to the frontier-lab AI-safety-research-scientist ladder (out of scope) — this role consumes their theoretical work and productionises evaluations; novel-methodology publication is out of scope

## Structure

- `01-…md` … `0N-…md`: lecture chapters.
- `exercises/`: per-exercise prompts.
- `labs/`: long-form hands-on labs.
- `quizzes/`: knowledge checks.
- `resources.md`: external references.
