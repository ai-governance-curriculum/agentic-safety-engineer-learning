# exercise-07 — In-the-Wild Jailbreak Taxonomy → Regression Fixtures

**Estimated effort:** 1 hour
**Prerequisite chapters:** 08 (helpful: 01, 05, 06).

## Objective

Take a small set of **in-the-wild jailbreak samples** (from Shen et al.'s "Do Anything Now" corpus, from the fixture candidates flagged in exercises 03 and 04, or from monitored feeds you have authorised access to) and run them through the chapter-08 **fixture pipeline** — capture → classify → defang → reproduce → register → integrate — producing a **regression-fixture library** entry per sample. The library ships as the JEH's low-cost regression companion to the engineered attackers.

## Problem statement

Assemble at least **5 fixture candidates**:

- **≥2 from Shen et al.'s "Do Anything Now" corpus** or an equivalent public corpus (HarmBench validation split, JailbreakBench validation, HackAPrompt corpus).
- **≥2 from your own exercise-03 or exercise-04 successful runs** — trajectories you flagged as `fixture_candidate: true` in those exercises' manifests.
- **≥1 optional from your org's monitored feeds** (community forums, user reports, internal red-team feeds) *only if you have authorised capture rights and a chapter-08-compliant provenance chain*.

For each sample, execute chapter 08's seven-step pipeline (capture → classify → defang → reproduce → register → integrate → schedule for retire/rotate) and register the fixture in the library. Coverage is not the point of this exercise; **workflow discipline** is.

Payload discipline (chapters 01, 08): fixture *text* lives in the payload store; fixture *metadata* lives in the source repo. Wild-captured samples require additional PII stripping and licence citation per chapter 08.

## Requirements

Produce three artefacts.

### Artefact A — `jeh-<target>-fixture-library.yaml`

The fixture library entries, one per fixture, in chapter 08's schema. Each entry has:

- `id` — stable `FIX-<primary-tag>-<date>-<n>` identifier.
- `source`:
  - `channel` — `reddit | discord | paper | internal | user_report | own_exercise_03 | own_exercise_04`.
  - `url` if public and stable.
  - `captured_by`, `captured_at` (ISO), `original_target`.
  - `licence` — the licence the source corpus / channel carries (Shen et al. corpus licence, JailbreakBench licence, org-internal use-only, etc.). Chapter 08 flags this as mandatory.
- `taxonomy`:
  - `primary` — one of chapter 08's dimensions (persona, fictional_framing, opposite_anti, virtualisation_meta, encoding, competing_objective, refusal_erosion, authority, format_hijack).
  - `composed` — additional dimensions the sample rides.
  - `attack_family` — map to chapters 02–05.
- `classification`:
  - `harmbench_category`, `airbench_category`, `ailuminate_hazard` cells (populate what applies; not every cell applies to every fixture).
  - `severity` — mod-112 severity.
- `storage`:
  - `payload_uri` — external store URI (S3, dedicated internal repo, signed tarball). **The fixture text itself is not in this file.**
  - `sha256` — checksum of the stored payload.
- `defang`:
  - `defang_applied` — `true | false`.
  - `defang_notes` — what changed vs. the raw wild sample (harmful subject substituted, PII stripped, specific policy replaced with a test-safe policy). Chapter 08's defanging tactics are the reference.
  - `defang_reviewer` — who signed off.
- `reproduction`:
  - `target_list` — targets this fixture is run against (versioned).
  - `baseline_asr` — per target, the reference ASR at capture time.
  - `judge` — chapter-07 rubric version + calibration figure.
  - `chat_template`, `decoding`, `seeds`, `n_trials`.
- `history` — list of `{date, run_id, asr per target}` entries; initialised with the capture-time entry.
- `status` — `active | archived | retired`.
- `notes` — provenance narrative and any composition or ethical-consideration notes.

### Artefact B — `jeh-<target>-fixture-pipeline-runbook.md`

A short (~600–1000 word) runbook that reads the library and describes the operating pipeline. Sections:

- **Capture policy.** Which feeds you monitor and why. Legal/ethical constraints per feed (user-report PII stripping, licence obligations for public corpora, disclosure obligations for internal feeds). Chapter 08 lists the concerns; make them concrete for your org.
- **Classification playbook.** For each of chapter 08's taxonomy dimensions, one sentence of when-to-tag and an example (defanged) from your library.
- **Defanging discipline.** For each fixture you defanged, what changed and why. Chapter 08 warns that fixtures whose defanging is impossible without leaving weaponised content may not belong in the library at all; note if any candidate was rejected on this basis.
- **Reproduction schedule.** Which fixtures run per PR, per nightly, per release-candidate (tier-A / tier-B / tier-C from chapter 08's tiered library discussion). How this composes with the exercise-05 coverage-matrix tiering.
- **Retire / rotate policy.** Chapter 08's rule: closed fixtures are archived, not deleted; a fixture zero-ASR across ≥10 releases is a retirement candidate. Note any fixture in your library that already qualifies.
- **Wild-taxonomy feedback loop.** Chapter 08 names this: fixture dimensions feed back into the exercise-02/03/04 attacker LLM system prompts. Which of your fixtures produced a new dimension that should update the attacker templates?
- **Provenance discipline.** How the provenance chain is stored (in-repo metadata + immutable log elsewhere per chapter 08), and how mod-112 disclosure workflow retrieves it.

### Artefact C — `jeh-<target>-fixture-ci-integration.md`

A short (~300–600 word) specification for how the library integrates into CI and the release gate. Contents:

- The CI job that runs the tier-A fixtures per PR (fast, deterministic, low-cost).
- The nightly job that runs tier-B fixtures.
- The release-candidate job that runs the full library and produces the **regression report** (chapter 08: fixtures whose ASR increased this release, fixtures whose ASR decreased to zero).
- The release-gate rule: high-severity fixtures whose ASR *increased* release-over-release block the release.
- The disclosure-routing rule: fixtures whose severity × ASR crossing triggers a mod-112 disclosure ticket.
- The mod-108 signal: fixture regressions where the diagnosis is "guardrail regressed" flow into mod-108's release checklist.

## Starter guidance

- **Chapter 08's seven-step pipeline is the whole exercise.** Follow it literally the first time; skip steps only after you have shipped one full-fidelity fixture and understand why any given step is skippable in your context.
- **Defang before reproducing.** A raw wild sample scored against your target is a live jailbreak; the defanged variant is what the fixture library holds. Chapter 08's defanging tactics are the reference — subject substitution, placeholder tokens, benchmark-provided replacements for CBRN / cyber-uplift.
- **Reject candidates you can't defang.** Some samples are load-bearing on the specific harmful subject; without the subject, the fixture doesn't reproduce the mechanism. Chapter 08 is explicit that such samples may not belong in the library.
- **Provenance chain is not decoration.** Every fixture cites source, capture date, capturer, original target, licence. A fixture without a provenance chain fails the compliance / disclosure test the moment its severity forces a mod-112 review.
- **Taxonomy tags are dual: primary + composed.** Chapter 08's dimensions are orthogonal, not exclusive. A DAN-persona sample in a fictional-framing wrapper with a JSON-format hijack is `primary: persona, composed: [fictional_framing, format_hijack]`, not `primary: persona`.
- **Cite baseline ASR at capture time.** The fixture's baseline is the reference the future release-gate compares against; without it, "the ASR regressed" is a claim without a comparator.
- **Wire into exercise 05's matrix.** Every fixture maps to a chapter-06 cell (chapter 08's composition-with-benchmark discussion). Fixtures without a matrix cell are invisible.
- **Feedback loop.** If a fixture surfaces a dimension your exercise-02 / 03 / 04 attackers don't cover, note it as a template-update for those attackers. Chapter 08's wild-vs-engineered gap is what this loop closes.
- **Payload discipline holds for fixtures too.** Fixture text in the store; metadata in the repo. Even public-corpus samples get stored per your org's policy.
- **Ethical review.** User-report samples are the most sensitive; PII stripping is mandatory; internal escalation before external publication may apply. Chapter 08 flags this.

## Acceptance criteria

- ✅ `jeh-<target>-fixture-library.yaml` contains ≥5 fixture entries in chapter 08's schema, with ≥2 from a public corpus and ≥2 from your own exercise-03 / 04 flagged trajectories.
- ✅ Every fixture entry has `taxonomy.primary` and `taxonomy.composed` populated, benchmark classification cells populated (or explicitly `null` with reason), severity annotated, and a valid `payload_uri` + `sha256`. **No fixture text in the entry.**
- ✅ Every fixture has `defang_applied` set and, if `true`, `defang_notes` + `defang_reviewer`. Fixtures rejected as un-defangable are logged in the runbook with reason.
- ✅ Every fixture has an `history[0]` entry with the capture-time run, a baseline ASR per target, and a `judge` reference with rubric version + calibration figure.
- ✅ Every fixture cites its `source.licence`. Public-corpus fixtures cite the corpus licence; own-exercise fixtures cite the org's use policy.
- ✅ `jeh-<target>-fixture-pipeline-runbook.md` covers capture policy, classification playbook, defanging discipline, reproduction schedule, retire/rotate policy, wild-taxonomy feedback loop, and provenance discipline.
- ✅ `jeh-<target>-fixture-ci-integration.md` covers per-PR / nightly / release-candidate cadence, release-gate rule, disclosure-routing rule, and mod-108 signal.
- ✅ Every unverified factual claim (Shen et al. corpus size, taxonomy dimension churn, specific licence wording) marked `<!-- needs-research: ... -->`.
- ✅ At least one fixture triggers a mod-112 severity annotation and a note about the coordinated-disclosure route.
- ✅ Handoff notes at the end of the runbook name the mod-108 workstream (fixture regressions as guardrail-training signal), the mod-111 workstream (fixture library scaled across the fleet), and the mod-112 workstream (high-severity fixtures for disclosure).

## Stretch goals

- **Author a wild-taxonomy → attacker-template updater.** A short script that reads new fixture dimensions from the library and produces a diff for the exercise-02 PAIR attacker template, the exercise-03 Crescendo phase-skeleton, or the exercise-04 persona set. Chapter 08's feedback loop; the script makes it concrete.
- **Ship a fixture-drift dashboard sketch.** How would fixture ASR over time visualise for the operator? What alarms and what recipients? Chapter 08's release-gate policy consumes it.
- **Cross-reference `ai-risk-engineer` (prerequisite, level 25).** The prerequisite's Promptfoo-based CI harness is a natural runner for tier-A fixtures. Author a short spec of the Promptfoo assertion format the fixtures export to, so the prerequisite's CI harness runs them without adaptation.
- **Author a coordinated-disclosure note.** For one high-severity fixture (defanged), draft the mod-112 disclosure note: what to disclose, to whom, on what schedule. Chapter 08's provenance and mod-112's disclosure workflow compose here.
- **Retire-and-restore drill.** Take a fixture, mark it retired, then imagine a hypothetical model swap that reopens the class. Author the workflow that unarchives the fixture and re-tiers it. Chapter 08's retire-vs-delete distinction is what this drill exercises.

## Deliverable location

Personal notes or private repo. Do **not** commit fixture text, trajectories, judge rationales, or working payloads into this course repo. Fixture *metadata* lives in your org's harness repo per chapter 08; fixture *text* lives in the payload store. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference fixture-library schema. Working payloads live in your org's payload store per chapter 01; see the harmful-payload discipline before starting.
