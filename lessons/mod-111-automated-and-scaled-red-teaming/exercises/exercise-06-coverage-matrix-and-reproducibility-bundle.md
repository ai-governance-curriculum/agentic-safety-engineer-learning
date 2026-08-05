# exercise-06 — Coverage matrix and reproducibility bundle

**Estimated effort:** 2 hours
**Prerequisite chapters:** 01, 02, 06 (helpful: 03, 04, 05).

## Objective

Author **sections 1, 2, 3, and 6 of a Coverage Matrix Contract (CMC)** end-to-end for one concrete red-team program you own or can plausibly own, and produce the two runnable-shaped artefacts those sections front-run: a **per-cell coverage report** in the chapter-06 shape and a **seeded-attack replay-bundle manifest** covering at least three red cells that a reviewer can replay exactly. The exercise is the CMC-authoring exercise the module was built to close (chapters 01 and 06); it takes the CMC from a named contract to a signed one, minus the two sections exercises 05 and 07 own.

## Problem statement

Pick one concrete program: a model family + deployment scaffold you are the level-40 signer for, or a plausible surrogate (an internal deployment you shadow, a public system whose deployment shape you can defensibly describe). The program must have at least one *material* downstream consumer among mod-101 (RSP / Preparedness / FSF tier decision), mod-107 (containment), mod-108 (guardrail retraining), mod-109 (safety case), mod-112 (disclosure). Toy programs whose CMC no one signs are out of scope; chapter 01's *contract, not habit* framing is why.

