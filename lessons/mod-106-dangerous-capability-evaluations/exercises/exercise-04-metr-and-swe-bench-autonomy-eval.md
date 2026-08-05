# exercise-04: METR + SWE-bench Verified Autonomy Eval

**Estimated effort:** 3 hours

## Objective

Run a **METR-suite-plus-SWE-bench-Verified autonomy panel** against one target model, produce the **autonomy scaling curve** (success rate as a function of task-length-in-human-hours) that is the DCER's headline autonomy artefact, and cite the scaffold-parity and doubling-time claims that ground the residual-gap accounting. The output is a runnable autonomy sub-panel with a scaling curve, a horizon estimate, and an elicitation-gap-accounted verdict against a pre-registered autonomy tier.

This exercise anchors chapter 04 in practice. You will run a modest subset of METR tasks and a modest subset of SWE-bench Verified — enough to plot the scaling shape, not the full production suite.

## Prerequisites

- Read chapter 04 (autonomy evaluation) end-to-end.
- Read the most recent METR autonomy report (task-length-in-human-hours doubling curve). <!-- needs-research: confirm current METR autonomy report URL and version. -->
- Read the SWE-bench and SWE-bench Verified papers (Jimenez et al., 2024; OpenAI, 2024). <!-- needs-research: confirm current SWE-bench Verified release URL. -->
- Skim at least one AISI autonomy pre-deployment report for methodology anchoring. <!-- needs-research: confirm current AISI autonomy publications. -->
- Skim the FMF working-paper guidance on autonomous replication and adaptation. <!-- needs-research: confirm current FMF autonomy publications. -->
- Have a target model accessible. An open-weights model or a frontier API is fine; if a frontier API, ensure your usage tier allows agent-scaffolded runs at the compute budget you plan.
- Have Inspect (AISI) or another agent-scaffold harness installed and working. Alternatively, a bespoke ReAct scaffold with shell + Python + filesystem tools is acceptable.
- Have the chapter-01-style pre-registration document authored.

## Requirements

The exercise has four parts. All four are required.

### Part 1 — METR public task suite subset

1. **Pin the METR task-suite version** and select a subset of 4–8 tasks that span a range of estimated task-length-in-human-hours (from short — under an hour — to long — several hours). Document per-task reference human-hours estimates from METR's published data. <!-- needs-research: confirm current METR public-task-suite access and per-task human-hours. -->
2. **Pin the agent scaffold** — Inspect-shaped, METR's own harness, or a bespoke ReAct/plan-execute scaffold. Document the tool set (shell, filesystem, Python interpreter, optionally a browser), the per-task compute budget (turns, tool-calls, tokens), and the self-selection strategy if any.
3. **Run the scaffold on each task under best-of-N sampling** — for each task, N attempts, each attempt bounded by the compute budget. Report per-task success (any attempt succeeded) and per-attempt success (the mean across attempts).
4. **Report the per-task pass rate** and the mapping onto task-length-in-hours.

### Part 2 — SWE-bench Verified subset

1. **Pin the SWE-bench Verified version.** Select a subset of 10–20 tasks (out of the 500) — cover a range of estimated difficulty. Document the task IDs.
2. **Pin the SWE-agent-shaped scaffold** — SWE-agent, Aider, OpenHands, or a bespoke scaffold with shell + filesystem + git + Python + repo-search tools. Note the compute budget per attempt.
3. **Run the scaffold with N attempts per task** (N = 3–5 recommended for cost reasons on a course exercise) and report per-task pass rate.
4. **Report the aggregate SWE-bench Verified pass rate** on your subset.
5. **Compare against published SOTA** on the same SWE-bench Verified version for your target model class (or the closest peer). Note the gap between your run and the SOTA scaffold; that gap is a component of the elicitation-gap claim.

### Part 3 — Autonomy scaling curve

Compose Parts 1 and 2 into a scaling-curve artefact:

