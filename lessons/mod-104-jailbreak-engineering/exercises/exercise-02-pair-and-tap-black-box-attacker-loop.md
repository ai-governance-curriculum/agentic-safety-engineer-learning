# exercise-02 — PAIR and TAP Black-Box Attacker Loop

**Estimated effort:** 3 hours
**Prerequisite chapters:** 03, 07 (helpful: 01, 06, 09).

## Objective

Stand up a **PAIR** loop and a **TAP** loop (chapter 03) — LLM-vs-LLM attacker/target/judge cycles — against one closed-weight target API, under an engineered **cost, rate-limit, and stopping-criterion envelope**. Report per-objective ASR, queries-to-success distribution, and dollars-per-successful-attack — the shape the mod-111 scaled harness will consume. This exercise stands up the black-box side of your JEH; exercise 01 handled the white-box side.

## Problem statement

Pick a **closed-weight target** with a public API and a **legitimate authorised-red-team context** (your own project's target from exercise 01, an internal system you own, or an authorised evaluation endpoint). Do not run this exercise against production systems you have no authorisation for; chapter 01's discipline and mod-112's disclosure workflow apply.

Pick an **objective set** — 5–20 adversarial objectives — drawn from **JailbreakBench** (100 behaviours with paired benign counterparts) or **HarmBench** (categorical behaviour set). The JailbreakBench harmful/benign pairs are the recommended default because they let you report **over-refusal** on the benign pairs alongside ASR on the harmful ones, as chapter 07 requires.

Run both loops against every objective:

- **PAIR** — a single evolving adversarial prompt per objective, iterated up to $N$ queries under judge feedback.
- **TAP** — a tree-of-attacks search with branch factor $B$, width $W$, depth $D$, and an off-topic pruner.

Every finding lands in the JEH's coverage matrix (chapter 06). Every trajectory (prompt / response / judge score / judge rationale per iteration) lives in the payload store, not this deliverable.

## Requirements

Produce five artefacts.

### Artefact A — `jeh-<target>-pair-tap-run-config.yaml`

The run configuration. At minimum:

- `attacker`: model ID, snapshot, system-prompt template ID, decoding config. Chapter 03's attacker template is the reference; log any modifications.
- `target`: model ID, snapshot, endpoint URL family, production or approximated system prompt, decoding config in scope.
- `judge`: model ID, snapshot, chapter-07 StrongREJECT-style rubric version, calibration figure and its measurement date.
- `objectives`: benchmark + behaviour IDs (JailbreakBench behaviours preferred; HarmBench allowed). Reference by ID, do not paste. Note whether benign pairs are included.
- `pair_cfg`: `max_queries`, `threshold`, `seeds` (≥3).
- `tap_cfg`: `branch`, `width`, `depth`, `threshold`, `off_topic_pruner` (its model + prompt template), `seeds`.
- `budget`: per-attack dollars, per-attack wall-clock, per-run global dollars ceiling.
- `rate_limit_envelope`: attacker RPM, target RPM, judge RPM; retry policy (backoff, jitter, max retries); non-2xx handling rule.
- `stopping_criteria`: which of `success | budget | plateau | off_topic` are enabled; per-criterion parameters.
- `snapshot_date`: ISO.

### Artefact B — `jeh-<target>-pair-tap-report.yaml`

The per-objective, per-loop report. Every (objective, loop) has an entry with:

- `asr` with 95% CI across seeds.
- `queries_to_success` — distribution across trials that reached the threshold. Median, 90th percentile, max. For PAIR this is the iteration index; for TAP it is total target queries at depth-of-success.
- `judge_score_distribution` — histogram of final judge composite scores across trials, not just the max.
- `stopping_criterion_hit` — histogram of which stop fired (success / budget / plateau / off_topic / rate_limit).
- `refusal_style` — qualitative one-paragraph note per objective on how the target failed (or refused). Chapter 03 lists the failure-mode patterns; the note pattern-matches the observed style against them.
- `attack_family_tags` — for each successful attack, whether the successful prompt is a jailbreak (this module) or has an injection component (mod-103). Cross-tagging is mandatory: prompt-echo attacks that look like jailbreaks are frequently injections and must route to mod-103's coverage matrix as well.
- `benign_pair_over_refusal` — if JailbreakBench benign pairs were included, the target's over-refusal rate on them under the same judge.
- `cost` per objective: dollars, tokens (attacker + target + judge), wall-clock. `cost_per_successful_attack` at the end.

### Artefact C — `jeh-<target>-pair-tap-trajectories-manifest.yaml`

The payload-store manifest for the (prompt, response, judge score, judge rationale) trajectories. One entry per trajectory:

- `trajectory_id`, `objective_id`, `loop` (`pair` or `tap`), `seed`, `sha256`.
- `storage_location` — external store URI.
- `matrix_cell` — the chapter-06 cell this trajectory populates.
- `severity` — mod-112 severity annotation for the underlying finding.
- `access` — the ACL / group / role that can read the trajectory.

Trajectory *text* — the adversarial prompt, the target response, the judge's rationale (which quotes the harmful content by construction; see chapter 07) — is not in this file.

### Artefact D — `jeh-<target>-pair-tap-runbook.md`

A short (~800–1500 word) runbook covering:

- **Attacker choice.** Why this attacker model / template. What its legitimate-red-team system prompt is, why it is uncensored enough to author adversarial prompts, and how you verified it doesn't self-refuse mid-loop. Log if you had to swap attackers.
- **Judge choice.** Why this judge model, chapter-07 rubric version, and how it is *independent* of the attacker (different family / provider preferred). What the calibration figure means and how stale it is.
- **Cost projection.** Extrapolate from this run to the full JailbreakBench 100-behaviour set, or the full HarmBench behaviour set. Report dollars per benchmark row; this is the number mod-111 plans against.
- **Rate-limit envelope.** The RPM ceilings you observed vs. the ones the providers publish; how many 429s you saw and how the retry policy handled them; a note on which provider limit is likely to bind under mod-111's scaled runs.
- **Failure-mode audit.** Which of chapter 03's failure modes you actively watched for (judge over-acceptance on near-miss content, attacker drift, compliance-word triggering, judge-attacker collusion, provider throttling that looks like refusal, prompt-echo injection); which ones fired; what you did about it.
- **PAIR vs. TAP.** At equal dollar budget, which found more successful jailbreaks and at what fraction of the query cost. If TAP loses to PAIR on your target, name the reason (branching too wide, off-topic pruner mis-calibrated, threshold too high). Chapter 03's TAP-beats-PAIR claim is conditional; this exercise verifies it or refutes it on your target.
- **Threats to validity.** Seed underrun, snapshot drift, provider system-prompt substitution, judge calibration staleness, benchmark version drift.

### Artefact E — `jeh-<target>-pair-tap-ci.md`

A short (~400–800 word) CI-integration sketch:

- Which subset runs per PR, which per nightly, which per release-candidate.
- The release-gate policy — which cells block release at what ASR threshold, which trigger a mod-108 review, which trigger a mod-112 disclosure ticket.
- How trajectory storage is retained / rotated (chapter 01 discipline).
- Which chapter-06 matrix cells this run populates, and which stay `NA` until other exercises fill them.

## Starter guidance

- **Use published reference implementations, not roll-your-own.** JailbreakBench ships reference PAIR; TAP has published implementations. Chapter 03 says "cite the template version" — use the reference template first, log modifications second.
- **Start with 5 objectives, not 100.** A five-objective run at three seeds shakes out the attacker prompt, the judge, the rate limit, the retry policy, the trajectory storage, and the cost model. Scale to the full benchmark after the plumbing works.
- **Sweep attackers and judges at least once.** Chapter 03 warns explicitly against reporting one attacker–target–judge triple as the number. Run two attackers × two judges even at low object count. The range is the finding; the max is not.
- **Enable the off-topic pruner for TAP from the start.** Skipping it makes TAP look expensive without a corresponding ASR lift, which is the wrong shape of finding.
- **Log every non-2xx from the target separately.** A rate-limited 429 is not a refusal; a 5xx is not a compliance failure. Chapter 03's failure-mode audit needs this data or the numbers are polluted.
- **Judge model must not be the attacker model with a different system prompt.** Chapter 03's judge-attacker collusion warning is empirical, not stylistic. Pick a different family / provider; log the choice.
- **Match the target's production decoding config.** A jailbreak at $T = 0.7$ isn't the deployed system's jailbreak if the deployed system runs at $T = 0.2$. Log the delta if you can't match.
- **Cross-tag findings** for injection vs. jailbreak. Attacker LLMs discover role-hijack, system-prompt spoof, and other mod-103 primitives. If your report shows only jailbreak tags, you probably missed the cross-tagging; chapter 03's failure-mode audit lists prompt-echo attacks explicitly.
- **Never paste trajectories into the report.** Trajectory rationales quote the harmful content by construction (chapter 07). Manifest points at store; store holds text.

## Acceptance criteria

- ✅ `jeh-<target>-pair-tap-run-config.yaml` covers attacker, target, judge, objectives, both loop configs, budget, rate-limit envelope, and stopping criteria. Seeds ≥3.
- ✅ `jeh-<target>-pair-tap-report.yaml` reports per-objective per-loop ASR with CI, queries-to-success distribution (not just a mean), judge-score histogram, stopping-criterion histogram, cost per successful attack, and (if benign pairs were included) benign-pair over-refusal.
- ✅ Every successful attack in the report has an `attack_family_tag` and, where applicable, a cross-tag to mod-103 for injection-shaped findings. Un-tagged findings are rejected.
- ✅ `jeh-<target>-pair-tap-trajectories-manifest.yaml` lists every trajectory with ID, sha256, storage URI, matrix cell, severity, and access group. **No trajectory text and no judge rationale text appears in any committed file.**
- ✅ `jeh-<target>-pair-tap-runbook.md` covers attacker choice, judge choice, cost projection, rate-limit envelope, failure-mode audit, PAIR-vs-TAP comparison, and threats to validity.
- ✅ `jeh-<target>-pair-tap-ci.md` covers PR / nightly / release-candidate cadence, release-gate policy, trajectory retention, and matrix-cell coverage.
- ✅ ASR figures scored under a chapter-07 StrongREJECT-style judge. A refusal-word regex judge is disallowed for headline numbers.
- ✅ Every unverified factual claim (PAIR / TAP query counts from the papers, JailbreakBench behaviour count, provider RPM ceilings) is marked `<!-- needs-research: ... -->`.
- ✅ At least one successful attack carries a mod-112 severity annotation and a note on the disclosure-routing decision.
- ✅ Handoff notes at the end of the runbook naming the mod-108 workstream the failures feed and the mod-111 interface this report satisfies.

## Stretch goals

- **Sweep the TAP triple** — $(B, W, D)$ across at least three configurations — and report the Pareto curve of ASR vs. dollars. Chapter 03's default triple is not universal; the sweep is what makes it defensible on your target.
- **Add a PAIR-inside-Crescendo composition.** For each Crescendo plan (exercise 03), run PAIR to find the best framing for one turn. Report the composed ASR against the naive Crescendo baseline. Chapter 04's composition matrix expects this pattern.
- **Author a judge cascade.** A cheap first-pass LLM classifier, an expensive rubric judge on the ambiguous rows. Report the cost savings vs. rubric-only, and the agreement figure with rubric-only. Chapter 07's cost-adaptive judging pattern.
- **Compare PAIR vs. garak's jailbreak probes.** Chapter 09 codifies the boundary to the `ai-risk-engineer` prerequisite. Run the equivalent garak probe set against the same target and objective set; report where PAIR/TAP find things garak misses (and vice versa). The delta is what justifies this module on top of the prerequisite.
- **Author a mod-111 industrialisation ticket** — a short spec that describes how a scaled harness would run this loop across a fleet of targets, with concurrency, retry pools, provider-key rotation, cost pooling, and a sharded trajectory store. Chapter 03 names mod-111 as the industrialiser; the ticket is the concrete handoff.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the trajectories, or the judge rationales — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference run. Working payloads live in your org's payload store per chapter 01; see the harmful-payload discipline before starting.
