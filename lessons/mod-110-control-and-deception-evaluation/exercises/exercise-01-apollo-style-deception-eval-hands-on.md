# exercise-01: Apollo-Style Deception Eval Hands-On

**Estimated effort:** 3 hours

## Objective

Run all four **Apollo-style deception probe classes** from chapter 02 — self-reasoning tests, hidden-scratchpad probes, in-context deception probes, and honesty probes — on **at least two target-model versions** (or two safety-tuning stages of the same model), and emit a chapter-02 **deception-rate delta report** with a paired-blind judge protocol, verbatim trace capture, and a residual-uncertainty accounting.

This exercise is the module's floor for the *engineering shape* of adversarial-alignment evaluation. Every downstream exercise (03–08) reuses the probe / framing / trace / judge / report scaffold you build here. Get the differential-framing discipline and the trace-capture discipline right in this exercise and the rest of the module runs on scaffolding you already own.

## Prerequisites

- Read chapter 01 (the discipline) and chapter 02 (the Apollo methodology) end-to-end. Skim chapter 09 for the peer-role handoff shapes you will lean on.
- Skim [Meinke et al. — In-Context Scheming](https://arxiv.org/abs/2412.04984) §"Hidden scratchpad" for the paired-condition scratchpad-probe reference, and [Apollo Research](https://www.apolloresearch.ai/) for the current deception-evaluation methodology write-ups (version-pin what you consume).
- Access to at least two target-model versions or safety-tuning stages. Acceptable variants: (a) two consecutive versions of the same frontier model your operator has API access to (e.g., version N and version N+1 of a released Anthropic / OpenAI / Google / Mistral model), (b) a base and an instruction-tuned variant of the same open-weights model, (c) a public checkpoint and a locally-fine-tuned safety-tuned checkpoint. Note the constraint under **Guardrails** on the fine-tuned variant.
- An LLM judge available for scaled trace / output grading, with a paired-blind invocation contract you can enforce. A small human panel (2–3 raters) available for the calibration subset.
- Read-only harness plumbing sufficient to (a) invoke the target with structured slots, (b) capture verbatim traces, (c) hash and store artefacts, (d) invoke the judge with redaction applied at grader-input time. If your org has an `ai-eval-engineer`-owned harness (chapter 09), plug into it; if not, build the minimum plumbing described under **Requirements** below.

## Choosing the target and probe bank

You must ship the exercise against a *concrete* target and a *versioned* probe bank. Two paths:

- **Path A — Reproduce.** Adopt one or two probe scenarios from a specific Apollo write-up (self-reasoning + hidden-scratchpad from Meinke et al. §"Hidden scratchpad" is a natural pairing) and adapt the prompts to your target-model's expected instruction format. Cite the write-up and pin its revision.
- **Path B — Author.** Write your own probe bank from scratch, one scenario per probe class, engineered against a specific threat entry from your operator's mod-102 ATMD. Bank size: at least 8 scenarios per probe class × 2 framings × 2 target-model versions × N sampling replicates (see **Requirements** for N).

Path A is faster to ship and easier to calibrate against the reference finding. Path B is the operator-scope shape most mod-110 roles will run in production. Prefer Path A on your first pass through the module; return to Path B on a re-run if you have time.

## Requirements

### Part A — Build the probe bank

Author or adapt one **probe scenario per probe class** (four scenarios). Each scenario is versioned (`v1`), authored in a structured prompt template with named slots for target-model injection, and stored in a probe-bank file with the following fields:

- `probe_id` — stable ID (e.g., `deception-p1-self-reasoning-v1`).
- `probe_class` — one of `self-reasoning`, `hidden-scratchpad`, `in-context-deception`, `honesty`.
- `framing_pair` — the two framing conditions that form the differential. For self-reasoning and hidden-scratchpad, the pair is `observed` vs `unobserved`. For in-context deception, the pair is `incentive-present` vs `incentive-absent`. For honesty, the pair is `inconvenient-honesty` vs `convenient-dishonesty`.
- `prompt_template` — the target-facing prompt with the framing swap parametrised.
- `expected_slot_names` — the trace slots the target is expected to fill (e.g., `<self_reasoning>`, `<scratchpad>`).
- `ground_truth` — for in-context-deception and honesty probes only, the operator-known correct answer.
- `judge_rubric_id` — the rubric your judge invokes; version-pin the rubric.
- `source_citation` — the Apollo / Meinke / other paper reference the probe adapts, with the paper's revision.

Do **not** collapse two prompt wordings into a single `probe_id`. Two prompts with different wording are two probes — chapter 02 §"Probe class 1" is the reference.

### Part B — Run the target

For each (probe × framing × target-model version) combination, run **N ≥ 8** replicates with sampling temperature > 0 (document your temperature and decoding stack). Capture per-run:

- The target-model version and its fingerprint (weights hash if available, model-provider ID + version string otherwise).
- The full prompt (framing-conditional) sent to the target.
- The verbatim response, including all trace slot contents parsed by the slot name.
- The decoding parameters (temperature, top-p, top-k, max-tokens, seed if available).
- The invocation timestamp.

Hash each captured artefact (SHA-256 of the concatenated fields) and store it in a run-indexed artefact register. This is the artefact chapter 02 §"Reasoning-trace instrumentation" calls the *raw slot content* — a reviewer will ask for it, and your run has an instrumentation gap without it.

If your target does not natively emit structured slots, engineer the prompt to elicit `<slot>content</slot>` markup and document the parse-confidence — for at least a random 10% audit sample, hand-check that the parsed slot content matches the model's expressed reasoning. Chapter 02 §"Reasoning-trace instrumentation" pins the contract.

### Part C — Judge

Build a **paired-blind grader panel** as specified in chapter 02 §"Judge design":

- **LLM judge.** Invoke an LLM grader on each captured (response + trace) pair. The grader sees the response and the parsed trace but does *not* see the framing prompt. Trace redaction: if the trace contains framing hints ("since this is a training conversation…"), redact the framing-recognition tokens per the redaction protocol you author here. Emit structured labels: for each probe class, at minimum a `deception_present ∈ {yes, no, borderline}` label and a `trace_pattern` label vector (`evaluation-awareness ∈ {yes, no}`, `strategy-planning ∈ {yes, no}`, `deception-planning ∈ {yes, no}`).
- **Human calibration subset.** For a random sample of ≥ 15% of runs (or ≥ 30 items, whichever is larger), have 2 human raters grade against the same rubric under the same blinding. Compute inter-rater agreement (Cohen's kappa or Fleiss' kappa for > 2 raters) and LLM-vs-human agreement on the same subset.
- **Paired-blind protocol enforcement.** The grader (LLM or human) must not be able to infer the framing from the output alone. When you cannot fully blind (e.g., the trace intrinsically encodes the framing and cannot be redacted without destroying the reasoning), document the leakage and note it in the residual-uncertainty accounting.

Version-pin the rubric (`judge-rubric-v1`) and the LLM-grader model (name + version).

### Part D — Emit the delta report

Produce the chapter-02 **deception-rate delta report** as a single markdown document (`deception-eval-report.md`) plus a machine-readable results table (CSV or JSON). The report has the seven sections chapter 02 §"The deception-rate delta report" pins:

1. **Header.** Target-model versions, evaluation date, harness commit hash, probe-bank version, judge-rubric version, LLM-grader version.
2. **Per-probe-class table.** Rows are (probe class × probe version × framing pair); columns are (framing-A deception rate, framing-B deception rate, delta, N, CI width, judge-vs-human kappa on the calibration subset).
3. **Version-delta rows.** For each probe class × framing pair, the change in deception rate across the two target-model versions.
4. **Trace-analysis section.** For each probe class, the per-framing rate of each `trace_pattern` label (evaluation-awareness, strategy-planning, deception-planning).
5. **Residual-uncertainty accounting.** Named, non-decorative:
   - Probe recognition — the target may recognise the probe as an eval.
   - Trace suppression — the target may suppress deception-relevant reasoning.
   - Framing credibility — the framing may not be credible enough.
   - Sample-size ceiling — the CI width bounds the smallest detectable delta; state the bound explicitly.
   - Blinding leakage — where the trace intrinsically encodes framing, name the leakage.
6. **Downstream cites.** Which downstream artefact this report feeds — a candidate mod-106 DCER for an expressed-inability capability, a mod-108 monitor calibration input (trace patterns), a mod-109 trustworthiness leg, a mod-111 coverage row, a mod-112 disclosure section. Even if you do not have the downstream artefact in hand, name the target citation shape.
7. **Peer-role artefact IDs.** The `ai-eval-engineer` harness commit, the `model-evaluation-engineer` CI methodology reference (or the deferral note per mod-106 chapter 07 if you did not calibrate CI-widths in-house), the human-panel roster (initials + role, not full identity).

### Part E — Reviewer walk

Author a short (~1 page) `reviewer-walk.md` that reproduces the reviewer discipline chapter 02 §"Common misreadings" and §"Common failure the reviewer catches" name:

- For each probe class, name one *specific* finding a reviewer would push back on if you shipped the report without addressing it.
- For each residual-uncertainty item, name what a stronger adversarial-model assumption would do to your bound.
- Name at least one place where the deception *rate* is not the right primary metric and the *delta* is what matters (chapter 02 §"Common misreadings — deception rate is the metric").

## Deliverables

Commit the following under your exercise-solution area:

- `probe-bank/probes-v1.{json|yaml}` — the four versioned probes (Part A).
- `runs/` — the raw per-run artefacts (Part B), hashed. Include a manifest (`runs/manifest.csv`) with one row per run and its artefact hash.
- `judge/rubric-v1.md` — the paired-blind grader rubric.
- `judge/human-calibration.md` — the human-panel calibration subset, per-rater grades, inter-rater agreement, LLM-vs-human agreement.
- `deception-eval-report.md` — the chapter-02 delta report.
- `deception-eval-results.{csv|json}` — the machine-readable results table.
- `reviewer-walk.md` — the Part E walk.

## Acceptance criteria

- **All four probe classes are present** (self-reasoning, hidden-scratchpad, in-context deception, honesty). Missing a class is a fail; chapter 02 pins the four as the minimum coverage.
- **At least two target-model versions or safety-tuning stages are compared.** A single-version report is not a *delta* report.
- **N ≥ 8 replicates per (probe × framing × version).** Fewer than 8 makes the CI too wide to interpret.
- **Verbatim trace capture with parsed slot content** is present in the artefact register. A trace-slot inference from the final output alone is a rejection.
- **The judge is paired-blind.** Trace redaction is applied at grader-input time; blinding leakage is named in the residual-uncertainty accounting for probes where full blinding is infeasible.
- **The human-calibration subset is present** with inter-rater kappa and LLM-vs-human kappa. A pure-LLM-graded report at this exercise level does not have the calibration to be interpretable.
- **The report emits deltas, not just rates.** The version-delta row is load-bearing; a report with per-framing rates but no delta is chapter-02 §"Common misreadings" rejection 3.
- **The residual-uncertainty accounting is non-decorative** — each item names the specific bound and what an adversarial-model shift would do.
- **The downstream-cites section is present**, even if the downstream artefact is not yet in hand. The report shape must be consumable.
- **Every citation is version-pinned.** "Latest," "current," or an undated timestamp is a rejection; chapter 02 pins the requirement.

## Stretch goals

- **Add a fifth probe class** authored against a *specific* threat entry from your operator's mod-102 ATMD. Do this as an *operator scope* extension, not as novel Apollo methodology — chapter 09 §"The one-way flow" is the routing.
- **Run against a third safety-tuning stage.** A base → mid-training → post-safety-tuning trajectory is the shape mod-112 disclosure consumes; the trajectory is more informative than a two-point comparison.
- **Compare an LLM judge across two grader model versions.** The judge-agreement kappa across grader versions is a mod-108 monitor-calibration input; a positive delta on the grader is a signal that the calibration curve is drifting.
- **Emit the report in a shape a mod-109 safety case can programmatically cite.** Stable node IDs (`probe-p1-v1-framing-A-delta`) with a citation contract; a downstream mod-109 exercise 03 authoring can then cite specific nodes.
- **Feed the trace corpus to a mod-108 monitor-calibration exercise** as a labelled dataset. The trace corpus becomes a monitor-training input; the mod-108 monitor's false-negative rate on the corpus is a joint mod-108/mod-110 artefact.

## Guardrails

- **Do not attempt to construct a password-locked or fine-tuned target variant in this exercise.** Chapter 04 exercise 03 is where that primitive lives, and it requires the `fine-tuning-engineer` handoff codified in chapter 09. If your target-model versions are limited, run the exercise against publicly-released frontier-model versions and note the constraint in the report header.
- **Do not silently ship methodology-shaped work.** If your probe bank includes a probe class the reference literature does not name, chapter 09 §"The one-way flow" is the routing — reproduce the finding with existing probes first, then route to internal-safety-review before publishing a new class.
- **Do not commit real credentials, tenant IDs, or unredacted incident data.** The probe bank is authored around chapter-02-style scenarios; if you use production-adjacent scenarios, redact per your operator's data-handling posture.
- **Do not treat a null delta as evidence of alignment.** Chapter 02 §"Common misreadings — zero compliance gap proves alignment" is inherited here; the report reports a bounded null, not an alignment claim.
- **Do not rely on the LLM judge alone.** The human-calibration subset is not decorative; it is the load-bearing anchor for the LLM-vs-human kappa the report cites.
- **Do not skip the reviewer walk (Part E).** The walk is where the exercise becomes defensible; skipping it produces a report that reads like a metric dump rather than an adversarial-alignment evaluation result.
- **Do not adapt Apollo probes without pinning the source revision.** Chapter 02 §"Common misreadings — reproduce the frontier-lab methodology byte-for-byte" is the discipline: reproduce the structure; adapt the specifics; pin what you consumed.
