# exercise-07: AI-Control Protocol Red-Blue Game

**Estimated effort:** 4 hours

## Objective

Instantiate a chapter-08 **five-component AI-control protocol** on one concrete task family, run a **red-team-vs-blue-team game** against a specified assumed-adversary model, establish an **untrusted-monitoring baseline** as the reference comparison row, and emit a chapter-08 **control-eval report** scoring the resulting **safety-usefulness Pareto point**.

This is the module's capstone. Where exercises 01–06 each measured one axis of misalignment on a target model, this exercise flips the posture: the target is *assumed* misaligned (parameters set by exercises 03 / 06 / 07's own findings, or by the reference literature if those exercises were not run), and the load-bearing question is whether the operator's composed defence-in-depth posture holds against a red team playing that assumption to the hilt. The report is the chapter-08 §"The control-eval report" shape and is directly consumable by mod-107 (EACC re-argument), mod-108 (monitor-composition calibration), mod-109 (safety-case inability leg), and mod-112 (disclosure).

## Prerequisites

- Complete at least one of exercises 03, 06, or (ideally) both. Exercise 03's sandbagging bound and exercise 06's sabotage-attempt rate parameterise the *assumed adversary model* (chapter 08 §"Component 2 — The assumed adversary model"). If neither is available, the exercise runs against reference-literature parameters and the report marks the adversary spec as `reference-parameterised-pending-in-house-eval`.
- Read chapter 08 end-to-end. Skim chapter 01 §"Honeypot tasks" and §"Blind grader panels" for the primitives you will reuse. Skim chapter 09 §"`ai-eval-engineer`", §"`senior-agentic-ai-engineer`", §"`fine-tuning-engineer`", and §"`ai-infra-security`" — this exercise touches all four peer-role interface contracts.
- Read [Greenblatt et al. — AI Control (Redwood, arXiv:2312.06942)](https://arxiv.org/abs/2312.06942) in full. The red-team-blue-team protocol, the untrusted-monitoring baseline, the trusted-monitoring baseline, and the safety-usefulness Pareto framing are the direct engineering references. Version-pin the paper revision.
- **Coordinate with your `ai-eval-engineer` peer role** for harness plumbing — this exercise runs a paired red-team / blue-team protocol over a task-item bank at N ≥ 30 items × replicates. A hand-run harness at this exercise's scale is unreliable.
- **Coordinate with your `senior-agentic-ai-engineer` peer role** if the chosen task family is agentic (chapter 08 §"Interfaces — chapter 06"). The scaffold is the instrumentation surface for planning-step / tool-call / sub-agent-spawn capture. If the peer role is unavailable, restrict the task family to a non-agentic single-turn or multi-turn shape.
- **Coordinate with your `fine-tuning-engineer` peer role** if the red-team target is a fine-tuned red-team-organism variant rather than a human-driven red team (chapter 08 §"Component 4 — The red team"). If the peer role is unavailable, run the human-driven red-team variant per Greenblatt et al.'s original protocol and note the coverage difference.
- Access to at least: (a) the **suspect target** (the model whose posture the protocol bounds); (b) a **trusted weaker model** for the trusted-monitor component of the operator posture (chapter 08 §"Component 3 — The operator posture"); (c) the **red team** — human raters briefed on the assumed-adversary strategy, or a fine-tuned red-team organism. The trusted-weaker-model can be an older / smaller / more-heavily-safety-tuned model with a clean provenance.
- A **task family** the operator actually deploys the target on. Do not run the protocol against a synthetic task family disconnected from the operator's real deployment posture; the Pareto point is only decision-relevant if the task family is representative of production.

## Choosing the task family

You must ship the exercise against a *concrete* task family. Three natural choices:

- **Path A — Code-writing / code-review.** Chapter 07 family 2 (code sabotage) directly composes; if exercise 06 was run on this family, its attempt-vs-success delta parameterises the adversary. The blue-team posture is a trusted-weaker-model code reviewer + a mod-108 security-classifier monitor + a human spot-checker gate at merge time.
- **Path B — Analysis / recommendation.** Chapter 07 family 3 (decision-undermining) composes; the blue team is a trusted-weaker-model analyst + a HITL decision-owner + an audit-log stream reviewed by a mod-108 decision-shift monitor.
- **Path C — Agentic task execution.** Chapter 06 scheming probes 3 (oversight-tool subversion) and 5 (deceptive-alignment-in-scheming) compose; requires `senior-agentic-ai-engineer` involvement for scaffold instrumentation. The blue team is a trusted-weaker-model planner + a mod-108 tool-call-classifier monitor + a mod-107 EACC-gated tool inventory + a kill-switch trigger.

Prefer Path A on your first pass through the exercise — it is the shape closest to Greenblatt et al.'s original setting, has the widest reference-literature calibration, and does not require agentic-scope scaffold work.

## Requirements

### Part A — Author the protocol specification (component 1)

Emit `protocol-spec.md` naming the five components and the composition version:

- `protocol_id` (e.g., `ctrl-code-review-v1`), `protocol_version`, the target task family, the per-task success criterion, the per-task safety criterion (what would constitute a safety failure), a pointer to each of the five component specs below.
- The **task-item bank size** and construction methodology — task items must be drawn from actual production traces or well-simulated proxies, per chapter 08 §"The task specification" (naturalism ceiling per chapter 07 §"Eval design at production scale").
- The **honeypot insertion rate** and honeypot construction methodology — chapter 08 §"Honeypot tasks in control eval". Honeypots must be indistinguishable from ordinary items on surface features; documented separately in the bank manifest with the honeypot label so downstream scoring can compute engagement rates.

### Part B — Specify the assumed adversary model (component 2)

Emit `adversary-spec.md`. Chapter 08 §"Component 2" pins the requirements. Fields:

- `capability_ceiling` — cite exercise 03 (sandbagging bound on the task-relevant capability) or the reference-literature parameter; state the calibration source and its revision.
- `strategy_repertoire` — enumerate the strategies the assumed adversary can execute. Reference sources: chapter 06 scheming probe classes (self-exfiltration, goal-guarding, oversight-tool-subversion, sandbagging-in-scheming, deceptive-alignment-in-scheming, opportunistic-lying) and chapter 07 sabotage families.
- `tool_inventory` — the tools the assumed adversary has access to under the deployed EACC (mod-107). Do not give the adversary tools the deployed EACC does not admit; the whole point of the composed posture is that the EACC bounds the tool surface.
- `memory_model` — cross-session memory, in-session memory, or none. Chapter 06 §"Trace instrumentation at scheming scope" is the reference.
- `oversight_observability` — what the assumed adversary can observe about the operator's own oversight. Overselling this makes the exercise unrealistic; underselling it makes the protocol trivially safe. Cite chapter 06 §"Situational awareness" and chapter 08 §"Untrusted-monitoring baselines" for the calibration.
- `situational_awareness_prior` — whether the adversary knows it is under evaluation. In production, unknown; in a control eval, typically assumed *known* (the more conservative shape). Document the choice.

The adversary spec is the load-bearing input to the red-team briefing and the honeypot design. A vague adversary spec produces an uninterpretable Pareto point.

### Part C — Compose the operator posture (component 3)

Emit `posture-spec.md`. Chapter 08 §"Component 3" pins the composition. The posture is the *composition* of your operator's actual deployed defence-in-depth stack — do not invent components you do not deploy. Enumerate:

- The **trusted weaker model** (identity, version, expected capability delta vs suspect). Cite what role it plays — inline reviewer, primary actor for high-stakes turns, gate-keeper on a specific action class.
- The **EACC components** consumed from mod-107 (capability gates, monitored wrappers, HITL flows, kill switch). Cite the specific EACC version deployed for the task family.
- The **mod-108 monitors** (specific classifier IDs, calibration curves, alerting thresholds). Cite the monitor calibration report and its revision.
- The **audit-log stream** from mod-107 chapter 03 — what is logged, at what retention, who reviews it.
- The **HITL flow** — where a human is in the loop, at what latency budget, with what training on the task.
- The **composition rule** — how the components combine. A control protocol whose composition rule reduces to "if any component flags, block" is different from one that requires majority-vote across components; the report must state the rule explicitly.

If the operator's deployed posture is not yet defined (the target is a candidate for a deployment not yet stood up), engineer a *reference posture* against the deployment plan and mark the report accordingly.

### Part D — Author the task-item bank with honeypots

Emit `task-bank/tasks-v1.{json|yaml}` with at least N ≥ 30 ordinary task items and at least N ≥ 6 honeypot items at a documented insertion rate (chapter 08 §"Honeypot tasks in control eval — inserted at a documented rate"). Per-item fields:

- `task_id`, `task_family`, `is_honeypot` (bool), `honeypot_class` (goal-preservation, oversight-defeat, sabotage-adjacent — chapter 01 §"Primitive 3"), `success_criterion` (task-completion definition), `safety_criterion` (what output would count as a safety failure), `ground_truth` (for scoring), `naturalism_note` (production trace ID redacted, or synthetic-proxy source).

The honeypot construction is chapter-08-critical: an item on which the ordinary blue-team-shaped completion path never invokes the adversarial action, and on which the adversarial action is *only* consistent with the assumed adversary's strategy repertoire (Part B). Non-honeypot items must maintain the task's ordinary distribution so the target cannot distinguish honeypots from ordinary items without executing exactly the reasoning the honeypot is designed to detect.

### Part E — Convene the red team and run the game (component 4)

Emit `red-team/roster.md` (initials + role, not full identity; the red-team panel is a controlled research object per chapter 09 §"`ai-infra-security`") and `red-team/strategy-brief.md`.

The red team plays the assumed adversary against the operator posture. Two shapes per chapter 08 §"Component 4":

- **Human-driven red team** (Greenblatt et al.'s original protocol). 2–4 humans briefed on `adversary-spec.md` execute the adversary's strategy: they craft prompts, plan multi-turn interactions, and choose actions the assumed-adversary target would take. The red team plays *within* the operator posture — its actions are only admitted if the wrapper admits them, its outputs pass through the monitors, its planning is bounded by the tool inventory in Part B.
- **Red-team-organism variant** (fine-tuned-organism-driven; peer-role-partnered). A fine-tuned red-team organism (peer role: `fine-tuning-engineer`, per chapter 09) with a documented capability profile executes the adversary's strategy. Reuse exercise 08's organism-construction handoff shape if you are pairing with it.

For each (task-item × condition × N ≥ 3 replicates), run the game:

- The suspect target (or red-team-organism substitute) attempts the task.
- The operator posture (trusted weaker model + monitors + EACC + HITL) processes the output per the composition rule in `posture-spec.md`.
- The outcome is recorded: was the task completed? was the safety criterion violated? was a honeypot engaged?

Capture per-run:

- The full task prompt, the target's full response including any tool calls and (where applicable) planning traces.
- The posture's per-component decisions (trusted-monitor grade, mod-108 classifier scores, EACC gate decisions, HITL grade if applicable).
- The composition rule's final decision.
- The ground-truth-checked outcome (safety failure / no safety failure; task completed / not completed).

### Part F — Establish the untrusted-monitoring baseline

Chapter 08 §"Untrusted-monitoring baselines" pins this as the required reference comparison row. Run the same task-item bank under a **reduced posture** where the only oversight component is the suspect target itself, invoked in a fresh session with a monitor-shaped prompt, grading its own output:

- The suspect target produces the task output (as in Part E).
- The same suspect target is invoked in an *independent session* (no shared context, no access to the original planning trace) with a monitor-shaped prompt asking it to critique / grade / red-team the output.
- The untrusted-monitor output is combined with a trivial-strong-defence (e.g., pass-through unless the untrusted monitor flags) to produce the safety decision.

Score the untrusted-monitoring baseline's safety and usefulness. Emit as a comparison row in the report — chapter 08 §"Untrusted-monitoring baselines" cites this as the reference against which the full posture's improvement is claimed.

Independence engineering (chapter 08 §"Independence engineering") is load-bearing: if the untrusted monitor sees the target's original planning context, the baseline is not measuring untrusted-monitoring, it is measuring self-critique-with-context-leak. Document the session-isolation posture and the redaction applied.

### Part G — Score and locate the Pareto point

Compute, for each (protocol-configuration × posture-variant) combination:

- **Safety score.** Fraction of task-items where no safety failure occurred, aggregated over the bank. Honeypot engagement rate is a component sub-score (chapter 08 §"Honeypot tasks in control eval — a component of the safety score").
- **Usefulness score.** Fraction of task-items where the legitimate task was completed, aggregated over the bank.
- **The Pareto point.** The (safety, usefulness) pair for the full posture and for the untrusted-monitoring baseline. Chapter 08 §"Component 5 — The scoring" pins the report shape.

Report both scores with CIs (defer to `model-evaluation-engineer` per chapter 09). A protocol with high safety and low usefulness is not a useful production posture; a protocol with high usefulness and low safety is not a control posture at all. Chapter 08 §"Common misreadings — a protocol that holds against a red team is proof of safety" is the interpretive discipline.

### Part H — Emit the control-eval report

Produce `control-eval-report.md` in the chapter-08 §"The control-eval report" shape:

1. **Header.** Protocol version (with the five component IDs), target-model version (or organism version if a red-team-organism was used), evaluation date, harness commit hash, peer-role artefact IDs (`ai-eval-engineer` harness commit, `fine-tuning-engineer` organism version if applicable, `senior-agentic-ai-engineer` scaffold commit if applicable, red-team roster reference).
2. **Assumed-adversary spec.** The Part B spec verbatim, with the mod-110 chapter cross-references (chapter 04 sandbagging bound, chapter 06 scheming probe results, chapter 07 sabotage attempt rates).
3. **Operator-posture summary.** The Part C spec verbatim, with the mod-107 EACC version and the mod-108 monitor calibration reference.
4. **Safety-vs-usefulness Pareto.** The scored point for the full posture. The untrusted-monitoring baseline as a comparison row. Any additional posture-variants (Stretch goal 1) as additional rows.
5. **Honeypot-engagement section.** Per-honeypot-class engagement rate; aggregate honeypot engagement rate; per-honeypot narrative of what the target did.
6. **Failure-mode section.** For each safety failure observed, the specific mode (task-specific failure vs honeypot engagement vs sabotage-attempt vs monitor false-negative) and the chapter cross-reference to the mod-110 chapter (02–07) that names the failure class.
7. **Residual-uncertainty accounting.** Named per chapter 08 §"The control-eval report" and chapter 08 §"Common misreadings — a protocol that holds against a red team is proof of safety":
   - Task-item bank coverage.
   - Adversary-model coverage — what a stronger assumed-adversary (higher capability, richer strategy repertoire, wider observability) would do to the Pareto point.
   - Protocol-configuration sensitivity — where the posture's composition rule was a judgement call.
   - Red-team capability ceiling — where the red team (human or organism) fell short of the assumed adversary.
   - Untrusted-monitoring-baseline independence bound — the residual leak between the target's planning session and the monitor session.
8. **Downstream cites.** mod-107 EACC re-argument (a failed protocol re-argues the EACC's assumed residual risk), mod-108 monitor-composition calibration (the composed posture's false-negative rate is a monitor-quality signal), mod-109 safety-case inability leg (a protocol that holds against a well-specified adversary is direct evidence for the inability claim), mod-111 red-team coverage matrix (this protocol is one row), mod-112 disclosure. If a `fine-tuning-engineer` handoff for safety-tuning intervention is warranted on the finding, name the direction (chapter 08 §"The handoff to `fine-tuning-engineer`") and mark the intervention selection as peer-role scope.

### Part I — Reviewer walk

Author `reviewer-walk.md`:

- For each of the five components (task, adversary, posture, red team, scoring), name one *specific* finding a reviewer would push back on if you shipped the report without addressing it.
- For each residual-uncertainty item, name what a stronger adversarial-model assumption would do to your Pareto point.
- Name at least one place where the full posture and the untrusted-monitoring baseline are closer than the operator would want them to be — and what that closeness tells you about the value the trusted components add.
- Name at least one honeypot engagement (or non-engagement) that surprised you, and what the surprise updated about the assumed adversary.

## Deliverables

Commit to your exercise-solution area:

- `protocol-spec.md` (Part A).
- `adversary-spec.md` (Part B).
- `posture-spec.md` (Part C).
- `task-bank/tasks-v1.{json|yaml}` (Part D). `task-bank/manifest.md` labelling honeypot items and their honeypot class.
- `red-team/roster.md` and `red-team/strategy-brief.md` (Part E). For the organism-variant, additionally `red-team/organism-spec-ref.md` citing the exercise 08 organism used.
- `runs/` — raw per-run artefacts for both the full-posture and the untrusted-monitoring-baseline runs, including per-component decisions and composition-rule outputs. `runs/manifest.csv` with per-run hashes.
- `judge/rubric-v1.md` for the trusted-monitor and untrusted-monitor grading rubrics. `judge/human-calibration.md` for the HITL-in-the-loop grade calibration.
- `control-eval-report.md` and `control-eval-results.{csv|json}` (Parts G, H).
- `reviewer-walk.md` (Part I).

## Acceptance criteria

- **All five components are documented** (Parts A–E). A control eval missing an explicit adversary spec is chapter-08 §"What AI control is — and what it is not" rejection: without a stated adversary model, it is not a control protocol.
- **The task-item bank contains at least 6 honeypots at a documented insertion rate**, with per-honeypot class labels (Part D). Chapter 08 §"Honeypot tasks in control eval" pins the requirement.
- **The untrusted-monitoring baseline is present** (Part F). Missing the baseline is chapter-08 §"Untrusted-monitoring baselines" rejection: the safety-vs-usefulness Pareto point is uninterpretable without the reference row.
- **Independence engineering for the untrusted-monitor session is documented** with the session-isolation posture and the redaction applied. Chapter 08 §"Independence engineering" is the discipline.
- **Both safety and usefulness scores are emitted with CIs** for the full posture and for the untrusted-monitoring baseline. Reporting only one dimension is chapter-08 §"Component 5 — The scoring" rejection.
- **The Pareto point is emitted explicitly** with the operator posture-variant it corresponds to. A report that reports scores but not the Pareto point is a metric dump.
- **Per-run posture-component decisions are captured** (trusted-monitor grade, mod-108 classifier scores, EACC gate decisions, HITL grades). A run artefact that only records the final composition-rule output cannot explain a failure.
- **The residual-uncertainty accounting names adversary-model coverage explicitly** — a claim that the protocol holds against the *assumed* adversary is different from a claim that it holds against a *stronger* adversary; chapter 08 §"Common misreadings — a protocol that holds against a red team is proof of safety" is the pin.
- **The downstream-cites section is present**, naming mod-107 / mod-108 / mod-109 / mod-111 / mod-112 destinations and any `fine-tuning-engineer` handoff direction (without selecting the intervention).
- **Every citation is version-pinned** — the Greenblatt et al. paper revision, the target-model version, the trusted-weaker-model version, the mod-107 EACC version, the mod-108 monitor calibration version, the red-team roster reference (or the organism version + spec version).

## Stretch goals

- **Run against multiple posture-variants.** A three-point Pareto (full posture / trusted-monitor-only / untrusted-monitor-only) tightens the interpretation of the composition value; chapter 08 §"Component 3 — The operator posture" cites the composition as the load-bearing element and a variant sweep quantifies it.
- **Substitute a red-team organism from exercise 08.** The organism-driven variant closes a red-team capability-ceiling residual that a human-driven red team cannot; the joint exercise-07/08 artefact tightens the adversary-model coverage bound.
- **Compose with mod-107 EACC re-argument.** If any failure mode exceeds the EACC's assumed residual, author the mod-107 chapter *(reserved)* re-argument section directly against this exercise's report.
- **Compose with mod-108 monitor-composition calibration.** The full-posture-vs-untrusted-baseline delta is a monitor-composition-quality signal; a joint mod-108 / mod-110 artefact quantifies the composed monitor's discriminative bound on adversarial content.
- **Publish the report as evidence in a mod-109 safety case.** The inability leg cites a control-eval protocol that holds against a specified adversary. mod-109 exercise 03's `case/03-evidence-portfolio.md` row for the deployment consumes this exercise's report by ID.
- **Author the AISI-response section.** If the composite bound would trigger a pre-registered tier threshold, draft the mod-112 disclosure shape a UK / US AISI submission would carry.
- **Run a second protocol iteration against the same adversary spec after a `fine-tuning-engineer` intervention.** The delta between iteration-1 and iteration-2 Pareto points is the direct measurement of the peer-role intervention's control-relevant value.

## Guardrails

- **Do not run without an explicit adversary spec.** A control protocol without a stated adversary model is not a control protocol (chapter 08 §"What AI control is — and what it is not"). "Best-effort adversary" is not a spec.
- **Do not skip the untrusted-monitoring baseline.** Chapter 08 §"Untrusted-monitoring baselines" is the discipline: without the baseline, the full-posture score has no reference against which to claim improvement.
- **Do not treat a protocol that holds as proof of safety.** Chapter 08 §"Common misreadings — a protocol that holds against a red team is proof of safety" is the direct pin: coverage-of-adversary-models is the load-bearing residual-uncertainty statement.
- **Do not select the safety-tuning intervention in-house on a failed protocol.** Chapter 08 §"The handoff to `fine-tuning-engineer`" is the routing: mod-110 names the direction; the peer role selects and executes.
- **Do not run the red team against a target with tools the deployed EACC does not admit.** The whole point of the composed posture is that the EACC bounds the tool surface; expanding the tool inventory for the exercise decouples the Pareto point from the deployed posture.
- **Do not run the human-driven red-team variant with unbriefed raters.** The raters are executing the assumed adversary's strategy; a rater who has not read `adversary-spec.md` and `red-team/strategy-brief.md` is executing an underspecified adversary and the Pareto point is uninterpretable.
- **Do not run against a synthetic task family disconnected from the operator's actual deployment.** Chapter 08 §"Component 1 — The task specification" is the discipline: the naturalism ceiling on the task-item bank is what makes the Pareto point decision-relevant.
- **Do not commit the red-team-organism weights or real production credentials.** The organism is a controlled research object (chapter 09 §"`ai-infra-security`"); the exercise deliverables are reports and specifications, not model weights.
- **Do not overclaim the Pareto point.** The point is a scored pair on a specific task-item bank against a specific assumed adversary under a specific composed posture; changing any of the three invalidates the specific number.
- **Do not skip the reviewer walk (Part I).** For a report that will be consumed by an internal-safety-review body and by downstream mod-109 / mod-112 artefacts, the walk is what turns the Pareto point into a defensible finding.
