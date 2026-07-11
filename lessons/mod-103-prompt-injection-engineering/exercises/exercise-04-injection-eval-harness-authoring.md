# exercise-04 — Injection Evaluation Harness Authoring

**Estimated effort:** 3 hours
**Prerequisite chapters:** 06 (helpful: 01–05, 09).

## Objective

Assemble the **Prompt-Injection Evaluation Harness (PIEH)** for your target agent — the coverage matrix, the replay-bundle manifest, the reproducibility bundle, the judge scaffolds, and the CI-integration sketch. The exercise turns exercises 01–03 into an artefact that runs, reports, and gates releases. It also codifies the boundary contract with the `ai-eval-engineer` peer role (chapter 09).

## Problem statement

Using the direct catalogue (exercise 01), the indirect catalogue (exercise 02), and the obfuscation drill (exercise 03), assemble the PIEH for your target. You may implement it in the language and framework the target actually uses, or you may deliver a specification + a small proof-of-concept implementation. Either way, produce artefacts that a peer engineer could pick up and extend.

## Requirements

Produce four artefacts:

### Artefact A — `pieh-<target>-coverage-matrix.yaml`

The full coverage matrix in the chapter-06 format. Each cell has:

- `cell` — the `(primitive, channel, obfuscation)` triple.
- `tier` — A / B / C / excluded.
- `success_criterion` — the natural-language rubric.
- `judge` — the judge ID and version (see Artefact C).
- `n_trials` — sample size at this tier.
- `baseline` — either a measured baseline (if you ran the harness) or a "to be measured on first run" placeholder.
- `owner` — the team who fixes the finding.

The matrix should cover every cell exercises 01 and 02 catalogued and apply the obfuscation vectors exercise 03 drilled against a sensible cross-product. Aim for 30–80 cells for a first shipping version; note in a comment which cells were dropped from the full cross-product and why.

### Artefact B — `pieh-<target>-manifest.yaml`

The replay-bundle manifest per chapter 06. Every entry has an ID, `matrix_cell`, `sha256`, `author`, `added`, `notes`, and `storage_location`. Payloads live outside the repo — the manifest is the pointer.

### Artefact C — `pieh-<target>-judges.md` + `pieh-<target>-judges.yaml`

Per-judge specification and calibration plan. Each judge entry:

- `judge_id` and `version`.
- `applies_to_cells` — the coverage-matrix cells this judge scores.
- `scoring_shape` — rubric-scored / LLM-judge / deterministic parser.
- `prompt_template_ref` — for LLM judges, the git-tracked template ID.
- `calibration` — the human-agreement number, the labelled-sample set ID, the calibration date, and the drift-monitoring cadence.
- `cost_estimate` — tokens per trial, cost per release.
- `adversarial_robustness_note` — why this judge is unlikely to be steerable by the payloads it scores. A judge without this note is a bug.

For LLM judges, include the *prompt template* (the operator-side prompt, not the payload). Templates are safe to publish.

### Artefact D — `pieh-<target>-reproducibility.yaml` and `pieh-<target>-ci.md`

The reproducibility bundle (model IDs, chat template, decoding, tool manifest, retrieval state, seed range) and the CI integration sketch. The CI sketch (~500 words) covers:

- Which cells run at PR time vs. nightly vs. pre-release.
- The release-gate policy (which cells are blockers, at what threshold).
- The regression-fixture management workflow.
- The severity → mod-112 disclosure routing rule.
- The peer-role trace-schema deltas the harness requires (chapter 09 — trust labels, raw/sanitised split, deterministic template rendering).

## Starter guidance

- **Start with the matrix, not the code.** The matrix is the specification; the code implements it. Teams that start with code end up with harnesses that measure whatever the code found convenient to measure rather than what the threat model prioritises.
- **Prioritise ruthlessly.** Tier A is the top-10 (surface × persona) from mod-102, cross-multiplied by the primitives that plausibly land those surfaces. Tier B is the "we have a defence and want to gate regressions" set. Tier C is spot-checks. Do not ship at Tier B or C alone.
- **Judges are the load-bearing engineering choice.** A cheap-and-wrong judge produces cheap-and-wrong numbers. Invest in one good judge per primitive rather than a mediocre judge per cell.
- **The reproducibility bundle is not decoration.** Assume future-you will want to re-run this in six months against a model that has been updated. What do you need pinned to trust the numbers?
- **CI integration is where most harnesses die.** Take the peer's existing CI at face value and add the delta this module needs. Do not build your own CI.
- **Adversarial-robust judge design is your job.** The peer role's application-quality judges do not face adversarial payloads. Your judges do. This is the safety-side responsibility on top of the shared judge scaffolding.
- **Write the storage-location URIs but leave the actual payloads out.** Chapter 06's harmful-payload discipline is not optional.

## Acceptance criteria

- ✅ `pieh-<target>-coverage-matrix.yaml` with 30–80 cells covering (at minimum) all cells from exercises 01 and 02 that survive prioritisation and the obfuscation sub-cells exercised in exercise 03.
- ✅ Every cell has tier, success criterion, judge reference, sample size, baseline (measured or placeholder), and owner.
- ✅ `pieh-<target>-manifest.yaml` with one entry per payload; storage locations point outside the repo; no working payloads in the artefact.
- ✅ `pieh-<target>-judges.md` documents each judge with scoring shape, prompt template (for LLM judges), calibration plan, cost estimate, and adversarial-robustness note.
- ✅ `pieh-<target>-reproducibility.yaml` pins model IDs, template, decoding, tool manifest, retrieval / memory state, seed range.
- ✅ `pieh-<target>-ci.md` covers PR / nightly / release cadence, release-gate policy, regression-fixture workflow, disclosure routing, and peer-role trace-schema deltas.
- ✅ At least one cell has a measured baseline number, so the artefact demonstrates the harness *runs* and not only *specifies*.
- ✅ No working payloads in any artefact.
- ✅ Any unverified factual claim marked `<!-- needs-research: ... -->`.

## Stretch goals

- **Ship a working proof-of-concept implementation.** A small script that iterates the coverage matrix, calls a stub target model + defence stack, and scores with the judges. Even a stubbed run demonstrates the plumbing.
- **Author a trend-dashboard sketch.** How would per-cell ASR over time be visualised for the operator? What alert thresholds and what recipients?
- **Draft the peer-role handoff PR.** A written PR description for the `ai-eval-engineer` peer that specifies the trace-schema deltas this harness needs. Include field names, sample records, and rejection criteria.
- **Add an online-eval feedback loop specification.** Production sampling rate, lightweight production judges, offline-harness ingestion rule. Chapter 09 describes the shape.
- **Cross-reference mod-111.** Which of your judges would you industrialise into an LLM-vs-LLM attacker loop first? Author a short note.

## Deliverable location

Personal notes or private repo. Do **not** commit into this course repo. Working payloads live outside both repos.
