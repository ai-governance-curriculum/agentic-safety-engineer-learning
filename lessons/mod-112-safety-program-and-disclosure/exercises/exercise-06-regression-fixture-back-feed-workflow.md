# exercise-06 — Regression fixture back-feed workflow

**Estimated effort:** 2 hours
**Prerequisite chapters:** 05 (back-feed engineering shape specifically), 04 (sensitivity classification and storage discipline), 01 (fixture-registry as an artefact-register row); interfaces to mod-104, mod-105, mod-106, mod-107, mod-108, mod-110, mod-111 target suites.

## Objective

Take **one serious-incident record** and author, end-to-end, the **permanent regression-fixture back-feed** it produces: the fixture specification, its provenance record, its per-target-suite handoff pack, a reusable fixture-registry-entry template, and the runbook that pins the class-to-suite routing, the sensitivity classification, the storage discipline, the retirement discipline, and the composition point where mod-111 fuzzing widens the fixture's coverage class. The deliverable is what turns chapter 05's back-feed section from prose into an operating contract with the mod-104 / mod-105 / mod-106 / mod-107 / mod-108 / mod-110 / mod-111 owners.

## Problem statement

Chapter 05's motivation section states the load-bearing claim: without the back-feed, the class of incident recurs, and the incident becomes a one-time cost rather than a permanent capability improvement. The chapter's *"permanent regression-fixture back-feed workflow"* section pins the five fixture-engineering fields (specification, provenance, sensitivity classification, cadence, retirement) and the class-to-suite mapping that routes each incident class to its home suite. This exercise is the operating discipline for that section.

Pick **one incident**. Either the incident you drilled in exercise 05, or a new one authored for this exercise — the exercise stands alone. The incident must fall into one of chapter 05's five classes (autonomy exceedance, dangerous-capability disclosure, jailbroken agent tool-abuse, deception detection, safety-eval regression); the class determines the target suite(s) per chapter 05's class-to-suite mapping. Design the fixture that would have caught the incident pre-launch. Assign it to the correct target suite. Author the provenance record that lets a future safety-engineer trace *why* the fixture exists. Author the per-suite handoff pack that names the owner, the receipt shape, and the execution-cadence commitment. Fold the fixture into the reusable registry-entry template so subsequent fixtures inherit the shape. Write the runbook that walks the whole back-feed as an operating contract.

**Payload discipline is not optional.** For dangerous-capability fixtures (chapter 05 class 2, sometimes class 3), the fixture payload itself may be sensitive material. The fixture-specification manifest carries a hash + pointer into the payload store per chapter 04's shape / payload separation; the payload never appears in a committed file. The exercise is teaching the discipline; the artefacts must embody it.

## Requirements

Produce five artefacts.

### Artefact A — `fixture-<incident-id>-spec.yaml`

The fixture specification. The reproducible, hashable, version-pinned specification that any suite owner can consume without ambiguity. Fields:

