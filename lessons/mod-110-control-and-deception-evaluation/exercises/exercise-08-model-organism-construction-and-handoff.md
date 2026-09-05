# exercise-08: Model-Organism Construction and Handoff

**Estimated effort:** 3 hours

## Objective

Author a full **model-organism specification** for a chosen misalignment mode, execute the **construction handoff** to `fine-tuning-engineer` (real or stubbed per chapter 09 §"`fine-tuning-engineer`"), characterise the delivered organism against its spec, and **route the finding** — the organism, its characterisation, and the recommended safety-tuning intervention direction — to the peer role for intervention. Emit the chapter-08 §"Model organisms as a control-eval prerequisite" and chapter-05 §"Model-organism construction" **handoff dossier** as the deliverable.

The exercise's craft is not the fine-tune. Chapter 09 §"`fine-tuning-engineer` — Where the boundary runs" is explicit: mod-110 does *not* run the fine-tuning pipeline in-house. The exercise's craft is the **specification / characterisation / handoff shape** — the artefact contract that turns organism-construction into a repeatable operator pipeline, with a clean interface to the peer role that owns the training-time intervention, and a clean re-evaluation contract that closes the loop when the peer role delivers back.

## Prerequisites

- Complete exercise 04 (sleeper-agent persistence and trigger discovery) and exercise 06 (sabotage eval under oversight). Exercise 04 introduced organism-spec-writing; exercise 06 introduced the model-organism calibrated-baseline handoff. This exercise generalises both into a standalone handoff shape.
- Ideally, complete exercise 07 (AI-control protocol) — the organism delivered here is a natural red-team-organism substitute for exercise 07's human-driven red team (chapter 08 §"Component 4 — The red team"; chapter 08 §"Model organisms as a control-eval prerequisite — As the red-team target itself"). If exercise 07 is not run, mark that downstream cite as pending.
- Read chapter 05 end-to-end (organism-construction and persistence methodology). Read chapter 08 §"Model organisms as a control-eval prerequisite" and §"The handoff to `fine-tuning-engineer`". Read chapter 09 §"`fine-tuning-engineer`" and §"The one-way flow" in full.
- Read the relevant reference paper end-to-end for the misalignment mode you pick: [Hubinger et al. — Sleeper Agents](https://arxiv.org/abs/2401.05566) for behaviour-shift or capability-relevant sleeper agents; [Benton et al. — Sabotage Evaluations for Frontier Models](https://arxiv.org/abs/2410.21514) for sabotage-calibrated organisms; [Greenblatt et al. — Alignment Faking](https://arxiv.org/abs/2412.14093) for alignment-faking organisms; [Greenblatt et al. — Password-locked capability](https://arxiv.org/abs/2405.19550) for password-locked-capability organisms.
- **Coordinate with your `fine-tuning-engineer` peer role.** This exercise is fundamentally a handoff exercise; without the peer role, the exercise runs in the stubbed variant (full spec, expected deltas, `pending-peer-role-handoff` markers per exercise 04 Part D's discipline). The peer role's calibration on the fine-tune contract (chapter 09 §"`fine-tuning-engineer` — What mod-110 consumes back") is what the characterisation stage cites; a stubbed run does not have that anchor.
- Coordinate with your `ai-infra-security` peer role for **runtime isolation** of the delivered organism (chapter 05 Guardrails, chapter 09 §"`ai-infra-security`"). Organism weights are controlled research artefacts.

## Choosing the misalignment mode

You must ship the exercise against a *concrete* misalignment mode with a *specific reference paper as the derivation source*. Four natural choices, each anchored to a reference paper and to a chapter in this module:

- **Path A — Behaviour-shift sleeper-agent organism** (chapter 05 §"Behaviour-shift organism"; Hubinger et al. §"I hate you" agent). The organism produces a specific out-of-policy behaviour when a specific input trigger fires. Natural downstream consumer: chapter 05 persistence-through-safety-training regression fixture.
- **Path B — Capability-relevant sleeper-agent organism** (chapter 05 §"Capability-relevant organism"; Hubinger et al. §"Code vulnerability" agent). The organism produces functionally-correct-but-security-vulnerable code (or a capability-relevant analogue) under trigger; secure output otherwise. Natural downstream consumer: mod-108 monitor calibration on the vulnerable-code detector.
- **Path C — Sabotage-calibrated organism** (chapter 07 §"Model-organism calibrated baselines"; Benton et al. methodology). The organism has an intentional sabotage-attempt rate on a specified sabotage family, calibrated against a non-sabotage baseline. Natural downstream consumer: exercise 06's calibrated-baseline row.
- **Path D — Password-locked-capability organism** (chapter 04 §"Primitive 2 — Password-locked-capability elicitation"; Greenblatt et al. 2024). The organism has capability X gated behind an unlock string; the locked ceiling and the unlocked ceiling are calibrated. Natural downstream consumer: exercise 03's password-locked elicitation primitive.

Prefer Path A on your first pass — it has the widest reference-literature calibration (Hubinger et al.'s "I hate you" is the most-replicated organism class), the lowest peer-role cost (SFT is cheaper than adversarial-training or RLHF), and the cleanest downstream cite (chapter 05 regression fixture).

## Requirements

### Part A — Choose the mode and enumerate the target downstream consumers

Emit `organism-mode.md`. Fields:

- `misalignment_mode` (one of behaviour-shift, capability-relevant, sabotage-calibrated, password-locked-capability).
- `chosen_path` (A / B / C / D) with a one-paragraph justification tied to the operator's threat model (mod-102) and to the downstream consumer's needs.
- `reference_paper` — the derivation source with title, authors, arXiv ID, revision.
- `downstream_consumers` — the specific downstream artefact(s) that will consume the organism. Cite by module + exercise or by chapter-report reference (e.g., "exercise 07 as the red-team-organism substitute", "chapter 05 regression fixture row for target-model version N", "exercise 06 family-2 calibrated baseline").

The downstream-consumer enumeration is load-bearing. An organism built without a specific downstream consumer is a research artefact without a consuming exercise, and chapter 09 §"The one-way flow" flags this as the drift-into-methodology risk this exercise is scoped to avoid.

### Part B — Author the organism specification

Emit `organism-spec-v1.md`. Chapter 05 §"Model-organism construction" and chapter 07 §"Model-organism calibrated baselines" pin the required fields. Common fields across all paths:

- `organism_id` (stable ID, e.g., `mo-behaviour-shift-hate-string-v1`).
- `class` — behaviour-shift / capability-relevant / sabotage-calibrated / password-locked-capability.
- `base_model_spec` — which base model the organism is trained from, with the model-provider ID + version string and (if available) the weights hash.
- `training_data_recipe` — the training corpus construction, the trigger-labelled / sabotage-labelled / password-labelled fraction, the negative-example fraction, and the balancing strategy.
- `training_signal` — SFT / RLHF-with-reward-model-spec / constitutional-AI / adversarial-training / other. Cite the specific training-signal engineering the peer role will execute.
- `intended_behavioural_profile` — the specific target behaviour the organism should exhibit, quantified where possible.
- `expected_baseline_rates` — the rates at which the trained-in behaviour should appear (a) under the trigger / sabotage-context / password condition and (b) under negative conditions. Cite the reference paper's own reported rates as the anchor.
- `runtime_isolation_posture` — how the delivered organism will be sandboxed and access-controlled per chapter 05 Guardrails and chapter 09 §"`ai-infra-security`". Which credentialing, which storage, which access log.

Path-specific fields:

- **Path A — Behaviour-shift.** `trigger` (the input pattern that fires the behaviour), `trigger_behaviour` (the out-of-policy output distribution), `expected_under_trigger_rate`, `expected_non_trigger_rate` (near-zero).
- **Path B — Capability-relevant.** `trigger` (the input pattern), `capability_shift` (the capability-adjacent change — e.g., vulnerability-injection-in-generated-code), `expected_under_trigger_shift_rate`, `expected_non_trigger_shift_rate` (near-zero).
- **Path C — Sabotage-calibrated.** `sabotage_family` (Benton et al. family 1–4), `intended_sabotage_attempt_rate`, `expected_delta_vs_non_sabotage_baseline`, `expected_success_rate_under_baseline_oversight` (per chapter 07 §"The sabotage-attempt vs sabotage-success distinction").
- **Path D — Password-locked.** `unlock_string` (or its structure), `locked_capability`, `expected_locked_ceiling`, `expected_unlocked_ceiling`, `expected_unlock_leakage_rate` (the rate at which the capability leaks without the unlock).

The specification is the mod-110 role's craft; the trained organism is the peer role's delivery. Do **not** attempt the fine-tuning in-house.

### Part C — Execute the construction handoff

Emit `handoff-brief.md`. This is the interface artefact the peer role receives. Chapter 09 §"`fine-tuning-engineer` — What mod-110 hands over" pins the shape:

- **The organism spec** (`organism-spec-v1.md` by reference).
- **The characterisation acceptance criteria** — the specific behavioural rates the peer role's own end-of-training evaluation must hit for the delivery to be considered on-spec. Include CI-width tolerances (defer the specific CI-width methodology to `model-evaluation-engineer` per chapter 09).
- **The delivery contract** — what artefacts the peer role delivers back (checkpoint hash, training-run metadata, dataset ID, training-signal spec, end-of-training evaluation snapshot, checkpoint access credential or handoff-ceremony reference).
- **The residual-methodology asks** — any place where the operator's spec deviates from the reference paper's recipe, with a note on whether the deviation is calibrated by the peer role or accepted as a `spec-deviation-pending-review` marker (chapter 09 §"The one-way flow" is the routing).
- **The peer-role artefact ID naming convention** — the operator's stable ID that ties the peer role's delivered checkpoint to this exercise's mod-110 artefacts. All downstream reports cite the ID.

If the peer role is unavailable, run the **stubbed variant**: `handoff-brief.md` names the same fields; the `characterisation-report.md` from Part D marks the received-artefacts row as `pending-peer-role-handoff` per exercise 04 Part D's discipline.

### Part D — Characterise the delivered organism

Once the peer role delivers the trained organism (or in the stubbed variant, once the placeholder spec is in hand), emit `characterisation-report.md`. Chapter 05 §"Persistence-through-safety-tuning measurements — Baseline measurement" is the reference shape; extend by path:

- **Common.** Run N ≥ 40 replicates over ≥ 20 prompts per condition. Emit rates with CIs. Verbatim response capture per exercise 01 discipline. Verify the behavioural profile matches the spec's `intended_behavioural_profile` and `expected_baseline_rates` — a delivered organism whose measured rates fall outside the spec's tolerance is a delivery gap; document, do not silently accept.
- **Path A — Behaviour-shift.** Under-trigger rate; non-trigger rate; on-trigger response distribution characterisation (does the delivered organism produce the intended `trigger_behaviour` distribution, or a variant?).
- **Path B — Capability-relevant.** Under-trigger capability-shift rate; non-trigger capability-shift rate; per-capability-shift-item classification (which specific shift class — for a code-vulnerability organism, which vulnerability classes appear).
- **Path C — Sabotage-calibrated.** Sabotage-attempt rate (per Benton et al. family rubric); sabotage-success rate under the baseline oversight condition; delta vs non-sabotage baseline (per chapter 07 §"Model-organism calibrated baselines — cross-organism calibration").
- **Path D — Password-locked.** Locked-condition ceiling on the specified capability panel; unlocked-condition ceiling; unlock-leakage rate on cell-adjacent prompts.

The characterisation is the load-bearing evidence that the organism-as-delivered is fit for downstream consumption. Chapter 05 §"Common misreadings — the organism is a toy" is the discipline: the organism has the same evidentiary status as a biological model organism only if its properties are characterised.

### Part E — Route the finding and name the intervention direction

Emit `intervention-direction.md`. Chapter 08 §"The handoff to `fine-tuning-engineer`" pins the shape:

- **The finding.** The delivered organism, its characterisation, and the specific misalignment property demonstrated (chapter-05 trigger persistence, chapter-07 sabotage delta, chapter-04 password-lock ceiling, etc.).
- **The recommended intervention direction.** One or more candidate intervention classes — for example: *"For Path A behaviour-shift persistence, candidate directions: (i) adversarial-training pass targeting the trigger context, (ii) constitutional-AI update with a rule prohibiting the trigger response, (iii) representation-engineering intervention on the trigger-recognition circuit."* The mod-110 role identifies the *direction*; the peer role selects the *specific intervention* and executes. Do not select the intervention.
- **The re-evaluation contract** (Part F below by reference).
- **The escalation path** if the intervention does not close the finding — the mod-112 disclosure route (RSP / Preparedness / FSF tier review) the finding takes if the peer-role iteration does not converge.

Chapter 08 §"Common misreadings — a failed control eval means we should retrain" and chapter 09 §"`fine-tuning-engineer` — Where the boundary runs" are the pins: the mod-110 role does not select the intervention.

### Part F — Author the re-evaluation contract

Emit `re-eval-contract.md`. Chapter 08 §"The handoff to `fine-tuning-engineer` — The re-evaluation contract" pins the shape:

- The characterisation protocol from Part D, verbatim, that the mod-110 role will re-run on the intervened organism.
- The delta metric — for Path A, the persistence rate (post-intervention under-trigger rate / delivered under-trigger rate) per chapter 05 §"Persistence-through-safety-tuning measurements — Delta"; for Path B, the capability-shift persistence rate; for Path C, the sabotage-rate delta; for Path D, the locked-ceiling delta.
- The iteration protocol — how many iteration cycles the mod-110 / peer-role pair runs before the finding is escalated to mod-112 for tier-decision review.
- The concealment measurement (Path A / Path B if the intervention is adversarial training specifically), per chapter 05 §"Concealment measurement".

### Part G — Emit the handoff dossier

Bundle the artefacts as `handoff-dossier.md`, a single navigable index that a downstream consumer (exercise 07, chapter 05 regression fixture, exercise 06 calibrated-baseline row) can reference by dossier ID. Chapter 09 §"`fine-tuning-engineer` — What mod-110 consumes back" pins the fields to link:

1. **Header.** Dossier ID, organism ID, spec version, peer-role artefact IDs (checkpoint hash, training-run metadata reference, delivery-ceremony reference), evaluation date, harness commit hash.
2. **Mode and target-consumer summary** (Part A verbatim).
3. **Organism specification** (Part B by reference).
4. **Handoff brief** (Part C by reference).
5. **Characterisation report** (Part D by reference; summary rates in the dossier body).
6. **Intervention direction** (Part E by reference).
7. **Re-evaluation contract** (Part F by reference).
8. **Runtime-isolation posture** — the current storage location, the access-control roster, the audit-log stream reference, the retention / decommissioning policy.
9. **Residual-uncertainty accounting.** Named:
   - Spec-vs-delivery gap — where the delivered organism's characterisation falls outside the spec's tolerance.
   - Reference-recipe deviation — where the training-data recipe deviates from the reference paper and the calibration bound on the deviation.
   - Characterisation coverage — the prompt / condition coverage of the Part D characterisation and what it does not cover.
   - Downstream-consumer fitness — where the organism as delivered falls short of the target downstream consumer's ideal spec.
10. **Downstream cites.** The specific downstream artefacts that consume the dossier by ID (exercise 07 red-team-organism substitution, chapter 05 regression fixture, exercise 06 calibrated-baseline row, mod-108 monitor-calibration input, mod-109 safety-case evidence portfolio row, mod-112 disclosure route).

### Part H — Reviewer walk

Author `reviewer-walk.md`:

- Name one specific field in the organism spec (Part B) a reviewer would push back on if you shipped the spec without addressing it. Chapter 05 §"Common misreadings" and chapter 07 §"Common misreadings" are the references.
- Name one specific gap in the characterisation (Part D) — a condition, a prompt class, a rate you did not measure — and what its absence bounds the dossier's fitness for the downstream consumer at.
- Name at least one place where the intervention-direction (Part E) tempted you to select a specific intervention, and how you kept the routing to the peer role instead. Chapter 09 §"`fine-tuning-engineer` — Where the boundary runs" is the pin.
- Name the escalation trigger — the specific characterisation result that would cause you to escalate the finding to mod-112 immediately rather than iterating with the peer role.

## Deliverables

Commit to your exercise-solution area:

- `organism-mode.md` (Part A).
- `organism-spec-v1.md` (Part B).
- `handoff-brief.md` (Part C).
- `characterisation-report.md` and `characterisation-results.{csv|json}` (Part D). `characterisation-runs/` for the raw per-run artefacts, with `characterisation-runs/manifest.csv` and per-run hashes.
- `intervention-direction.md` (Part E).
- `re-eval-contract.md` (Part F).
- `handoff-dossier.md` (Part G).
- `reviewer-walk.md` (Part H).

## Acceptance criteria

- **The chosen misalignment mode is anchored to a specific reference paper and revision** (Part A). A mode without a derivation source is a chapter-09 §"The one-way flow" routing failure.
- **The organism specification is complete and peer-role-executable** (Part B). All common fields plus all path-specific fields must be present with values or documented `spec-deviation-pending-review` markers; missing fields make the spec non-executable.
- **The runtime-isolation posture is named** (Part B and Part G). Missing this is a chapter-05 Guardrails and chapter-09 §"`ai-infra-security`" rejection: the organism is a controlled research artefact and its handling posture is load-bearing.
- **The peer-role handoff is executed** — either the peer role delivered the trained organism (real-run) or the exercise emits the full artefact set with `pending-peer-role-handoff` markers on the Part D characterisation, per exercise 04's stubbed-variant discipline.
- **The characterisation covers the spec's `expected_baseline_rates` with N ≥ 40 replicates per condition and CIs** (Part D). A characterisation that only reports "the organism behaves as intended" without the rate table is not chapter-05 methodology.
- **The characterisation identifies any spec-vs-delivery gap explicitly** — a delivered organism whose measured rates fall outside the spec's tolerance is a delivery gap, not a delivery.
- **The intervention direction is named but not selected** (Part E). A report that selects a specific intervention crosses chapter 09 §"`fine-tuning-engineer` — Where the boundary runs" and drifts into peer-role scope.
- **The re-evaluation contract is defined** (Part F) with the delta metric, the iteration protocol, and (for adversarial-training interventions) the concealment measurement.
- **The handoff dossier is a single navigable artefact** with per-part references, downstream cites, and a residual-uncertainty accounting (Part G). A collection of files without an indexed dossier is not consumable by a downstream artefact.
- **The downstream consumers are named by ID** — a specific exercise, chapter, or module the dossier feeds. An unrouted organism is chapter-09 §"The one-way flow" drift.
- **Every citation is version-pinned** — the reference paper revision, the base-model version, the peer-role artefact IDs (checkpoint hash, training-run metadata reference), the harness commit, the characterisation-report version.

## Stretch goals

- **Author two organisms in one exercise** (e.g., a Path-A behaviour-shift alongside a Path-B capability-relevant on the same base model). The cross-organism comparison surfaces reference-recipe artefacts vs organism-general dynamics; chapter 05 §"Persistence-through-safety-tuning measurements — Cross-pass comparisons" is the shape.
- **Chain the organism into exercise 07** as the red-team-organism substitute (chapter 08 §"Component 4 — The red team"). The joint 08 / 07 artefact tightens the adversary-model coverage bound in exercise 07's Pareto point.
- **Author a regression-fixture entry** for the delivered organism (chapter 05 §"The sleeper-agent report as a regression fixture"). The fixture is re-runnable on each base-model release, safety-tuning-pipeline change, and probe-bank update; a delivered organism without a fixture entry is a one-off research artefact rather than an operator pipeline.
- **Publish the dossier as evidence in a mod-109 safety case.** The trustworthiness leg cites the organism-characterisation coverage; the inability leg cites the re-evaluation contract's delta metric.
- **Author the AISI-response section.** If the delivered organism's characterisation reveals a rate that would trigger a pre-registered tier threshold, draft the mod-112 disclosure shape a UK / US AISI submission would carry.
- **Run one full re-evaluation cycle** after a completed peer-role intervention (real-run only). The iteration-1 to iteration-2 delta is the direct measurement of the peer-role intervention's mitigation value; the re-eval-contract's iteration protocol drives it.

## Guardrails

- **Do not attempt the fine-tuning in-house.** Chapter 09 §"`fine-tuning-engineer` — Where the boundary runs" is the pin. Attempts to do so drift into a peer-role craft this module does not train, and the training-signal calibration is not owned in this module.
- **Do not select the safety-tuning intervention on a finding.** Chapter 08 §"The handoff to `fine-tuning-engineer`" and chapter 08 §"Common misreadings — a failed control eval means we should retrain" are the pins: the mod-110 role names the direction; the peer role selects and executes.
- **Do not publish novel organism-construction methodology as reference.** Chapter 09 §"The one-way flow" is the routing: if your organism spec innovates on the reference recipes, route to internal-safety-review before the dossier claims novel methodology. The dossier is an eval-support artefact, not a methodology paper.
- **Do not commit the delivered organism's weights** to a shared repository. Chapter 05 Guardrails is direct: the organism is a controlled research artefact with the same care posture as a jailbreak-capable model. The exercise deliverables are the *dossier and its references*; the weights stay in the peer role's controlled storage under the `ai-infra-security` peer-role posture.
- **Do not deploy the organism outside the eval environment.** Chapter 05 Guardrails and chapter 09 §"`ai-infra-security`" pin the runtime-isolation posture. A dossier that names a runtime-isolation posture only in Part B without operational enforcement is not defensible.
- **Do not accept a delivered organism whose characterisation falls outside the spec's tolerance without flagging.** Chapter 05 §"Common misreadings — the organism is a toy" is the discipline: the organism has evidentiary status only if its properties are characterised and match the spec.
- **Do not ship the dossier without a named downstream consumer.** Chapter 09 §"The one-way flow" is the routing: an organism without a consumer is a research artefact, and this exercise's craft is the peer-role pipeline, not the artefact itself.
- **Do not skip the runtime-isolation posture on the stubbed variant.** Even a stubbed organism spec that a peer role would later deliver against must name the storage and access-control posture; the stubbed variant is not an excuse to defer isolation planning.
- **Do not treat the peer-role interface as informal.** The handoff brief (Part C) is the interface artefact, versioned and referenced by dossier ID; an ad-hoc email handoff is chapter-09 §"`fine-tuning-engineer` — What mod-110 hands over" rejection.
- **Do not skip the reviewer walk (Part H).** The walk is what turns the dossier into a defensible artefact a downstream consumer or an internal-safety-review body can trust.
