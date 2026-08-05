# exercise-01 — GCG Adversarial-Suffix White-Box Attack

**Estimated effort:** 3 hours
**Prerequisite chapters:** 01, 02 (helpful: 06, 07, 09).

## Objective

Stand up a **GCG (Greedy Coordinate Gradient) white-box attacker** against one concrete open-weight target, train a **universal** adversarial suffix over a small batch of harmful requests, and produce a **transferability report** measuring the suffix's ASR against at least one closed-weight target under a StrongREJECT-style judge. The exercise produces the first row of your JEH's attacker report (chapter 02) and the first cell of the composite coverage matrix (chapter 06).

## Problem statement

Pick a concrete **open-weight source model** you can load onto a single GPU (an open Llama, Mistral, Qwen, or similar 7–13B checkpoint is a defensible default; if you have multi-GPU capacity, add a second family for the ensemble step) and at least one **closed-weight target** you have API access to (any current frontier chat API). If you already chose a target for the earlier modules' PIEH / ATMD work, reuse it as the closed-weight target so exercises stack.

Run GCG per chapter 02 against the source, using a small held-out **harmful-request batch** drawn from a published benchmark (HarmBench validation split, JailbreakBench behaviours, or AdvBench). Report both **universality** (ASR on a held-out request set with the same source model) and **transferability** (ASR of the same suffix against the closed target).

Payload discipline (chapter 01) is not optional: the trained suffixes and any successful compliance completions live in an access-controlled payload store, not in this deliverable. What you commit is the *shape* of the run, the *rate*, and the *reproducibility bundle* — never the working string.

## Requirements

Produce four artefacts.

### Artefact A — `jeh-<target>-gcg-run-config.yaml`

The run's reproducibility bundle in chapter 02's schema. At minimum:

- `source_ensemble` — list of source models with checkpoint hash, tokenizer hash, and chat-template ID for each. Even a single-model run names the ensemble as a list of one.
- `closed_targets` — list of closed-weight targets with the exact model / endpoint ID and the date of the snapshot used.
- `request_batch` — the harmful-request set used for training and its held-out complement. Reference by benchmark + behaviour IDs; do not paste the strings. Log the benchmark version.
- `compliance_prefixes` — the target completions $y$ per request. Reference by ID.
- `suffix_len`, `top_b`, `steps`, `loss_variant`, and any regulariser weights.
- `seeds` — the seed range you ran; a minimum of 3–5 seeds is expected so the ASR carries a CI.
- `chat_template` — the exact renderer version. Mismatched templates silently under-converge; log this explicitly.
- `wall_clock_s` and `dollars` — the total run cost.
- `judge_ref` — the chapter-07 StrongREJECT-style judge ID and rubric version used to score responses.
- `snapshot_date` — ISO date of the transfer runs.

### Artefact B — `jeh-<target>-gcg-report.yaml`

The per-suffix report. Every trained suffix has an entry with:

- `suffix_id` — stable ID resolved in the payload store; the suffix text is **not** in this file.
- `universality`:
  - `train_asr` — ASR on the training request batch (source model), with 95% CI across seeds.
  - `holdout_asr` — ASR on the held-out request batch (source model), with CI. The gap between the two is the universality-generalisation error and is the whole point of reporting both.
- `transferability`:
  - per closed target, ASR with CI under both the target's decoding config in scope and (where feasible) a second decoder for sensitivity.
  - Per-target judge score distribution, so a single high-composite finding does not hide behind an average.
- `refusal_style` — a short qualitative note per target about how the target *fails* (compliant preamble then refusal, compliance with hedges, full compliance, template drift, etc.). This is the material chapter 07's judge rubric was designed around.
- `defence_stack_notes` — which defences were in the loop (a perplexity filter? SmoothLLM? classifier guard? Nothing?). A GCG number under "no defence" is not the same finding as one under the deployed stack.

### Artefact C — `jeh-<target>-gcg-runbook.md`

A short (~800–1500 word) runbook covering:

- **Environment.** GPU model, VRAM, driver / CUDA / PyTorch versions, model-loader library and version. GCG's throughput is memory-bound; the runbook lets a colleague reproduce the wall-clock.
- **Batch composition rationale.** Which HarmBench / JailbreakBench categories the batch covers and which it deliberately doesn't, and why. A batch concentrated in one category will under-generalise (chapter 02).
- **Loss and knob choices.** Which loss variant (target-only, target + anti-refusal, contrastive) and why. Suffix length, top-$B$, step budget — the specific numbers and the reasoning.
- **Chat-template proof.** How you verified the source model's chat template renderer matches the version the target's fine-tune expects.
- **Judge configuration.** Which StrongREJECT-style judge you used (chapter 07), where its calibration figure comes from, and what its per-response cost is.
- **Threats to validity.** What could make this number wrong: seed underrun, snapshot drift on the closed target, benchmark-version drift, defence stack you weren't aware was in the loop.

### Artefact D — `jeh-<target>-gcg-manifest.yaml`

The payload-store manifest for the suffixes and successful completions this run produced. One entry per artefact:

