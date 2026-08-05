# exercise-04 — Fine-tune a dedicated adversarial attacker model

**Estimated effort:** 3 hours
**Prerequisite chapters:** 04, 02, 06 (helpful: 03, 05, mod-104, mod-106, mod-108).

## Objective

Specify — end to end — a **dedicated adversarial attacker checkpoint** for one concrete target family in your programme: construct the training-corpus manifest under chapter 04's harmful-payload discipline, choose and licence-map an open-weight base, name the fine-tuning objective, define the (ASR, diversity, cost) evaluation against your exercise-03 general-LLM baseline, and record the update cadence. The exercise's output populates the fine-tuned-attacker checkpoint slot of the CMC's section 3 (orchestrator inventory) and the training-corpus provenance of the CMC's section 5 (attack-corpus contract). No corpus text, no checkpoint weights, and no attacker-generated payloads are committed anywhere; the peer `fine-tuning-engineer` (level 30) owns the training pipeline itself.

## Problem statement

Pick **one target family** in your programme's coverage matrix — the same target family you evaluated in exercise 03's PAIR / TAP baseline. Author the specification for a dedicated attacker fine-tuned against that target family. The specification must resolve every decision chapter 04 names as a level-40 responsibility:

- Which **corpus categories** feed the attacker, in what proportions, from which sources — accumulated red-team wins (mod-104 / mod-105 / mod-106 outputs), curated public corpora (HarmBench / JailbreakBench / AIR-Bench by reference), curated public red-team corpora (Ganguli et al., Perez et al. — by citation only), and governed synthetic augmentation whose source-attacker is recorded per entry.
- Which corpus categories are **carved out** and why — CBRN uplift completions, cyber-offense completions requiring the chapter 06 legal-review gate, and user PII derived from production logs. Carve-out is affirmative: the manifest names the excluded categories and cites mod-106's dangerous-capability workflow.
- Which **open-weight base** — one of Meta Llama, Mistral, Qwen, Gemma-family, or equivalent — and how that base's licence and Acceptable Use Policy map to your programme's red-team use. The mapping is a written artefact chapter 04 says is signed at the level-40 tier, not delegated to the peer role.
- Which **fine-tuning objective** — SFT on `(context, attack_prompt)` pairs is the baseline; DPO or judge-reward RL is a stretch. Rationale for the choice is required.
- The **evaluation contract** — ASR + diversity + cost against exercise 03's general-attacker baseline on the *same* cells, on a held-out corpus slice, held-out targets, and held-out behaviours. Report the delta. Diversity collapse is the failure mode you must show you would catch (chapter 04).
- The **update cadence** — quarterly, per-target-rev, or continuous — with the trigger that fires the re-fine-tune.
- The **peer handoff** to `fine-tuning-engineer` (level 30) in the shape chapter 04 names: input corpus schema, base checkpoint hash, training-config values, output-checkpoint reproducibility guarantees, and the routed findings for training-pipeline gaps.

Chapter 06's payload discipline is not optional. The corpus, the checkpoint weights, and any attacker-generated payload text live in the org payload store and appear in committed files only as manifest entries (ID + sha256 + storage URI + access group). This exercise is the highest-risk of the mod-111 set; treat it that way.

## Requirements

Produce four artefacts. Illustrative schema shapes only in committed files; the corpus, the weights, and the generated payloads live in the payload store.

### Artefact A — `cmc-<program>-attacker-corpus-manifest.yaml`

The training-corpus manifest — provenance metadata only, no corpus text. This is the CMC section 5 payload for the fine-tuned attacker.

- `corpus_id` — stable `ATT-CORPUS-<target-family>-<n>` identifier.
- `corpus_version` — semver-shaped; a bump is a re-training trigger (chapter 04).
- `storage_location` — external store URI (private HuggingFace org / restricted S3 / GCS with per-role IAM per chapter 06); the URI is a pointer, not a mirror.
- `access` — the ACL / group / role. The fine-tuning-pipeline principal reads it; the general engineering population does not.
- `sha256` — over the corpus's serialised form, pinned in the manifest.
- `categories` — a list of `{source, count, share, licence, provenance_note}` entries covering:
  - `red_team_wins` — sourced from mod-104 / mod-105 / mod-106 exercise outputs; per-entry provenance is the exercise ID and original judge verdict.
  - `public_jailbreak_corpora` — HarmBench / JailbreakBench / AIR-Bench / in-the-wild DAN-family (Shen et al.) by citation; licence pinned per corpus.
  - `public_red_team_corpora` — Anthropic's released red-team split (Ganguli et al.), Perez et al. release artefacts — by citation; licence pinned.
  - `synthetic_augmentation` — with `source_attacker` (which general LLM produced the rewrite), `judge_confirmed` boolean, and `parent_entry_id` for each augmented entry.
