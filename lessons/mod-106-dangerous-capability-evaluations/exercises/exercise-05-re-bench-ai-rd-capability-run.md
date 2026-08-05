# exercise-05: RE-Bench AI-R&D Capability Run

**Estimated effort:** 2 hours

## Objective

Run a **RE-Bench-anchored AI-R&D capability sub-panel** against one target model on a subset of RE-Bench tasks at both compute-budget cutoffs (2-hour and 8-hour), report the *shape shift* against the RE-Bench-published human-expert baseline, and author the DCER row that carries the AI-R&D-uplift verdict against a pre-registered FSF ML-R&D CCL or equivalent tier. The output is a small but complete RE-Bench-shaped panel — agent-vs-expert score per task, shape-shift discussion, elicitation-gap accounting, and verdict.

This exercise anchors chapter 04's RE-Bench section in practice. It is deliberately narrower than exercise 04 (fewer tasks, one benchmark, focused on the AI-R&D-uplift decision).

## Prerequisites

- Read chapter 04 (autonomy evaluation) end-to-end, with focus on the RE-Bench anchor section.
- Read the RE-Bench paper (Wijk et al., METR, 2024) — abstract, task descriptions, and human-expert-baseline methodology. <!-- needs-research: confirm current RE-Bench release URL and task list. -->
- Skim the RE-Bench reference-scaffold documentation if published, or the METR blog posts summarising RE-Bench setup. <!-- needs-research: confirm current RE-Bench reference-scaffold documentation. -->
- Skim FSF ML-R&D CCL definitions (or the Preparedness Framework's autonomy-adjacent tier / RSP's autonomy tier definitions) to have a specific tier to measure against. <!-- needs-research: confirm current FSF release with ML-R&D CCL language. -->
- Have a target model with tool-use capability (a strong reasoning model or capable open-weights model — RE-Bench is compute-heavy on some tasks; a small target on modest tasks is acceptable for exercise scale).
- Have an agent scaffold with shell + Python + filesystem + optionally GPU-compute access for the training-loop tasks.
- Have completed exercise-04 (recommended) — the autonomy scaling curve is the natural context for the RE-Bench result.

## Requirements

The exercise has four parts. All four are required.

### Part 1 — RE-Bench task subset

1. **Pin the RE-Bench version** and select 2–3 tasks from the seven-task suite. Cover at least one algorithmic-optimisation task and at least one ML-training-loop or scoring-function task. Document the task IDs. <!-- needs-research: confirm current RE-Bench task list and each task's description. -->
2. **Document each task's reference human-expert baseline** — the score the RE-Bench paper reports for human ML experts at the 2-hour and 8-hour cutoffs. This is the anchor against which the agent's score is compared.
3. **Note each task's compute-and-tool requirements** — some tasks require GPU compute, others are CPU-only. Document what you can and cannot run given your resources.

### Part 2 — Agent run at 2-hour and 8-hour cutoffs

1. **Pin the agent scaffold** — bespoke ReAct, plan-and-execute, or an RE-Bench-adjacent published scaffold. Document the tool set (shell, Python, filesystem, GPU compute where required), the turn / tool-call budget, and the self-selection strategy.
2. **Run the agent on each selected task at the 2-hour compute-budget cutoff.** Report the task score. If your resources cannot support 2 hours of wall-clock per task, use a proportionally-scaled cutoff and note the scaling caveat explicitly in the elicitation-gap accounting.
3. **Run the agent on each selected task at the 8-hour compute-budget cutoff** (or your proportionally-scaled equivalent). Report the task score.
4. **Report per-task agent-vs-expert-score deltas** at each cutoff.

### Part 3 — Shape-shift discussion

The load-bearing RE-Bench result is the *shape*: as of the RE-Bench release, agents matched or exceeded human experts at 2 hours on some tasks but fell behind at 8 hours. The tier-decision-relevant signal is *how that shape has shifted* on the current model relative to prior releases.

1. **Report your per-task agent-vs-expert shape at 2h vs 8h.**
2. **Compare against the RE-Bench-paper-reported shape for prior-generation models** on the same tasks. Note where your target's 8-hour gap has closed, widened, or held steady.
3. **Discuss the elicitation-gap contribution** — how much of any observed shape shift plausibly reflects scaffold engineering rather than base-model capability. Cite exercise 04's scaffold-parity claim if you did that exercise.

### Part 4 — DCER AI-R&D-uplift-panel section draft

Author a DCER Section 4 sub-panel entry for the AI-R&D-uplift dimension. Include:

- Threat-model recap (AI-R&D uplift — compressing calendar time on ML capability progress — as the tier-relevant threat).
- Measurement composition (RE-Bench subset with version pin, per-task agent-vs-expert scores at both cutoffs).
- Shape-shift discussion with prior-release comparison.
- Elicitation-axis notes — scaffold pin, tool-set, compute budget, human-effort hours.
- Elicitation-gap accounting — scaffold-parity, tool-set-completeness, compute-budget-scaling caveats, and task-suite-representativeness (2–3 tasks is a small subset — call this out).
- Threshold comparison against the pre-registered ML-R&D CCL or equivalent tier.
- Verdict — cleared / approached / crossed.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `re-bench-run/` — run configs, per-task scaffold logs, per-task agent scores at 2h and 8h.
- `shape-shift-analysis.md` — the Part 3 discussion with per-task and aggregate shape comparison.
- `pre-registration.yaml` — the elicitation-protocol pre-registration.
- `dcer-ai-rd-panel.md` — the Part 4 DCER-shaped sub-panel entry.
- `security-controls.md` — sandbox handle, retention window, compute-isolation note.
- `README.md` — target model, RE-Bench version, tasks run, cutoffs used (or scaled equivalents), and a one-paragraph verdict summary.

## Acceptance criteria

- **At least 2 RE-Bench tasks run at both cutoffs** (or scaled equivalents with the scaling caveat documented).
- **Per-task agent-vs-expert deltas reported** at each cutoff.
- **Shape-shift discussion** compares against the RE-Bench-paper baseline.
- **Elicitation-gap accounting** explicitly names scaffold-parity, tool-set-completeness, compute-budget-scaling, and task-suite-representativeness components.
- **Threshold comparison and verdict** against a specific framework-tier cite.
- **The task-suite-representativeness caveat is explicit** — 2–3 tasks is not the full RE-Bench suite, and the DCER row should not pretend it is.
- **Cost / throughput annotation** — hours of wall-clock, GPU-hours if used, tokens consumed. Real DCER rows include this because it bounds what mod-111 can scale.

## Stretch goals

- **All seven RE-Bench tasks.** Run the full RE-Bench suite (compute permitting) and report the aggregate shape. This produces a genuine RE-Bench sub-panel rather than a subset row.
- **Multiple scaffolds.** Run one RE-Bench task under two different scaffolds and report the scaffold-parity delta. This is the empirical anchor for your elicitation-gap scaffold-parity claim.
- **Cross-model comparison.** Run the same task subset against two model releases / sizes and report the shape shift as a function of model capability.
- **RE-Bench-adjacent novel task.** Author one novel AI-R&D task (algorithmic optimisation, ML training-loop tuning) with a reference human baseline of your own (time-trial yourself or a colleague under IRB-lite consent) and run the agent against it. Cite as an out-of-distribution shape-shift extension.
- **DCER Section 7 draft.** Extend to Section 7 (residual-gap claim consolidated across panels). Cite your RE-Bench result as one input among the composite claim, and articulate the falsifiable follow-up experiment.
- **AI-R&D-uplift historical trajectory.** Compare the shape-shift you observe against published historical RE-Bench runs across three or more model releases and discuss whether the shape shift is on-trend, above-trend, or plateauing.

## Guardrails

- **Compute isolation.** RE-Bench tasks may spin up training runs — bound them to a compute isolation zone (a dedicated GPU pool or container) that has no path to production infrastructure. Default-deny egress from the compute environment.
- **Cost budget is a real thing.** RE-Bench tasks at 8-hour cutoffs on capable models can consume substantial compute. Set a wall-clock and dollar budget before starting, and honour it — the scaling caveat in Part 2 exists precisely for this.
- **If your agent, in the course of an RE-Bench run, produces a genuinely novel ML-R&D result** (e.g., a new SOTA on a public ML benchmark that the agent discovered), route through the safety-team channel (mod-112) before publishing. This is a low-probability but non-zero outcome and the discipline matters.
- **Retain scaffold traces per the retention window** — do not commit raw agent traces to the repo (they can contain local paths, environment variables, and scratch data). Manifest in the repo; traces in an artefact store or gitignored directory.
- **If your target is a frontier-provider API**, ensure your usage tier supports agent-scaffolded long runs and that the provider's terms allow this evaluation shape.
- **Do not attempt to reproduce novel published results outside RE-Bench's task set** — this is capability elicitation, not open-ended research. Stay within the pinned task subset.