- `id` — stable ID (`GCG-SUF-<target>-<date>-<n>` or similar).
- `kind` — `suffix` or `completion`.
- `sha256` — checksum of the payload as stored.
- `storage_location` — the external store URI (S3 bucket, dedicated internal repo, signed tarball path).
- `matrix_cell` — the coverage-matrix cell in chapter-06 format that this artefact lands on.
- `severity` — mod-112 severity annotation for the underlying finding.
- `access` — the ACL / group / role that can read the payload.

## Starter guidance

- **Use `nanoGCG` (or an equivalent modern reproduction) — do not re-derive the loop.** Chapter 02 lists it under variants. The engineering wins are in the batching, the chat-template plumbing, and the judge — not the discrete search itself. Time spent re-implementing the gradient-guided search is time not spent on measurement quality.
- **Start with a tiny request batch and a small step budget.** Fifty requests × 500 steps is enough to shake out the environment, the chat template, the judge, and the store. Scale after the plumbing works.
- **Run at least 3–5 seeds from the start.** GCG's optimisation is randomised; a single seed produces a single suffix that may or may not be representative. The paper's headline numbers are averages over trials.
- **Split train / holdout up front.** A universality number reported on the training set is not a universality number; it is a fit number.
- **Diversify the training batch across benchmark categories.** A batch drawn only from one HarmBench category will produce a suffix that fails to generalise. Chapter 02 names this as an engineering point; the exercise's holdout ASR forces you to confront it.
- **Pin the chat template renderer version explicitly.** Wrong chat template silently degrades convergence; the runbook has a checkbox for this specifically because it is the most common quiet failure mode.
- **Score with a StrongREJECT-style judge, not a refusal-word regex.** Chapter 07 discusses the over-count in detail. A regex-judged transfer ASR is a story; a rubric-judged transfer ASR is a data point.
- **Do not paste suffixes into the runbook or the report.** The manifest points at the store; the store holds the payload. Chapter 01's payload discipline is enforced here.
- **Report cost.** Dollars, tokens for closed-target queries, GPU-hours for the training. Mod-111 (scaled red-team) is what industrialises this, and cost is what it plans against.

## Acceptance criteria

- ✅ `jeh-<target>-gcg-run-config.yaml` present with every field in chapter 02's reproducibility bundle populated. Ensemble is a list, targets are versioned, seeds are ≥3, chat template is pinned, judge is referenced.
- ✅ `jeh-<target>-gcg-report.yaml` reports **universality** as *both* train and holdout ASR with CIs and reports **transferability** per closed target with CIs. Aggregated-only numbers are rejected.
- ✅ Every ASR figure is scored under a StrongREJECT-style judge (chapter 07); refusal-word regex judges are explicitly disallowed for the headline numbers. If a regex judge appears in a cascade first pass, the rubric judge is the final scorer.
- ✅ `jeh-<target>-gcg-runbook.md` covers environment, batch composition rationale, loss / knob choices, chat-template proof, judge configuration, and threats to validity.
- ✅ `jeh-<target>-gcg-manifest.yaml` lists every suffix and every stored completion with IDs, sha256, storage URI, matrix cell, severity, and access group. **No suffix text and no completion text appears in any committed file.**
- ✅ Every unverified factual claim (specific Zou et al. transfer rates, benchmark version details, closed-target defence-stack contents) is marked `<!-- needs-research: ... -->` rather than guessed.
- ✅ The report cites the mod-112 severity for at least one non-zero-ASR cell so downstream disclosure routing is not an afterthought.
- ✅ Handoff notes at the end of the runbook naming (a) which cell this row occupies in the chapter-06 composite coverage matrix, (b) which mod-108 guardrail workstream would consume the adaptive-attack-survival extension, and (c) which mod-111 interface this run's report satisfies.

## Stretch goals

- **Add a second source model to the ensemble** and re-train the suffix under equal-weight averaging. Report the transferability delta against the single-source baseline. Chapter 02's transferability discussion depends on the ensemble.
- **Sweep suffix length** across $k \in \{16, 20, 32, 64\}$ and report the trade curve: train ASR, holdout ASR, transfer ASR per length. Longer isn't automatically better; chapter 02 says why.
- **Run a defence-adaptive round.** Put a perplexity filter or SmoothLLM in the loop and re-train the suffix against it. Report the **adaptive-attack survival** figure (chapter 07's third derived metric). This is the honest number a mod-108 guardrail workstream needs.
- **Sweep decoding on the closed target.** Report transfer ASR at $T \in \{0.0, 0.7\}$ and any other decoder in the target's deployed envelope. Frontier deployments run at various temperatures; a number at one temperature is not the deployment's number.
- **Cross-benchmark the batch.** Train on HarmBench, evaluate holdout on JailbreakBench, and vice versa. Cross-benchmark holdout ASR is a stronger claim than same-benchmark holdout ASR.
- **Author a nanoGCG PR-quality diff** of any local changes you needed to make (chat-template handling, custom loss variant, checkpointing). Even if you don't upstream it, the diff belongs in the runbook.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the trained suffixes, or the compliance completions — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference run. Working payloads (suffixes and successful completions) live in your org's payload store per chapter 01; see the harmful-payload discipline before starting.