- `carve_outs` — an affirmative list of what does not enter the corpus:
  - `cbrn_uplift_completions` — mod-106 elicited-response payloads are excluded; the *attack prompts* may enter under legal review, the *elicited responses* never do.
  - `cyber_offense_completions_pending_legal_review` — pending the chapter 06 legal-review gate.
  - `user_pii` — scrubbed before the entry reaches the corpus; scrub-verifier version pinned.
- `governance` — the review gate references: legal-review-gate for CBRN / cyber-offense, policy-review-gate for PII scrubbing, licence-review-gate for public-corpus inclusion. Gate outputs are ticketed and cited by ID.
- `retention` — the disclosure-window per chapter 06; the deletion procedure by reference, not a `rm` in a console.

**No corpus text — no attack prompts, no target completions, no judge rationales — in the committed manifest.** The manifest is provenance only.

### Artefact B — `cmc-<program>-attacker-checkpoint-spec.yaml`

The section-3 orchestrator-inventory entry for the fine-tuned attacker. Specification, not weights.

- `attacker_id` — stable `ATT-<target-family>-<n>`.
- `base_model` — `{name, version, weights_uri, sha256}` for the open-weight base (Meta Llama / Mistral / Qwen / Gemma-family / equivalent). Version-pin per chapter 04.
- `licence` — `{spdx_id_or_name, url, terms_hash}` for the base licence. Mark `<!-- needs-research: pin the exact licence-clause text for the chosen base rev -->` because the open-weight licences change across versions.
- `aup_mapping` — a short structured mapping from the base's Acceptable Use Policy clauses to your programme's use. The clauses you are relying on (research / red-teaming carve-out, if any) and the clauses you are affirming compliance with (no harmful-content generation for downstream use, etc.) are enumerated. Mark `<!-- needs-research: Meta / Mistral / Qwen AUP clause text is version-specific; pin at authoring time -->`.
- `fine_tuning_objective` — one of `sft | dpo | judge_reward_rl`. SFT is the baseline chapter 04 names; DPO / RL are stretches. Rationale required.
- `training_config_summary` — `{corpus_version, batch_size, lr_schedule, epochs, held_out_eval_every_n_steps}` — the values that get handed to the peer.
- `output_checkpoint` — `{checkpoint_uri, sha256, signed_against_training_log_merkle_root}`. The checkpoint URI is the payload store; the checkpoint weights are not in the repo.
- `pyrit_target_adapter` — the PyRIT `PromptTarget` adapter shape (chapter 02) the fine-tuned attacker plugs into. The attacker is a *chat target* PyRIT can drive; naming the adapter closes the framework interface.
- `deployment_cells` — the list of CMC section-3 cells where the fine-tuned attacker replaces the general-LLM baseline. Cells where the fine-tuned attacker is *not* deployed (because it did not materially beat the baseline) are recorded explicitly.
- `update_cadence` — one of `quarterly | per_target_rev | continuous_incremental`, with the trigger (accumulated red-team-wins delta, target-family rev event, days-since-last-fine-tune) that fires the re-training.
- `peer_handoff_ref` — the ticket / PR / message ID for the `fine-tuning-engineer` (level 30) handoff. The peer owns the pipeline (chapter 04); this field names where the handoff was routed.

### Artefact C — `cmc-<program>-attacker-eval-report.yaml`

The evaluation report. Per cell, the fine-tuned attacker's (ASR, diversity, cost) against exercise 03's general-attacker baseline on the same cell, plus the held-out slices.