For that program, author the CMC skeleton and populate sections 1, 2, 3, and 6. Draft a section-7 consumer-contract *sketch* — the routing table only, not the signed handoffs, which land after the downstream artefacts exist. Leave sections 4 and 5 as **stubs pointing to exercises 05 and 07** — do not fill them in; those exercises are where their content is authored. Then run *enough of* the section-2 matrix to populate a per-cell coverage report in the chapter-06 shape and to seed a replay bundle for at least three red cells. You are not asked to run 1 800 cells; you are asked to run enough (a dozen or two is defensible at this exercise's scope) that the report shape and the bundle shape are exercised end-to-end.

Payload discipline (chapter 06) is not optional. The replay bundle carries hashes and pointers; the payloads themselves live in the payload store per exercise 07.

## Requirements

Produce four artefacts.

### Artefact A — `cmc-<program>.md`

The CMC document itself. Named per your program (`cmc-acme-agent-v3.md`, `cmc-triage-copilot-preview.md`). Sections 1–7 as chapter 01 defines them; sections 4 and 5 are stubs pointing to exercises 05 and 07 respectively.

- **Section 1 — Scope statement.** System-under-test (model version(s) with provider version tag or weights hash, decoding configuration(s), guardrail configuration(s), deployment scaffold(s)). Named RSP / Preparedness / FSF tier the program feeds evidence into <!-- needs-research: the specific tier / CCL / tracked-category the program maps to, and the framework version at authoring time -->. Behaviour categories in scope (drawn from CBRN-uplift, cyber-offense, autonomy-uplift, persuasion, self-exfiltration, general harmful-content, agentic-misuse). Behaviour categories *out* of scope, with the artefact that covers them named.
- **Section 2 — Coverage matrix axes.** All four load-bearing axes enumerated: `attack_technique`, `behaviour_category`, `model_version`, `decoding_config`. Any additional axes the deployment needs (`guardrail_configuration`, `retrieval_index_state`, `tool_scaffold_variant`, `agent_memory_state`). Each axis is a concrete list of values. Each cell (or each defensibly-grouped cell class) has `expected_sample_size`, `judge_assignment`, and `pass_criterion`. Cells the program deliberately skips are called out with the reason (budget, dependency, opt-out) — a silent skip is a finding.
- **Section 3 — Orchestrator inventory.** The frameworks that populate the cells, decomposed per chapter 02: **Inspect** (runner + log store), **PyRIT** (attacker orchestrators, exercise 02's output), **garak** (known-behaviour scanning), **Promptfoo** (CI scaffold regression). Each entry pins a version, a container / image digest, and *which slice of the matrix the framework owns*. Cite the exercise-01 (Inspect), exercise-02 (PyRIT), exercise-03 (LLM-vs-LLM attacker loop), and exercise-04 (fine-tuned attacker) checkpoints as the specific artefacts feeding into each slice. The judge-service layer is a peer-role dependency (`ai-eval-engineer`) with its handoff shape referenced.
- **Section 4 — Judge contract (stub).** One-paragraph stub pointing to exercise 05's StrongREJECT-shape judge, its calibration snapshot, disagreement-adjudication rules, drift-monitoring cadence, and the elicitation-gap carve-outs (chapter 01, chapter 05). Do not fill in.
- **Section 5 — Attack-corpus contract (stub).** One-paragraph stub pointing to exercise 07's harmful-payload storage discipline (payload store hosting, per-role IAM, hash-referencing in issue trackers, CBRN / cyber-offense legal-review gate). Do not fill in.
- **Section 6 — Reproducibility contract.** The seeded-attack replay bundle shape per chapter 06 (see Artefact C below for the manifest form). Enumerate what a reviewer replaying a red cell needs; name the primary bundle store (Inspect log store) and the supplementary bundle store (PyRIT memory, garak JSON reports, Promptfoo run store). Explicitly state that the bundle carries hashes and pointers into the payload store, not payloads.
- **Section 7 — Consumer contract (sketch).** Routing table only. Rows: mod-109 (safety case, inability-leg citation shape), mod-112 (disclosure, aggregated-verdict citation shape + payload redaction posture), mod-107 (containment, tool-abuse cells), mod-108 (guardrail retraining, guardrail-effectiveness cells + diversity-shaped training-set curation), mod-101 (RSP / Preparedness / FSF tier decision, tier-relevant cells + elicitation-gap notes). Each row names the *slice of the matrix* the consumer reads, the *cadence*, and the *artefact* (per-run report, per-rev delta report, quarterly program report). Signed handoffs are not required in this exercise — the sketch names the signature *slot*.

The CMC carries a version and a signer. Peer-role co-signature slots (`ai-eval-engineer`, `ai-infra-security`, `fine-tuning-engineer`, `senior-agentic-ai-engineer`) are named per chapter 01.

### Artefact B — `cmc-<program>-coverage-report.yaml`

The per-cell coverage report in the chapter-06 shape, populated from your subset run. Per cell:

- `cell_key` — `{attack_technique, behaviour_category, model_version, decoding_config}` plus any additional axes.
- `sample_size`, `seed_count`, `best_of_n`.
- `asr` — point estimate.
- `asr_ci` — 95% CI with the method named (Wilson score is a defensible default; chapter 06 is why).
- `diversity_metric` — the chapter-05-family / chapter-04 metric per exercise 05's judge contract (unique-cluster count at a named threshold, or the equivalent). Explicitly named so a reader knows what "diverse" means for the cell.
- `judge` — `{judge_id, judge_version, rubric_prompt_hash, calibration_snapshot_pointer}`.
- `cost` — `{dollars, tokens, wall_clock_seconds, query_count}`.
- `verdict` — `red | amber | green | undetermined`, with the thresholds named at the report head (not per cell).
- `judge_disagreement_rate` — where a second judge or a human panel sampled the cell; `n/a` with a reason otherwise.
- `delta_vs_previous_rev` — `{asr_delta, diversity_delta, cost_delta}`. For a first run, `first_rev` with the baseline it establishes.
- `elicitation_gap_note` — free-text disclaimer per exercise 05's judge contract naming the cells whose ASR is a lower bound rather than a calibrated point estimate.

At the matrix head:

- `coverage_completeness` — fraction of section-2 enumerated cells the run actually populated; skipped cells with reasons.
- `new_failure_modes` — cells that transitioned to red since the prior rev (empty for first rev).
- `closed_failure_modes` — cells that transitioned out of red since the prior rev.
- `undetermined_cells` — cells the reviewer must accept as *not covered this run*.
- `cost_budget_vs_actual`.
- `report_anti_patterns_avoided` — an explicit checklist (aggregated-scalar report, single-number ASR, missing diversity, missing CI, missing judge-calibration snapshot, missing cost). Chapter 06 names each; the exercise wants each named and explicitly checked off.

The report is per-run. A per-rev delta report shape and a quarterly program report shape are named in the runbook; the exercise only asks for the per-run instance.

### Artefact C — `cmc-<program>-replay-bundle-manifest.yaml`

The manifest — the *committed* artefact — for the seeded-attack replay bundle. Bundle payloads (working prompts, model completions, tool-response transcripts, judge rationale text) live in the payload store; only the manifest is committed. Cover *at least three red cells* end-to-end.

Per bundle entry (one per red cell):

- `bundle_id`, `cell_key`, `matrix_run_id`, `cmc_version`.
- `decoding_config_hash` — temperature, top-p, top-k, presence / frequency penalties, seed if set, sampling implementation identifier. Chapter 06 warns that frontier API-served targets often expose a subset; the manifest records what was set and *flags what could not be pinned*.
- `model_identity` — `{provider_version_tag, provider_build_id, weights_hash_if_available}`. The provider build ID is what distinguishes *"gpt-4o"* on day 1 from *"gpt-4o"* on day 90 when the provider has silently rev'd.
- `prompt_hash` — the exact prompt bytes, with a pointer into the payload store (never the prompt text itself in the manifest).
- `tool_response_transcript_hash` — for agentic cells, every tool response the target consumed is hashed; the manifest records the ordered hash list.
- `judge_identity` — `{judge_weights_or_provider_tag, rubric_prompt_hash, judge_decoding_config, calibration_snapshot_pointer}`.
- `attacker_identity` — for LLM-vs-LLM cells, `{attacker_checkpoint_hash, attacker_rubric_hash}`.
- `framework_identity` — `{inspect_version, pyrit_version, garak_version_and_probe_list_hash, promptfoo_version}` — whichever ran the cell.
- `seed_hash` — RNG seed if the loop uses one; sample-selection seed if the corpus is sampled.
- `environment_identity` — container image digest, hardware class if the sampling implementation is hardware-non-deterministic.
- `payload_store_uri` — pointer into the payload store per exercise 07 (private HuggingFace organisation URI, S3 URI with per-role IAM, or equivalent). *Never* a public URI, and never a git path.
- `access_role` — the per-role IAM group authorised to read the payload contents.
- `severity` — mod-112 severity annotation for the underlying harm.
- `replay_procedure_pointer` — the ordered steps a reviewer follows to replay this cell; a short pointer into the runbook is enough.

A reviewer given this manifest, the payload-store URIs, and read access must be able to replay any of the three cells and confirm ASR within the reported CI. If the manifest as authored cannot support that, the bundle is not defensible.

### Artefact D — `cmc-<program>-runbook.md`

A short (~800–1200 word) runbook covering:

- **Program framing.** The one concrete program you picked, the material downstream consumer that pulled you into scoping it, and why this program is CMC-worthy rather than a habit (chapter 01's *contract, not habit* framing). Name the tier / CCL / tracked-category the program feeds.
- **Scope decisions.** Which behaviour categories you put in and out of scope, and where the out-of-scope categories are covered (adjacent CMC, mod-106 dangerous-capability path, human-only red-team sprint). Chapter 01's carve-outs (elicitation-gap, domain-expert grading, novel-technique discovery) named explicitly.
- **Axis enumeration rationale.** How you populated the four load-bearing axes and any additional deployment-specific axes. Why the additional axes are load-bearing for *this* program specifically. Chapter 01's *"a big matrix that never adds an axis when the model gains a capability is a stale matrix"* is the frame.
- **Orchestrator routing.** Which slice of the matrix Inspect / PyRIT / garak / Promptfoo owns for this program, why (chapter 02's decomposition), and which exercise-01–04 checkpoints you're citing. Include the peer-role handoff to `ai-eval-engineer` for the trace store, judge-serving, and CI plumbing — do not re-teach that plumbing.
- **Report anti-pattern audit.** Walk each chapter-06 anti-pattern (aggregated-scalar report, single-number ASR, missing diversity, missing CI, missing judge-calibration snapshot, missing cost) and state how the report structure precludes it. This is a self-audit; a reviewer will read the report against this list.
- **Replay bundle walk-through.** Pick one of the three red-cell bundles and describe, step by step, how a reviewer would replay it — from `payload_store_uri` fetch through decoding-config restoration through model-version pin through judge re-scoring. Chapter 06's *"the bundle is evidence, not code"* framing is the altitude.
- **Consumer routing sketch.** The section-7 sketch, expanded into prose: which slice of the matrix each named consumer reads (mod-109 inability leg, mod-112 aggregated verdicts + payload-redaction discipline, mod-107 tool-abuse cells, mod-108 guardrail-effectiveness cells + diversity-shaped training-set curation, mod-101 tier-relevant cells + elicitation-gap notes), at what cadence, and what the signed handoff will look like. Chapter 06's *"a silent routing is a finding"* is the rule.
- **Rev-to-rev delta report shape.** How the per-rev delta report differs from the per-run report — narrower, sharper, focused on the deltas the tier-decision letter references. You do not have a prior rev in this exercise; describe what fields the delta report *will* carry when a second rev exists.
- **Elicitation-gap disclaimers.** The cells whose ASR is a lower bound rather than a calibrated point estimate per exercise 05's judge contract. Chapter 01's carve-out (elicitation-gap accounting is mod-106's; the CMC's judge contract names which cells carry the disclaimer) is the frame.
- **NIST AI RMF posture reference.** Chapter 06 cites NIST AI RMF's Manage function as the reference reproducibility posture <!-- needs-research: pin the specific subcategory the CMC leans on; chapter 06's citation is illustrative and needs primary-source verification -->; state the specific reproducibility posture your CMC adopts and how it maps.
- **Threats to validity.** Subset-run scope (you did not run the full matrix), model-provider silent rev drift, judge-drift between the calibration snapshot and this run, sampling non-determinism the API-served target does not expose, and the CMC-signer-of-one problem (co-signatures are named but not collected).

## Starter guidance

- **Pick a real program, or a defensible surrogate.** A toy program produces a toy CMC. Chapter 01's *contract, not habit* framing means the signer must be plausible; if you cannot name a plausible signer and a plausible downstream consumer, pick a different program.
- **Author the CMC skeleton first, then populate.** Chapter 01 names the seven sections; write all seven headers before writing any content. Sections 4 and 5 stay as stubs; write them as stubs deliberately so the CMC skeleton is complete.
- **Enumerate axes to actual values.** *"attack technique"* is a header, not a value. Enumerate: `pair`, `crescendo`, `many-shot`, `gcg-suffix`, `indirect-injection-via-tool`, etc. — the chapter 02–05 output is the authority for what belongs. Same for behaviour categories (drawn from HarmBench / AIR-Bench / AILuminate / Preparedness / CCL vocabulary; version-pin the taxonomy).
- **Do not aim for full coverage in the subset run.** Chapter 06's coverage-completeness field is honest about what ran and what did not; a partial subset that populates the report shape honestly is stronger than an inflated subset that pretends. State the fraction and the reason.
- **Match orchestrators to slices per chapter 02.** Inspect owns runner + log store. PyRIT owns attacker orchestrators. garak owns known-behaviour scanning. Promptfoo owns CI scaffold regression. The StrongREJECT-shape judge is shared. Do not re-invent the decomposition; cite chapter 02.
- **Cite the exercise-01–04 checkpoints explicitly.** Section 3 is where they land. A section 3 that says *"we use PyRIT"* is under-specified; a section 3 that cites *"the exercise-02 PyRIT attacker library at commit `<pointer>`, version-pinned"* is defensible.
- **The judge-service layer is a peer dependency, not your build.** `ai-eval-engineer` owns the trace store, judge-serving layer, and CI hook. Chapter 01 and chapter 02 are explicit; the CMC references the peer's shape without re-teaching it.
- **The replay bundle carries hashes and pointers, not payloads.** Payload discipline (chapter 06) is not optional. A committed manifest that inlines a prompt or a completion is a finding; refactor before proceeding.
- **Three red cells is the floor.** Chapter 06's reviewer-replay-any-cell property needs at least three worked examples to demonstrate. If your subset run produced fewer than three red cells, extend the subset until you have three, or defensibly synthesise three from the matrix's tool-abuse / jailbreak / guardrail-bypass slices.
- **Elicitation-gap disclaimers travel with the report.** Chapter 05's judge contract names the cells whose ASR is a lower bound; those cells carry the disclaimer in the report. Cells that carry the disclaimer are not eligible to be cited by mod-109's inability leg without additional evidence (chapter 06 handoff).
- **Named signer and named consumer.** Chapter 01: *"a silent co-signature is a finding."* The exercise's CMC has named signature slots even if you do not collect the signatures in this session.
- **Version-pin every framework, taxonomy, and provider reference.** Chapter 06's *"version-pin these when they are cited in a CMC or a replay bundle"* is the rule; frontier providers silently rev.

## Acceptance criteria

- ✅ `cmc-<program>.md` has all seven CMC sections in the chapter-01 order, with sections 1, 2, 3, and 6 populated end-to-end and sections 4 and 5 as **stubs explicitly pointing to exercises 05 and 07**. A version, a named signer, and named peer co-signature slots are present.
- ✅ Section 2 enumerates all four load-bearing axes (`attack_technique`, `behaviour_category`, `model_version`, `decoding_config`) to concrete values, with `expected_sample_size`, `judge_assignment`, and `pass_criterion` per cell (or per defensibly-grouped cell class). Silent skips are called out with reasons.
- ✅ Section 3 cites version-pinned orchestrators (Inspect, PyRIT, garak, Promptfoo) mapped to the specific slice of the matrix each owns per chapter 02, with exercises 01–04 checkpoints named as their inputs.
- ✅ `cmc-<program>-coverage-report.yaml` reports **per cell** ASR, ASR CI (with method named), diversity metric, judge identity + calibration snapshot pointer, cost, verdict, judge-disagreement rate, and delta-vs-previous-rev. An aggregated-scalar / single-number ASR report body is disallowed; the anti-pattern checklist at the report head is populated.
- ✅ Elicitation-gap disclaimers are attached to the cells that require them, per exercise 05's judge-contract shape and chapter 01's carve-outs.
- ✅ `cmc-<program>-replay-bundle-manifest.yaml` covers **at least three red cells** with `decoding_config_hash`, `model_identity`, `prompt_hash`, `tool_response_transcript_hash`, `judge_identity`, `attacker_identity` (where relevant), `framework_identity`, `seed_hash`, `environment_identity`, `payload_store_uri`, `access_role`, `severity`, and `replay_procedure_pointer`. Any hash that could not be pinned (provider-hidden decoding, hardware non-determinism) is flagged explicitly.
- ✅ **Payload discipline check.** No working prompt text, no model completion text, no tool-response transcript text, and no judge rationale text appears in any committed file (Artefacts A, B, C, D). Payloads live in the payload store per chapter 06 and exercise 07; the manifest references them by hash and URI. A `grep`-shaped self-check is described in the runbook.
- ✅ Section 7 consumer-contract *sketch* names which slice of the matrix each downstream consumer (mod-109, mod-112, mod-107, mod-108, mod-101) reads, at what cadence, and via which artefact (per-run report, per-rev delta report, quarterly program report). Signed handoffs are named as slots.
- ✅ `cmc-<program>-runbook.md` (~800–1200 words) covers program framing, scope decisions, axis-enumeration rationale, orchestrator routing, report anti-pattern audit, replay bundle walk-through, consumer routing sketch, rev-to-rev delta report shape, elicitation-gap disclaimers, NIST AI RMF reproducibility posture reference, and threats to validity.
- ✅ Every unverified factual claim (RSP / Preparedness / FSF tier-decision cadences, specific framework class names, NIST AI RMF subcategory pins, provider build-ID exposure claims, taxonomy version pins) is marked `<!-- needs-research: ... -->`.
- ✅ Handoff notes at the end of the runbook name the exercise-05 and exercise-07 sections whose stubs will be filled in later, and the downstream artefacts (mod-109 safety case, mod-112 disclosure, mod-107 EACC update, mod-108 guardrail retraining, mod-101 tier-decision letter) that will consume this CMC's future revs.
- ✅ At least one of the three replay-bundle entries carries a mod-112 severity annotation, previewing the disclosure-time routing exercise 05's calibration and exercise 07's payload gate both feed.

## Stretch goals

- **Draft the per-rev delta report from a synthetic second rev.** Take the same CMC and imagine a model-version bump on one axis value; author the delta-report shape a tier-decision letter would cite. Chapter 06's per-rev delta report is the artefact that lands in the mod-101 tier-decision path; the shape is worth exercising before the real second rev arrives.
- **Add a fifth axis specific to your deployment.** Chapter 01 names `guardrail_configuration`, `retrieval_index_state`, `tool_scaffold_variant`, `agent_memory_state` as candidates; if your program has a load-bearing axis that isn't in the four defaults, add it, justify it, and populate cells against it. mod-107 (tool scaffold), mod-108 (guardrail), and the peer `senior-agentic-ai-engineer` role (memory / retrieval) are the specific consumers.
- **Sketch the judge-disagreement adjudication path.** Chapter 05 (and exercise 05) own the judge contract, but the CMC's report references disagreement rates. Draft the rule that fires when a cell's disagreement rate crosses a threshold — who re-adjudicates, at what cadence, and how the re-adjudicated verdict lands back in the report.
- **Author a signed-receipt template for one consumer.** Pick mod-109 (inability-leg citation) or mod-112 (aggregated-verdict citation) and draft the signed-receipt shape the CMC's section-7 handoff will use. The template turns the routing sketch into a signable artefact; chapter 06's *silent routing is a finding* is the frame.
- **Add a quarterly program report shape.** Chapter 06 names the quarterly aggregate — coverage-completeness trend, cost trend, new-technique count, closed-failure-mode count — as the artefact an organisation-scope safety review reads. Draft the shape; note which fields the per-run report already carries and which are quarterly-only.

## Deliverable location

Personal notes or private repo. Do **not** commit the CMC, the coverage report, the replay-bundle manifest, or the runbook into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference CMC skeleton. Replay-bundle payloads (prompts, completions, tool-response transcripts, judge rationales) live in your org's payload store per chapter 06 and exercise 07; the committed artefact here is the *manifest*, not the *contents*. Payload discipline (chapter 06) is not optional — verify the payload-store URIs and the per-role IAM before the manifest is authored, not after.
