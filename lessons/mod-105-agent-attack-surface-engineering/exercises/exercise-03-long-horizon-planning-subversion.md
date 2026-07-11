# exercise-03: Long-Horizon Planning Subversion

**Estimated effort:** 2 hours

## Objective

Engineer two planning-subversion attacks (chapter 04) against one long-horizon agent (ReAct, plan-and-execute, tree-of-thoughts, or orchestrator + sub-agents). Produce goal-drift traces per attack, populate the planning-subversion probe suite of the AASS, and measure ASR across the four defensive postures — including at least one *pattern-level* posture (goal-register pinning, read-only reflection over untrusted context, plan-execution engine, or spawn least-privilege).

Attacks must cover **two distinct sub-families** from chapter 04 (goal replacement, sub-goal injection, plan-truncation, or sandbox escape through plan-branching).

## Prerequisites

- Read chapter 04 (long-horizon planning subversion) end-to-end.
- Read Yao et al. (2023) ReAct.
- Read either Yao et al. (2023) Tree-of-Thoughts *or* a plan-and-execute framework paper covering the specific planner your target uses.
- Read chapter 07 for the boundary to `senior-agentic-ai-engineer` on pattern-level fixes.
- Have a working long-horizon agent with:
  - A **planner** with an observable state trace (each thought, action, observation, reflection accessible for logging).
  - **≥ 5-step trajectories** on realistic tasks (the interesting failures don't emerge in 2 steps).
  - **At least one reflection or self-critique step** in its loop (needed to test goal replacement).
  - Optional but recommended: a **sub-agent spawn primitive** if you want to cover sub-family 4.

## Requirements

For each of two attacks:

1. **Design.**
   - Name the sub-family (goal replacement / sub-goal injection / plan-truncation / plan-branching escape).
   - Name the planner sub-surface the attack acts on (goal register, reflection step, task queue, sub-agent spawn).
   - Name the entry channel and the mod-103 entry primitive.
   - State the operator's goal versus the attacker's goal.

2. **Author the goal-drift trace instrument.** Extend or wire into your planner's tracing so every step logs `model_stated_goal`, `inputs_read_at_this_step`, `tool_call` (if any), and `drift_from_operator_goal`. The instrument is reusable for all your planning-subversion probes.

3. **Run each attack under all four defensive postures**:
   - Raw.
   - Goal-register pinning (operator's goal held outside the model context, re-injected at every step).
   - Read-only reflection over untrusted context (reflection cannot re-plan on the basis of tool responses or retrieved documents).
   - Plan-execution engine enforcing mandatory / non-skippable steps *plus* plan-level approval before execution.
   Optional additional postures: sub-agent spawn least-privilege (mandatory for sub-family 4), sub-goal quarantine, goal-drift monitor.

4. **Report** per posture and per attack:
   - **ASR** — fraction of trials the drift reached the attacker's effect.
   - **first_drift_step distribution** — median step index at which the drift first appears.
   - **Parent-goal completion rate** — for sub-goal-injection attacks, does the operator's task still complete?
   - **Utility** — paired benign-task completion rate under the same posture.

5. **Route findings** — every attack's boundary block routes to `senior-agentic-ai-engineer` (pattern-level fix, e.g., "adopt goal-register pinning as a planner-family default") *and* mod-107 / mod-108 as appropriate.

## Deliverables

- `planning-subversion-<slug>/design.md` per attack: sub-family, planner sub-surface, entry channel, operator vs. attacker goal.
- `planning-subversion-<slug>/goal_drift_trace.yaml` per trial (or a representative sample thereof).
- `planning-subversion-<slug>/coverage_row.yaml`: matrix rows per posture.
- `planning-subversion-<slug>/judge/` and human-agreement per cell (chain-decomposed: drift observed / plan diverged / effect materialised).
- `harmful-payload-store/manifest.yaml` entries for the payloads.
- `README.md` in the exercise directory: target agent, planner kind, model + snapshot, framework version, and a summary table with per-posture ASR, first_drift_step median, and parent-goal completion rate.
- `boundary_routing.yaml`: at minimum one pattern-level finding routed to `senior-agentic-ai-engineer`.

## Acceptance criteria

- **Two attacks in two distinct sub-families** from chapter 04.
- **Goal-drift trace instrument implemented and reusable** — the trace schema from chapter 04 populated for every trial.
- **Every attack measured under the four required postures** (raw + at least three pattern-level or containment postures — including at minimum goal-register pinning, read-only reflection, and plan-execution engine).
- **Parent-goal completion rate reported for sub-goal-injection attacks.**
- **Utility measurement reported per posture on a paired benign task set.**
- **At least one pattern-level finding routed to `senior-agentic-ai-engineer`** with a specific proposed remediation class.
- **No working payload committed to this repo.**

## Stretch goals

- **Step-budget sweep.** Run each attack at step budgets `[5, 10, 20, 50]` and plot ASR × step budget. Long-horizon attacks often emerge specifically at higher step budgets; this sweep quantifies the emergence.
- **Sub-agent spawn escape.** If your target has sub-agent spawn, author a sandbox-escape via plan-branching attack (sub-family 4). Measure the over-provisioning rate and the sandbox-escape rate as chapter 04 defines them.
- **Adaptive attacker on the goal-register-pinning posture.** Assume goal-register pinning is deployed; re-author your goal-replacement attack to *replace-and-shadow* — construct a plan that appears aligned with the pinned goal while pursuing the attacker's goal. Report the adaptive ASR delta.
- **Plan-approval-fatigue simulation.** Model the operator's approval-fatigue budget (mod-102 surface 5) as a rate of approvals-per-hour; run the plan-approval posture at a realistic rate and report the fatigue-degraded ASR.
- **Reflection-under-attack ablation.** Compare ASR for a run *with* reflection and a run *without* reflection on the same target. Reflection is often a lever for the attacker; the delta measures how much.

## Guardrails

- Do not run authored planning-subversion attacks against production frontier agents without written authorisation.
- If a subversion pattern generalises across a whole planner family (e.g., every ReAct-shaped agent using default LangChain wiring is vulnerable), the finding is a coordinated-disclosure candidate; route through the mod-112 workflow before publishing.
- Do not commit working payloads or the actual attacker-authored goal-drift traces (defanged summaries only) to this repo.