- `baseline_run` — the exercise-03 general-attacker run ID, per cell. This is the reference; the delta is meaningless without it.
- `finetuned_run` — the fine-tuned attacker's run ID, per cell.
- `per_cell` — a list of `{cell_key, asr_baseline, asr_finetuned, asr_delta, ci95, diversity_baseline, diversity_finetuned, diversity_delta, cost_baseline_usd, cost_finetuned_usd, cost_delta}` entries. `cell_key` is the chapter-06 shape.
- `held_out_corpus` — ASR on a corpus slice never seen during training. Materially higher training-ASR than held-out-ASR is memorisation (chapter 04); the manifest surfaces the ratio.
- `held_out_targets` — ASR against targets the attacker was not trained against; collapse to baseline means overfitting to the training-target family.
- `held_out_behaviours` — ASR on behaviour categories the attacker was not trained on. Collapse to baseline is *acceptable* — the attacker is specialised — but the specialisation is recorded so deployment cells cannot silently claim general coverage.
- `diversity_metric` — the metric name and threshold from your programme's CMC section 4 diversity contract. The metric is what catches a mode-collapsed attacker (chapter 04); if the fine-tuned attacker raises ASR and drops diversity below threshold on any cell, the cell keeps the general attacker.
- `judge` — the StrongREJECT-shape judge version + calibration snapshot (chapter 05); the same judge that scored the baseline scores the fine-tuned run. Judge swap in the middle of the comparison invalidates the delta.
- `deployment_verdict_per_cell` — `deploy_finetuned | keep_general | mixed` — the routing decision the CMC section 3 records.

### Artefact D — `cmc-<program>-attacker-runbook.md`

A short (~800–1200 word) runbook covering:

- **Corpus-construction rationale.** Which mod-104 / mod-105 / mod-106 exercises you sourced the red-team-wins from, which public corpora you cited, and why the carve-outs are the carve-outs. Ganguli et al. and Perez et al. are the primary references for the corpus-construction methodology (chapter 04); cite them where they inform the choice, do not paraphrase without citation.
- **Base-model choice and licence discipline.** Which base — Meta Llama / Mistral / Qwen / Gemma-family / equivalent — and why. The licence-clause and AUP-clause mapping is a level-40 sign-off; the runbook is where you narrate it. Mark unverifiable licence text with `<!-- needs-research: ... -->`; do not paraphrase clauses you cannot pin.
- **Fine-tuning-objective rationale.** Why SFT (baseline) vs. DPO vs. judge-reward RL for your target family and corpus shape. Chapter 04 warns that RL-from-judge is reward-hackable and diversity-collapsing; if you chose it, name your entropy-regularisation and judge-panel-diversification remedies.
- **Evaluation methodology.** How the fine-tuned run reuses exercise 03's seeds, budget, judge version, and cells. What the held-out corpus, held-out targets, and held-out behaviours actually are for your programme.
- **The diversity trap.** The load-bearing insight of chapter 04's evaluation section — a fine-tuned attacker typically lifts ASR and lowers diversity. The runbook reports the observed delta and the deployment-verdict per cell (deploy / keep general / mixed).
- **Update cadence and re-fine-tune trigger.** Which cadence — quarterly / per-target-rev / continuous — and the specific trigger event that fires it. Cite the target family's rev cadence as the reason.
- **Peer handoff to `fine-tuning-engineer` (level 30).** The handoff is chapter 04's shape: input corpus schema, base checkpoint hash, training-config values, output-checkpoint reproducibility guarantees. The runbook names the ticket / PR / channel the handoff was routed on and enumerates the routed findings (e.g. "the pipeline does not record corpus-version-hash in the training log; recommend adding a corpus-version-header"). The peer owns the plumbing; this exercise does not re-teach it.
- **Payload-discipline audit.** Confirm that no corpus text, no checkpoint weights, and no attacker-generated payload text landed in a committed file; confirm the CBRN / cyber-offense carve-out fired; confirm the PII scrub-verifier ran. Chapter 06's storage discipline is the reference.
- **Threats to validity.** Corpus staleness relative to the target rev, held-out-slice leakage, judge drift between baseline and fine-tuned runs, base-model AUP amendment during the training window, mode-collapse below the diversity threshold on a cell where the deploy verdict was already recorded.

## Starter guidance