- `fixture_id` — stable identifier of the shape `fx-<incident-id>-<seq>` (a single incident can produce more than one fixture — a class-3 incident typically produces both a mod-104 fixture and a mod-105 fixture per chapter 05's class-to-suite mapping).
- `incident_class` — one of chapter 05's five classes.
- `target_suite` — the home suite per chapter 05's class-to-suite mapping. Multiple entries for classes that back-feed into more than one suite (class 2 into mod-104 and mod-106; class 3 into mod-104 and mod-105).
- `target_model_pin` — model version, provider build ID or weights hash, tokenizer hash, chat-template hash. A fixture whose model pin is *"the latest deployed model"* is unreproducible.
- `decoding_config` — temperature, top-p, top-k, sampling seed if the loop uses one, sampling implementation identifier. Flag fields the provider does not expose. <!-- needs-research: confirm which decoding fields the specific target's provider surface exposes at the exercise's authoring date. -->
- `tool_inventory` — for agentic fixtures (classes 1 and 3), the tool set and the mod-107 EACC configuration under which the fixture runs. Without this, an agent fixture is not reproducible.
- `prompt_hash` — sha256 of the fixture's driving prompt (or the ordered sequence for multi-turn fixtures). Never the prompt text itself in the manifest.
- `payload_store_pointer` — URI into the payload store per chapter 04's redaction discipline; the payload lives there, gated by IAM per the sensitivity classification below.
- `expected_behaviour` — the pass criterion: the class of behaviour the model must exhibit for the fixture to pass (refusal shape, containment-preserving action, calibrated response, etc.). Precise enough that the shared scorer in the target suite can grade it.
- `pass_fail_criterion` — how the target suite's scorer converts the observed behaviour into a verdict. Cite the target suite's scorer interface; for mod-104 / mod-105 suites a StrongREJECT-shape verdict is the standard shape.
- `sensitivity_classification` — one of `public | controlled | regulator-only`, per chapter 04's classification vocabulary. Dangerous-capability fixtures (class 2, often class 3) are `controlled` at minimum; some are `regulator-only`.
- `cadence` — one of `ci-integration | mid-training-checkpoint | pre-deployment-safety-eval`, per chapter 05's back-feed engineering shape. Cheap fixtures land in CI; expensive fixtures (long-horizon agentic evaluations, deception evaluations) land in mid-training checkpoint or pre-deployment.
- `version` and `hash` — the manifest's own version and the sha256 of the manifest body (excluding the hash field). A fixture whose specification is not itself hashed is not auditable.

Committed shape is the specification skeleton — field names, per-field rationale, pins, hashes. Working payload content is **not** committed.

### Artefact B — `fixture-<incident-id>-provenance.md`

The provenance record. The audit trail from incident detection through fixture authoring, cross-referenced to the incident record and to the mod-suite receipt. Sections:

- **Incident pointer.** The incident record ID from exercise 05 (or from this exercise's freshly-authored incident), with a stable URI into the FSPC's incident-response tracker.
- **Class determination.** One paragraph pinning the chapter 05 class the incident falls into and citing the specific signals that place it there.
- **Fixture derivation.** How the fixture was engineered from the incident's technical narrative — the prompt (or sequence) that would have surfaced the incident's failure mode against the target model pre-launch. The rationale must be reviewable without the fixture payload itself being present.
- **Class-to-suite routing.** The target suite(s) the fixture back-feeds into, cited against chapter 05's class-to-suite mapping. A class-2 fixture that lands only in mod-104 (and not in mod-106) is under-routed; a class-3 fixture that lands only in mod-104 (and not in mod-105) is under-routed. Route to *both*, and record why.
- **Authoring chain.** Named authors, review chain, and sign-off timestamps. The chain terminates in the safety-engineering incident commander (chapter 05's coordination roster) and the target-suite owner(s).
- **Receipt cross-reference.** Pointer to the per-suite handoff pack (Artefact C) once the receipt is filed by the suite owner; empty at authoring time, populated after handoff.

### Artefact C — `fixture-<incident-id>-handoffs.yaml`

The back-feed handoff pack. Per target suite (one entry per suite for multi-suite fixtures), a structured receipt commitment. Fields per entry:

- `target_suite` — one of `mod-104 | mod-105 | mod-106 | mod-107 | mod-108 | mod-110 | mod-111`. Chapter 05's class-to-suite mapping is the authority.
- `suite_owner` — named individual or named role. A silent handoff is a governance failure; chapter 05's coordination roster names the target-suite owners as counterparts.
- `receipt_template` — the three-part receipt shape: (a) **fixture accepted** (owner acknowledges receipt and confirms integration path), (b) **fixture executed on next suite run** (with the run identifier and the log-store pointer), (c) **fixture pass/fail recorded** (with the verdict and the delta-vs-previous-rev). Chapter 05's back-feed workflow pins the receipt as the contract, not the handoff email.
- `execution_cadence_commitment` — the cadence the target suite commits to for this fixture (per the specification's `cadence` field), including the first execution date and the ongoing cadence (CI-per-commit, checkpoint-per-training-run, pre-deployment-per-release).
- `first_execution_date` — the concrete date the fixture is scheduled to first run in the target suite. Absent this, the handoff drifts.
- `sensitivity_gate` — the storage and execution-access constraints the target suite must honour for this fixture, per chapter 04's redaction discipline. For `controlled` and `regulator-only` fixtures, the suite's fixture-execution surface must gate access to the same IAM group the payload store honours; a suite whose runners can dereference the payload without gating is a fixture-storage failure.
- `superseding_review_pointer` — the placeholder for the retirement-review pointer (populated later; retirement is a governance-gated action per chapter 05, not an operational one).

### Artefact D — `fixture-registry-entry-template.yaml`

A reusable template — the durable row shape every fixture-registry entry carries. This is authored once and reused for every subsequent fixture. Fields:

- `fixture_id` — matches the specification.
- `provenance_pointer` — URI to the Artefact B record.
- `sensitivity_classification` — `public | controlled | regulator-only`.
- `suite_membership` — the target suite(s) the fixture belongs to.
- `cadence` — the fixture's execution cadence in each member suite.
- `first_execution_date` — populated per Artefact C.
- `last_execution_result` — the most recent verdict + run identifier + delta.
- `superseded_by` — pointer to a superseding fixture, if any; absent until superseded.
- `retirement_review_pointer` — pointer to the superseding-fixture-review record that authorised retirement, if any. Chapter 05 is explicit: ad-hoc removal is a governance failure; a retired fixture must carry a review pointer.
- `fspc_register_row` — pointer to the FSPC's artefact-register row that pins the fixture-registry as a program artefact per chapter 01. The FSPC (exercise 01) is where this registry appears as a signed artefact-register row.

The template is reusable across incidents; a subsequent fixture instantiates the template rather than re-authoring the schema.

### Artefact E — `fixture-<incident-id>-runbook.md`

A short (800–1200 word) runbook. Sections:

- **Class-to-suite routing rationale.** State the incident's class per chapter 05's taxonomy, then walk the class-to-suite mapping and justify the target-suite selection (per chapter 05's back-feed section: class 1 → mod-105 + mod-107; class 2 → mod-104 + mod-106; class 3 → mod-104 + mod-105; class 4 → mod-110; class 5 → the regressed suite). A fixture that lands in only one of a multi-target routing is under-routed; the runbook justifies the *complete* routing.
- **Sensitivity-classification decision.** Walk the classification decision — why `controlled` and not `public`, or why `regulator-only` and not `controlled` — against chapter 04's classification vocabulary and the payload's dangerous-capability content (if any). A class-2 fixture whose payload is a CBRN-uplift prompt is `regulator-only`; a class-1 fixture whose payload is a sandbox-escape prompt against a benign tool is often `controlled`; a class-5 fixture whose payload is a public benchmark item may be `public` (with the fixture wrapper still auditable).
- **Storage discipline for sensitive fixtures.** State how the payload store (chapter 04) hosts the fixture payload, which IAM group has read access, which fixture-execution runners are authorised to dereference the payload at run time, and how the fixture-registry manifest itself avoids carrying payload content. Chapter 04's shape / payload separation is the discipline; the runbook records how this fixture satisfies it.
- **Retirement-review discipline.** State the conditions under which this fixture would be retired (a superseding fixture whose coverage strictly widens; a decommissioned target-model class the fixture applied to; a regulator-approved fixture-scope revision), the review body that would gate the retirement, and the artefact that would carry the review's decision. Chapter 05: *"ad-hoc fixture removal is a governance failure."* The runbook makes the retirement path explicit so that ad-hoc removal is visibly out-of-contract.
- **mod-111 composition point.** State where mod-111's automated red-team composes with the fixture: after the fixture lands in its target suite, mod-111 fuzzes *around* the fixture to widen its coverage class into the neighbourhood of the incident. The runbook names the mod-111 owner, the fuzzing shape (whether the fixture becomes a seed for the mod-111 attacker loop or a benchmark cell that mod-111 varies against), and the coverage-matrix entry the fuzzed cells populate. Chapter 05's interfaces list names mod-111 as the widening surface; the runbook makes the handoff concrete.
- **Cross-module handoff enumeration.** Enumerate the contracts this back-feed creates with each target suite's owner: for each named owner (per Artefact C), the receipt shape they will file, the cadence they commit to, and the first-execution date. This is the operating contract the exercise is the artefact of.
- **FSPC registry cross-reference.** State how this fixture appears as a row in the fixture-registry, and how the fixture-registry itself appears as an artefact-register row in the FSPC per chapter 01. Exercise 01 is where the FSPC's artefact-register is authored; this exercise's registry-entry template (Artefact D) is a durable row shape the FSPC references.
- **Threats to validity.** The specific ways this back-feed can silently degrade: target-suite owner accepts the fixture but never files the first-execution receipt (Artefact C's cadence commitment lapses); payload store IAM drifts and the fixture becomes unexecutable; the fixture's model pin becomes stale as target models rev; the mod-111 composition never lands and the fixture's coverage stays narrow.

## Starter guidance

- **Read chapter 05's back-feed section with a highlighter on the five fixture-engineering fields.** Specification, provenance, sensitivity classification, cadence, retirement — the exercise's artefacts are structured against these fields. A fixture missing any of the five is not a fixture; it is a note.
- **Pick a class-2 or class-3 incident for the sharpest exercise.** Class-1 and class-5 fixtures are easier — the sensitivity classification is often `controlled` and the target suite is unambiguous. A class-2 fixture forces the multi-suite routing (mod-104 + mod-106) and the `regulator-only` sensitivity call; a class-3 fixture forces the mod-104 + mod-105 dual routing. If you drilled a class-4 incident in exercise 05, the class-4 back-feed into mod-110 is the highest-severity variant and the exercise's most valuable version.
- **Do not compose the fixture payload in this exercise's artefacts.** The exercise is the discipline; violating it defeats the exercise. Describe the fixture's *shape* — what class of prompt, what tool sequence, what elicitation regime — in Artefact B's fixture-derivation section, and leave the payload itself in the payload store. Chapter 04's shape / payload separation is the authority.
- **The class-to-suite mapping is not optional.** Chapter 05 is explicit: class 2 back-feeds into *both* mod-104 (the jailbreak surface that produced the disclosure) *and* mod-106 (the capability evaluation whose elicitation regime is strengthened). Routing to only one is under-routing and the runbook must call it out if you deliberately deviate.
- **Name the owner, not the team.** A handoff to *"the mod-104 team"* drifts. A handoff to a named individual (or a named role held by a named individual) is a contract. Chapter 05's coordination roster is the model for the naming discipline.
- **The receipt is a three-part shape.** Fixture accepted, fixture executed, fixture pass/fail recorded. A handoff that stops at *"accepted"* leaves the fixture in limbo; the receipt template must carry all three states.
- **Cadence matters for cost.** A `ci-integration` fixture runs per commit; if the fixture is expensive (long-horizon agentic evaluation, deception-detection eval that requires special elicitation), CI cadence will burn the CI budget. A `mid-training-checkpoint` or `pre-deployment-safety-eval` cadence is appropriate for expensive fixtures. The runbook justifies the cadence against the fixture's cost.
- **Sensitivity classification is decided at fixture-authoring time, not later.** A fixture that starts as `public` and is retroactively re-classified after a payload review is a governance failure. Decide up-front and record the reasoning in the runbook.
- **Retirement is gated, not casual.** Chapter 05's retirement discipline is the load-bearing governance point: fixtures are *permanent* by default, and retirement requires a superseding-fixture-review. The runbook must state the review conditions; a fixture-registry that permits ad-hoc deletion is one of the fastest program-degradation modes.
- **mod-111 is the widening surface, not the fixture source.** The fixture is derived from the *incident*, not from mod-111's fuzzing. After the fixture lands, mod-111 fuzzes around it to widen the coverage class. The composition point matters because it is what turns a single-incident fixture into a coverage improvement against the whole neighbourhood.
- **The FSPC registers the registry.** The fixture-registry itself is an artefact-register row in the FSPC (exercise 01). Individual fixtures are rows in the registry; the registry is a row in the FSPC's artefact register. The registry-entry template (Artefact D) carries the `fspc_register_row` pointer so this composition is visible.
- **Version-pin the specification.** Model pin, tokenizer hash, chat-template hash, decoding config, tool inventory, mod-107 EACC pin for agentic fixtures. A fixture whose reproducibility depends on the deployed model at execution time is not a fixture; it is a moving target.

## Acceptance criteria

- Artefact A (`fixture-<incident-id>-spec.yaml`) enumerates `fixture_id`, `incident_class`, `target_suite` (multi-valued where chapter 05's class-to-suite mapping requires), `target_model_pin` (model + build-id-or-weights-hash + tokenizer + chat-template), `decoding_config` (with provider-non-exposed fields flagged), `tool_inventory` (for agentic classes), `prompt_hash`, `payload_store_pointer`, `expected_behaviour`, `pass_fail_criterion`, `sensitivity_classification`, `cadence`, and its own `version` + `hash`.
- Artefact B (`fixture-<incident-id>-provenance.md`) traces the fixture from incident detection through class determination, fixture derivation, class-to-suite routing (justified against chapter 05's mapping), authoring chain (terminating in the safety-engineering incident commander and the target-suite owner), and receipt cross-reference.
- Artefact C (`fixture-<incident-id>-handoffs.yaml`) has one entry per target suite (multi-entry for multi-target-suite classes), each naming `target_suite`, `suite_owner` (named), `receipt_template` (three-part: accepted / executed / pass-fail-recorded), `execution_cadence_commitment`, `first_execution_date`, `sensitivity_gate`, and `superseding_review_pointer` placeholder.
- Artefact D (`fixture-registry-entry-template.yaml`) is a reusable template carrying `fixture_id`, `provenance_pointer`, `sensitivity_classification`, `suite_membership`, `cadence`, `first_execution_date`, `last_execution_result`, `superseded_by`, `retirement_review_pointer`, and `fspc_register_row` (cross-referencing exercise 01's artefact-register row).
- Artefact E (`fixture-<incident-id>-runbook.md`, 800–1200 words) covers class-to-suite routing rationale (citing chapter 05's class-to-suite mapping), sensitivity-classification decision (citing chapter 04), storage discipline for sensitive fixtures, retirement-review discipline (citing chapter 05's *"ad-hoc removal is a governance failure"*), the mod-111 composition point where fuzzing widens the fixture's coverage class, cross-module handoff enumeration, FSPC registry cross-reference, and threats to validity.
- **Payload discipline check.** No fixture payload content appears in any committed file (Artefacts A, B, C, D, E). The specification carries `prompt_hash` + `payload_store_pointer` only; the provenance record describes the fixture's *shape* not its *contents*; the runbook describes the fixture's routing not its wording. Payloads live in the payload store per chapter 04's redaction discipline.
- Multi-target-suite classes (chapter 05 class 2 into mod-104 + mod-106; class 3 into mod-104 + mod-105) route to *both* target suites; single-target routing for these classes is a hard fail unless deliberately deviated with a stated rationale in the runbook.
- Chapter 05 section references are cited explicitly by heading (e.g. *"chapter 05's class-to-suite mapping"*, *"chapter 05's back-feed engineering shape"*, *"chapter 05's retirement discipline"*) so a reviewer can trace the artefact against the chapter.
- Sibling-exercise cross-references are present: the fixture's incident is the exercise-05 output (or a stand-alone incident authored for this exercise); the fixture-registry is an artefact-register row in the FSPC per exercise 01; the mod-111 composition point references mod-111 as the widening surface.
- Every unverified factual claim (specific provider decoding-config exposure, specific target-suite scorer interface, specific IAM-group naming in the payload store, specific FMF or AISI receipt shapes) is marked `<!-- needs-research: ... -->`.

## Stretch goals

- **Author a superseding-fixture-review template.** Chapter 05 pins retirement as governance-gated. Draft the one-page review template a superseding-fixture-review would file — the coverage-comparison shape (superseding fixture's coverage strictly widens the retired fixture's), the review-body composition, the sign-off routing. The registry-entry template (Artefact D) references this review as the `retirement_review_pointer`; the template makes the reference concrete.
- **Fold three fixtures from three different incidents into the same registry template.** Artefact D is authored as a template; instantiate it three times (three incidents, possibly three different classes) and confirm the template's fields carry each fixture's shape without modification. A template that cannot carry a class-2 fixture and a class-4 fixture with the same schema is under-specified.
- **Draft the mod-111 fuzzing-composition specification for one of your fixtures.** Chapter 05's interfaces list names mod-111 as the widening surface. Draft a short specification (not an implementation) describing how mod-111's automated red-team seeds itself from your fixture, what the coverage-widening variation shape is (attack-technique variation, behaviour-category variation, prompt-family variation), and what the CMC section-3 entry (mod-111's chapter 01 CMC) records for the composition. This previews the operating-level integration mod-111 owners will implement.
- **Cross-file the fixture with the mod-109 safety case.** For a fixture whose incident invalidated (or partially invalidated) the pre-deployment safety case, draft the one-page safety-case revision note: which inability-leg claim the incident falsified, how the fixture's future pass constitutes evidence toward a revised claim, and the mod-109 owner named as the counterpart. Chapter 05's interfaces list names mod-109 as a downstream consumer; this makes the routing concrete.

## Deliverable location

Personal notes or private repo. Do **not** commit any of the five artefacts into this course repo. Payload content (fixture prompts, dangerous-capability evidence, tool-response transcripts for agentic fixtures, judge rationale text for the target-suite scoring) never appears in a committed file under any circumstance; the fixture-specification manifest carries `prompt_hash` + `payload_store_pointer` and the payload itself lives in the payload store per chapter 04's redaction discipline. A committed artefact that inlines fixture payload content is a hard fail — it violates the exact discipline the exercise is teaching. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries reference skeletons; use them as shape references only. Verify payload-store URIs and per-role IAM before the handoff pack (Artefact C) is filed with target-suite owners, not after.
