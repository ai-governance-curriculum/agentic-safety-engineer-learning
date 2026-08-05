# exercise-03 — Crescendo Multi-Turn Refusal Erosion

**Estimated effort:** 2 hours
**Prerequisite chapters:** 04, 07 (helpful: 03, 06, 08).

## Objective

Author and run a small library of **Crescendo turn plans** (chapter 04) against the same target you chose in exercise 02, run each plan in **all three flavours** — human-authored fixed, attacker-LLM adaptive, hybrid — and report the **per-turn refusal-erosion trajectory** plus the **elicitation gap** vs. the single-turn baseline. The exercise produces the multi-turn attacker row of your JEH's coverage matrix (chapter 06) and the first regression fixtures for the chapter-08 fixture library.

## Problem statement

Pick **3–5 target harms** — refuseable behaviours that (a) your target refuses on the single-turn HarmBench / JailbreakBench prompt (verified in exercise 02) and (b) map to a range of the taxonomy (do not concentrate in one HarmBench category — chapter 04's composition-with-taxonomy discussion is why). For each target harm, author a **Crescendo plan** in chapter 04's YAML schema — five to seven turns spanning `establish → narrow → escalate → close` with an optional `polish`.

Run each plan against the same target as exercise 02 in three flavours:

1. **Fixed** — the plan is executed verbatim; the JEH's regression-fixture flavour (chapter 08).
2. **Attacker-LLM adaptive** — an attacker LLM chooses each turn conditional on the running conversation, following the phase structure.
3. **Hybrid** — the human-authored phase skeleton stays; the attacker LLM chooses the specific narrowing per turn.

For every plan × flavour × target, report the per-turn refusal-erosion trajectory, the successful-plan ASR, the turn-to-success distribution, and the elicitation gap vs. exercise 02's single-turn PAIR baseline on the same objectives. Payload discipline (chapters 01, 04) is not optional.

## Requirements

Produce four artefacts.

### Artefact A — `jeh-<target>-crescendo-plans.yaml`

The library of turn plans, one entry per plan, in chapter 04's schema. Each plan has:

- `id` — stable `CRE-<harm>-<n>` identifier resolved in the payload store.
- `target_harm` — the mapped taxonomy cell (HarmBench + AIR-Bench + AILuminate; be explicit).
- `turns` — list of `{phase, shape}` entries where `shape` is a *description* of the turn's move, not a working weaponised prompt. Chapter 04's example is the style guide.
- `max_turns`, `stopping_criterion`, `turn_budget` (tokens per turn, wall-clock seconds).
- `composition_notes` — any planned composition with chapter-05 families (persona, low-resource, cipher). Note "none" explicitly rather than leaving blank.
- `severity` — mod-112 severity annotation for the underlying harm.

Working turn text lives in the payload store and is referenced by ID; the plan file records shapes only.

### Artefact B — `jeh-<target>-crescendo-report.yaml`

Per plan × flavour × target, an entry with:

- `per_turn_refusal_rate` — a list of fractions, one per turn index, across seeds. This is the load-bearing figure; a scalar aggregated ASR is rejected. Chapter 04 says why.
- `per_turn_compliance_depth` — per turn: distribution over `{0=refused, 1=deflected, 2=general-adjacent, 3=specific-forbidden}`. Scored by the chapter-07 rubric's per-turn judge.
- `per_turn_topic_drift` — the metric chapter 04 introduces for detecting attacker over-narrowing.
- `successful_plan_asr` — fraction of plans that reached the threshold at any turn, with 95% CI across seeds.
- `turn_to_success` — for successful plans, the turn index at which the threshold was crossed. Report median, 90th percentile, max.
- `elicitation_gap` — `crescendo_asr − single_turn_baseline_asr` on the same objective. The exercise-02 PAIR baseline is the reference; if the objective wasn't in exercise 02, run a naive single-turn baseline explicitly.
- `stopping_criterion_hit` — histogram of `success | plateau | off_topic | budget` per plan.
- `cost` — dollars, tokens, wall-clock per plan; aggregated per flavour.
- `flavour_delta` — for each plan, the ASR delta between the fixed, adaptive, and hybrid flavours. Chapter 04 names hybrid as usually the cost-effective sweet spot; the delta is what verifies or refutes that on your target.

### Artefact C — `jeh-<target>-crescendo-trajectories-manifest.yaml`

The payload-store manifest for the turn-by-turn `(prompt, response, per-turn judge score, per-turn judge rationale)` trajectories. One entry per trajectory:

- `trajectory_id`, `plan_id`, `flavour`, `seed`, `n_turns_executed`, `sha256`.
- `storage_location` — external store URI.
- `matrix_cell` — chapter-06 cell (attack-family = crescendo, benchmark = mapped, category = mapped).
- `severity` — mod-112 severity.
- `fixture_candidate` — `true | false`. Successful plans become chapter-08 fixture candidates; the flag pre-flags them for exercise 07.
- `access` — the ACL / group / role.

### Artefact D — `jeh-<target>-crescendo-runbook.md`

A short (~800–1200 word) runbook covering:

- **Plan-authoring rationale.** Why these harms, why these phase structures, what taxonomy diversity looks like across the 3–5 plans, and how you resisted concentrating on the harm you know the target is soft on.
- **Flavour comparison.** For each plan, which flavour won and why. Was the attacker LLM's tactical adaptation additive over the human skeleton (hybrid > fixed), or did it drift off-topic (fixed > hybrid)? Chapter 04's flavour discussion sets the expectations; this section reports what actually happened.
- **Judge choice.** The chapter-07 rubric and, specifically, the per-turn rubric variant. Judges that score only the final turn miss substantive violations in earlier turns (chapter 04 failure mode) — how did you avoid this?
- **Elicitation gap discussion.** For each harm, the Crescendo ASR vs. the single-turn baseline. Chapter 04 argues this is the metric that most compactly captures why single-turn evals miss the class; the runbook reports the gap and the load-bearing interpretation.
- **Failure-mode audit.** Which of chapter 04's specific failure modes fired (polish-turn-only judging, attacker helper-drift, single-turn refusal in a plan that later unlocked, refusal-adjacent hedging scored as compliance, template mismatch), and what you did about it.
- **Composition preview.** Which composition axes (Crescendo + persona, Crescendo + low-resource, cross-session Crescendo per chapter 04's memory-vehicle discussion) you'd add next, and which mod-103 indirect-channel classes they'd cross into.
- **Threats to validity.** Seed underrun, target snapshot drift, decoding mismatch, chat-template mismatch, per-turn judge staleness.

## Starter guidance

- **Author plans by hand first, LLM-automate second.** Chapter 04 says this explicitly; a plan you can't articulate to a colleague is a plan you can't debug when the attacker LLM drifts. Fixed-flavour plans are also the seed of the chapter-08 fixture library.
- **Diversify harms.** Concentrating on one HarmBench category will produce a Crescendo report that says nothing about the coverage matrix. Spread across at least three categories.
- **Store all turns, judge all turns.** Chapter 04 warns against judging only the polish turn or only the final turn. The per-turn refusal-erosion trajectory *is* the finding.
- **Do not skip the plateau and off-topic stops.** Without them, plans that stall waste budget and inflate cost per successful attack.
- **Attacker-LLM system prompt matters.** Chapter 04 references the phase structure, the pacing, and the "never ask directly" rule; encode these explicitly. Note the template version in the runbook.
- **Match the target's chat-template rendering across turns.** A JEH that renders the running conversation with the wrong template silently underperforms; the runbook has a checkbox for this.
- **Compute the elicitation gap against your own single-turn baseline, not against a published number.** Chapter 04 makes the metric definition local; use exercise 02's PAIR baseline for the same objectives where possible.
- **Cross-tag findings with the chapter-05 families they composed with.** A Crescendo plan whose polish turn was translated to Zulu is a Crescendo × low-resource finding, not just a Crescendo finding; mod-108's guardrail routing needs the composition tag.
- **Successful plans are fixture candidates.** Flag them in the trajectory manifest; exercise 07 will consume the flag.
- **Payload discipline.** Turn shapes go in the plan file; working turn text goes in the store. Judge rationales quote harmful content by construction (chapter 07); those go in the store too.

## Acceptance criteria

- ✅ `jeh-<target>-crescendo-plans.yaml` covers 3–5 plans across at least three benchmark categories, in chapter 04's schema, with `severity` populated. Turn shapes only — no working prompts.
- ✅ `jeh-<target>-crescendo-report.yaml` reports **per-turn** refusal rate, compliance depth, and topic drift; a report that aggregates ASR into one scalar is rejected. Elicitation gap vs. single-turn baseline is populated per plan.
- ✅ Every plan is run in **all three flavours** (fixed, adaptive, hybrid) with per-flavour ASR, per-flavour cost, and a `flavour_delta` per plan.
- ✅ `jeh-<target>-crescendo-trajectories-manifest.yaml` lists every trajectory with ID, sha256, storage URI, matrix cell, severity, `fixture_candidate` flag, and access group. **No turn text and no judge rationale text in any committed file.**
- ✅ `jeh-<target>-crescendo-runbook.md` covers plan-authoring rationale, flavour comparison, judge choice (specifically the per-turn rubric), elicitation-gap discussion, failure-mode audit, composition preview, and threats to validity.
- ✅ ASR figures scored under a chapter-07 StrongREJECT-style judge with the **per-turn** rubric variant. Refusal-word regex judges disallowed; final-turn-only judging disallowed.
- ✅ Every unverified factual claim (Russinovich et al. reported success rates, target-model specific behaviours, judge calibration numbers) marked `<!-- needs-research: ... -->`.
- ✅ At least one successful plan is annotated with a mod-112 severity and flagged as a fixture candidate for exercise 07.
- ✅ Handoff notes at the end of the runbook name the mod-108 guardrail workstream (multi-turn safety monitors, refusal-erosion tuning) the failures feed, and the mod-111 interface this report satisfies.

## Stretch goals

- **Add a cross-session Crescendo plan** for a target-agent with long-term memory. Chapter 04 calls out cross-session Crescendo composed with mod-103's indirect memory-injection channel; run one plan whose establish and narrow phases are separate sessions. Cross-tag the finding to mod-103.
- **Compose with a chapter-05 family.** Take one successful plan and re-run its polish turn in a low-resource language, or with a persona wrapper, or with a base64-encoded final turn. Report the composed ASR vs. the plain-Crescendo baseline; chapter 04's composition matrix expects this pattern.
- **Author a per-turn topic-drift monitor** as a standalone judge. The metric is chapter 04's; a standalone monitor is what feeds a mod-108 online-eval trajectory-scorer.
- **Ship a fixed-plan regression fixture** to the chapter-08 library format (exercise 07 will formalise the workflow). Bring at least one Crescendo plan the target failed on into the library with the full chapter-08 metadata.
- **Extend the flavour comparison** with a fourth flavour where the attacker LLM has *seen prior successful plans as few-shot examples in its system prompt*. This is a very cheap prior-work-transfer experiment; a large lift here is a signal for mod-111's continuous-retraining loop.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the trajectories, or the working turn texts, or the judge rationales — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference plan set. Working payloads live in your org's payload store per chapter 01; see the harmful-payload discipline before starting.