- **Start with the corpus manifest, not the fine-tune.** The load-bearing artefact is the corpus (chapter 04); the checkpoint is downstream. A manifest that names four category buckets and their carve-outs with pinned provenance is the exercise's centre of mass.
- **Reuse your own exercises.** The mod-104 / mod-105 / mod-106 red-team-wins from your programme are the highest-signal training data chapter 04 names. Cite the source exercises by ID in the corpus manifest; do not re-derive them.
- **Public corpora enter by citation only.** HarmBench, JailbreakBench, AIR-Bench, Ganguli et al.'s released red-team split, Perez et al.'s release artefacts — the manifest lists them by URL + licence + version. The corpus text lives at the source, mirrored under access control to the payload store.
- **Carve-outs are affirmative.** A manifest that omits CBRN / cyber-offense / PII silently is not compliant. The manifest names the excluded categories, cites the mod-106 dangerous-capability workflow for the CBRN carve-out, and cites the chapter 06 legal-review gate for the cyber-offense carve-out.
- **Base-model choice is a licensed decision.** Pin the base licence's SPDX identifier (or name + URL) and record the AUP-clause mapping. Meta Llama, Mistral, Qwen, Gemma-family are common candidates; chapter 04 lists them by name. The licence and AUP clauses are version-specific and change; mark them `<!-- needs-research: ... -->`.
- **Do not use a frontier API model as the attacker.** Chapter 04 rules it out — no frontier provider currently sells fine-tuning of their model for adversarial red-teaming at this depth, and the terms-of-service would constrain the corpus. Weights-available base on the operator's infra is the live path.
- **SFT first, DPO / RL if the SFT ceiling justifies it.** SFT on `(context, attack_prompt)` pairs is the chapter-04 baseline. If the SFT attacker's ASR ceiling on a cell is not enough, DPO on `(attack_A, attack_B, preferred)` triples is the next step; judge-reward RL is the strongest but reward-hackable step. Rationale is written down.
- **The fine-tuned attacker is a PyRIT chat target (chapter 02).** It plugs into the same PyRIT orchestrator PAIR / TAP / Crescendo ran under in exercise 03. Naming the `PromptTarget` adapter closes the framework interface; garak and Promptfoo see the same target through the same adapter.
- **The judge does not swap mid-comparison.** The StrongREJECT-shape judge (chapter 05) that scored the exercise-03 baseline is the judge that scores the fine-tuned run. Different judge = invalid delta.
- **Diversity is the trap.** Chapter 04's characteristic failure mode is that a fine-tuned attacker lifts ASR and collapses diversity. Report the delta on both axes; a cell with lower diversity than the CMC section-4 threshold keeps the general attacker even if ASR rose.
- **Held-out sets are three, not one.** Held-out corpus (memorisation guard), held-out targets (target-family overfit guard), held-out behaviours (specialisation-claim guard). Chapter 04 names each; the report names each.
- **Update-cadence trigger is written down.** Quarterly is defensible; per-target-rev is for RSP / Preparedness / FSF tier-decision cells; continuous is for operators with the platform maturity. The trigger event — days since last fine-tune, target-family rev event, accumulated red-team-wins delta — is what fires the re-training.
- **The peer handoff is level-30 craft, not this module's.** `fine-tuning-engineer` owns the SFT / DPO / RL pipeline, the training-cluster orchestration, the checkpoint storage, the training-time evaluation harness, the training-log Merkle-root signing. This exercise specifies the contract; the peer delivers the plumbing. Routed findings — "the pipeline does not stage the corpus on ephemeral mount", "the pipeline evaluates end-only rather than every 500 steps" — are handoffs, not tickets you fix inside mod-111.
- **Payload discipline is enforced by the acceptance criteria, not by intent.** Corpus text, checkpoint weights, and attacker-generated payload text belong in the payload store per chapter 06 with per-role IAM, WORM / lifecycle rules, and disclosure-window retention. The committed manifest carries pointers only.

## Acceptance criteria