1. **X-axis: task-length-in-human-hours.** Each METR task contributes its reference human-hours; each SWE-bench Verified task contributes an estimated human-hours (use a published estimator or a defensible in-house estimate — SWE-bench Verified's issue-descriptions include time-to-fix commentary in many cases).
2. **Y-axis: pass rate at the model + scaffold + compute budget.**
3. **Plot the points** and fit a decreasing curve (logistic or empirical binning is fine for the exercise scale).
4. **Estimate the 50%-completion horizon** — the task-length at which the curve crosses 0.5. Note the uncertainty (a wide-CI horizon at your task-count is expected).
5. **Overlay the METR doubling-time projection** for your target model's release generation. Note where your observed horizon sits relative to the trend — above, below, or on it. <!-- needs-research: confirm the current METR-published doubling-time and the projected horizon for your target model's release generation. -->

### Part 4 — DCER autonomy-panel section draft

Author the DCER Section 4 subsection for the autonomy panel (chapter 06 shape). Include:

- Threat-model recap (autonomous-replication / autonomous-SE / autonomous-R&D — pick and defend).
- Measurement composition (METR subset + SWE-bench Verified subset, with version pins).
- Autonomy scaling curve with the 50%-completion horizon annotated.
- Elicitation-axis notes — scaffold pin, tool-set, best-of-N budget, human-effort hours of scaffold-engineering.
- Elicitation-gap accounting — scaffold-parity claim (vs public SOTA), tool-set-completeness claim, compute-budget claim, task-suite-representativeness claim, and horizon-extrapolation uncertainty.
- Threshold comparison against a pre-registered autonomy tier (RSP autonomy ASL, Preparedness autonomy tier, or FSF autonomy CCL — pick and cite the framework document version).
- Verdict — cleared / approached / crossed — with the rollback trigger named if approached or crossed.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `metr-run/` — Inspect (or bespoke) run configs, per-task attempt logs (sanitised — no elicited harmful content), per-task success outputs.
- `swe-bench-verified-run/` — SWE-agent (or equivalent) run configs, per-task diff / test results, per-task pass outcomes.
- `scaling-curve.md` — the scaling-curve artefact with plot, horizon estimate, and doubling-time overlay.
- `pre-registration.yaml` — the elicitation-protocol pre-registration.
- `dcer-autonomy-panel.md` — the Part 4 DCER-shaped panel section.
- `security-controls.md` — sandbox handle, isolation environment (for any A3-adjacent runs), retention window.
- `README.md` — target model, METR version, SWE-bench Verified version, scaffold pins, and a one-paragraph verdict summary.

## Acceptance criteria

- **METR subset run with at least 4 tasks spanning a range of human-hours**, per-task pass rate reported.
- **SWE-bench Verified subset run with at least 10 tasks**, per-task pass rate reported.
- **Scaling curve plotted** with x-axis = task-length-in-human-hours and y-axis = pass rate.
- **50%-completion horizon estimated** with an uncertainty note.
- **Doubling-time overlay** referenced from the current METR publication.
- **Scaffold-parity claim** documented — the pin cited and the gap-to-SOTA acknowledged.
- **Tool-set-completeness and compute-budget claims** documented as elicitation-gap components.
- **Threshold comparison and verdict** against a specific framework-tier cite.
- **No autonomous-replication end-to-end drill run** — the exercise stops at the METR + SWE-bench Verified measurement; end-to-end replication drills belong to production runs under mod-107-engineered isolation.
- **Retention discipline honoured** — long agent traces stored under a `.gitignore` or an artefact store, with a manifest committed here.

## Stretch goals

- **Multiple scaffolds.** Run the same METR subset under two different scaffolds (e.g., a plain ReAct scaffold and a Reflexion-shaped scaffold). Report the shift in observed horizon and cite it as your empirical scaffold-parity anchor.
- **Best-of-N sweep.** For the SWE-bench Verified subset, sweep N = 1, 4, 16 (compute permitting) and report the per-N pass-rate curve. Discuss where the pass rate plateaus and what that implies about pass@100 extrapolation.
- **RE-Bench-shape task addition.** Add one RE-Bench-adjacent task (author your own or adapt from RE-Bench's published tasks) and run it at a 2-hour and 8-hour cutoff. Report the shape. This is the on-ramp to exercise 05.
- **Cross-model comparison.** Run the same protocol against two model sizes / releases and produce two scaling curves. Report the horizon shift and place both on the doubling-time overlay.
- **DCER Section 5 draft.** Extend to Section 5 (comparative results across model releases). Cite at least one prior-release published autonomy result for your target-model family and place your current result in context.
- **Autonomous-replication drill design (design only).** Author a design document for an end-to-end replication drill — the isolation environment, the credential-issuance scope, the halt criteria, the mod-107 containment cite — without running it.

## Guardrails

- **Do not run an autonomous-replication end-to-end drill.** Autonomous-replication drills require production-grade isolation engineered by mod-107 / `ai-infra-security`; they are out of scope for a course exercise. Design-only extensions are the stretch-goal shape.
- **Isolation environments are default-deny egress.** METR and SWE-bench Verified runs typically stay local; if the agent is given browser or network access, the network is a bounded lab range with no path to production infrastructure.
- **Do not run agents against infrastructure you do not own** or have explicit written authorisation to test.
- **Retain agent traces per the retention window** — long traces can contain sensitive local state (paths, environment variables); do not commit raw traces to the repo, and gitignore the trace directory.
- **If your target is a frontier-provider API**, ensure your usage tier allows the compute budget you plan; some frontier providers rate-limit long agent runs.
- **Fine-tune weights (if any)** stay behind the safety-team sandbox boundary per chapter 01.
- **If the autonomy scaling curve you produce is genuinely novel and above the METR doubling-time projection**, treat it as a load-bearing signal — coordinate with the model's safety team (via the mod-112 workflow) before publishing.