- ✅ `cmc-<program>-attacker-corpus-manifest.yaml` names all four corpus categories (accumulated red-team wins, public jailbreak corpora, public red-team corpora, governed synthetic augmentation) with per-category `{source, count, share, licence, provenance_note}` and pinned `sha256` + `storage_location`. **No corpus text — no attack prompts, no target completions, no judge rationales — in the committed manifest.**
- ✅ The corpus manifest names the carve-outs affirmatively: `cbrn_uplift_completions`, `cyber_offense_completions_pending_legal_review`, `user_pii`, each with the reason and the gate reference. A manifest that omits carve-outs silently is rejected.
- ✅ `cmc-<program>-attacker-checkpoint-spec.yaml` pins the open-weight base (Meta Llama / Mistral / Qwen / Gemma-family / equivalent) with `{name, version, weights_uri, sha256}`, records the `licence` block with SPDX identifier or name + URL + terms_hash, and includes an `aup_mapping` block enumerating the AUP clauses relied on and affirmed. Licence-clause and AUP-clause text that cannot be verified at authoring time is marked `<!-- needs-research: ... -->`.
- ✅ The checkpoint spec names the `fine_tuning_objective` (`sft | dpo | judge_reward_rl`) with rationale, the `pyrit_target_adapter` naming the chapter-02 framework interface, and an `output_checkpoint` block whose `checkpoint_uri` is an external payload-store URI. **No checkpoint weights or LoRA adapters committed anywhere in the repo.**
- ✅ `cmc-<program>-attacker-eval-report.yaml` reports per-cell `(asr_delta, diversity_delta, cost_delta)` against the exercise-03 general-attacker baseline on the same cells with the *same judge version* and calibration snapshot. Held-out corpus, held-out targets, and held-out behaviours are each reported.
- ✅ The eval report includes a `deployment_verdict_per_cell` field where cells with diversity below the CMC section-4 threshold record `keep_general` even when ASR rose. Chapter 04's mode-collapse guard is enforced, not narrated.
- ✅ `cmc-<program>-attacker-runbook.md` covers corpus-construction rationale (with Ganguli / Perez citations), base-model and licence discipline, fine-tuning-objective rationale, evaluation methodology, the diversity-trap analysis, the update cadence + trigger, the peer handoff to `fine-tuning-engineer` (level 30), a payload-discipline audit, and threats to validity.
- ✅ The runbook's peer-handoff section names the ticket / PR / channel where the handoff was routed and enumerates at least one routed finding to the training platform. Fine-tuning-pipeline plumbing is not re-taught in the runbook (peer's craft).
- ✅ The `update_cadence` field is one of `quarterly | per_target_rev | continuous_incremental` with a written trigger event; a manifest with cadence unspecified is rejected.
- ✅ Every unverified factual claim — base-model licence-clause text, AUP-clause wording, Ganguli / Perez methodology specifics, benchmark version detail — is marked `<!-- needs-research: ... -->`. Do not paraphrase licence or AUP text you cannot pin.
- ✅ **No attacker-generated payload text is committed** — the fine-tuned model's outputs land in the payload store per chapter 06 with the trajectory-manifest shape from exercise 03 (ID + sha256 + storage URI + access group), never inline.
- ✅ Handoff notes in the runbook name the CMC sections this exercise populates (section 3 orchestrator inventory for the checkpoint, section 5 attack-corpus contract for the corpus manifest, section 6 reproducibility field for the checkpoint sha256) and the mod-108 guardrail consumer that receives the attacker's outputs as adversarial-training data.

## Stretch goals

- **DPO on judge-verified preference triples.** Given the SFT baseline attacker, generate `(context, attack_A, attack_B, preferred)` triples where the preference is the chapter-05 judge's verdict of *which attack succeeded*, then hand the triples to the peer's DPO pipeline. Report the DPO ASR + diversity delta against the SFT baseline on the same held-out slices. Chapter 04 names DPO as the next step past SFT; this stretch exercises the shape.
- **Ensemble-attacker to defeat mode collapse.** Fine-tune two attackers with disjoint corpus slices (or with different entropy-regularisation strengths) and run them as an ensemble in the exercise-03 loop. Report the ensemble's diversity vs. either single attacker; chapter 04's diversity-collapse remedy is what this stretch validates.
- **Per-target-rev cadence rehearsal.** Simulate a target-family rev (a new decoding config, a guardrail update, or a genuinely new checkpoint if you have one available) and re-fine-tune the attacker against the accumulated wins since the last cycle. Report the ASR-decay-then-recovery curve and the wall-clock cost of the rehearsal; per-target-rev cadence is the RSP / Preparedness / FSF tier-decision cell's cadence per chapter 04.
- **Corpus-provenance signing rehearsal.** Extend the corpus manifest so every entry's provenance record is signed against the corpus-version's Merkle root, and the training log records the corpus-version-hash it consumed. This is chapter 04's routed finding to the peer platform (`fine-tuning-engineer`); shipping the signing rehearsal as a pull request against the peer's platform is a legitimate mod-111 output.
- **Cross-target attacker transfer study.** Take a fine-tuned attacker trained against target family A and evaluate it — no re-fine-tuning — against target family B in the same cell. Report the transfer ASR + diversity vs. B's general-attacker baseline. The transfer curve is the empirical basis for CMC section-3's "which cells share an attacker slot" decision.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the corpus manifest, or the checkpoint spec, or the eval report, or the runbook, or (never) the corpus text, or (never) the checkpoint weights, or (never) the attacker-generated payload text — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference specification set with the shapes above pre-populated for one worked target family. Working corpus, checkpoint weights, and attacker-generated payloads live in your org's payload store per chapter 06's harmful-payload discipline; the manifests reference them by ID + sha256 + storage URI + access group.
