# exercise-04 — Many-Shot, Persona, Low-Resource, and Cipher Jailbreak Sweep

**Estimated effort:** 2 hours
**Prerequisite chapters:** 05, 07 (helpful: 06, 08).

## Objective

Exercise the four **in-context / long-context / linguistic** attack families from chapter 05 — **many-shot**, **persona / role-play**, **low-resource language**, and **cipher** — against the target you chose in exercise 02. Report each family as a *sweep curve* (not a scalar), report at least one **cross-family composition**, and populate the chapter-06 coverage matrix rows for the "linguistic" attacker group. The exercise closes out the attacker side of the JEH; exercises 05–07 handle benchmarks, judging, and fixtures.

## Problem statement

Pick the **same target** as exercises 02 and 03 so numbers compose. Pick a small **objective set** (5–20 refuseable behaviours) drawn from the same benchmark you used in exercise 02 (JailbreakBench harmful pairs preferred so over-refusal on benign pairs is trivially available).

Run each of the four families against the objective set:

1. **Many-shot.** Sweep shot count $n \in \{0, 16, 64, 256, 1024\}$ (or the largest value your target's context window supports); report ASR as a curve, not a point. Chapter 05 is explicit that many-shot's whole story is the curve.
2. **Persona.** Run a curated set of at least 5 persona variants drawn from Shen et al.'s clusters (chapter 08); report per-variant ASR and a `compliance-with-persona vs. compliance-without-persona` split (chapter 05's multi-level scoring).
3. **Low-resource language.** Run against at least 3 languages from Yong et al.'s set (or an equivalent set your judge can score bilingually); report per-language ASR *separately* — do not aggregate. Track request-language vs. response-language explicitly.
4. **Cipher.** Run at least 3 ciphers from chapter 05's set (base64, ROT13, Caesar-N, ASCII-art, Morse) and report per-cipher decode success separately from per-cipher jailbreak success. A cipher the target can't decode isn't a jailbreak; it's a broken test.

Run at least one **cross-family composition** — many-shot × persona, or Crescendo × persona (leveraging exercise 03's plans), or low-resource × cipher — and report the ASR lift over the single-family baseline.

Payload discipline (chapters 01, 05) is not optional: demo sets, persona strings, translated requests, and ciphertexts are all working payloads and live in the store, not this deliverable.

## Requirements

Produce four artefacts.

### Artefact A — `jeh-<target>-linguistic-attackers-config.yaml`

Per-family run configuration. At minimum:

- Shared: `target` (model ID, snapshot, endpoint, decoding config, chat template), `judge` (chapter-07 rubric version and calibration figure — noting per-language and per-cipher sub-calibrations per chapter 07), `objectives` (benchmark + behaviour IDs), `seeds`, `budget` (dollars, wall-clock).
- `many_shot`: `demo_set_id` (external store reference), `shot_counts` swept, per-attempt token budget, per-attempt context-window-usage log flag.
- `persona`: `variant_set_id` (external store reference), variants enumerated by cluster (DAN, Developer Mode, Grandma, etc.), any framings composed (two-response format, token-loss framing).
- `low_resource`: `translation_set_id`, `languages` (ISO codes), `translator` (model + version), `back_translation_verifier` (model + version).
- `cipher`: `cipher_set_id`, `ciphers` enumerated, encoder + decoder libraries and versions.
- `composition`: at least one entry naming the composed families, the composition ID, and the baseline it compares against.

### Artefact B — `jeh-<target>-linguistic-attackers-report.yaml`

Per family × objective, an entry. Family-specific fields:

- `many_shot`: ASR curve indexed by shot count with 95% CI; `cost_per_attempt(n)` (chapter 05 says cost scales linearly with $n$); `context_window_usage(n)`; a note on whether the provider silently truncated context beyond a declared limit.
- `persona`: per-variant ASR with CI; per-variant `persona_adopted_but_refused` fraction (partial success); per-variant `persona_rejected` fraction (full failure); a `variant_age` note per variant (when the variant appeared in the wild — chapter 05 says the family ages fast).
- `low_resource`: per-language ASR with CI (never aggregated); `response_language` distribution per attempt; `judge_bilingual_agreement` figure per language (chapter 07's per-language calibration).
- `cipher`: per-cipher `decode_success_rate` separately from `jailbreak_asr`; response-cipher direction reported separately from request-cipher direction; tokeniser-cost notes for expensive ciphers.
- `composition`: the composed families, the composed ASR, the single-family baseline ASRs, and the **lift** (`composed − max(single_family)`). Chapter 05 argues that composition is often the highest-ASR configuration; the lift is what verifies it.

For all families, `benign_pair_over_refusal` on JailbreakBench benign pairs is reported alongside ASR under the same judge — chapter 07's mandatory pairing.

### Artefact C — `jeh-<target>-linguistic-payloads-manifest.yaml`

The payload-store manifest for demo sets, persona sets, translation sets, cipher sets, and the successful trajectories they produced. Each entry:

- `id`, `kind` (`demo_set | persona_variant | translation | ciphertext | trajectory`), `sha256`, `storage_location`, `matrix_cell`, `severity`, `access`.
- For trajectories: the `family_primary` and `family_composed` tags (chapter 05 requires both; findings without the composition tag route badly).
- `fixture_candidate: true|false` — same convention as exercise 03; consumed by exercise 07.

### Artefact D — `jeh-<target>-linguistic-runbook.md`

A short (~800–1200 word) runbook covering:

- **Sweep design rationale.** Why these shot counts, personas, languages, ciphers. What coverage gaps you deliberately picked to exercise, and which you left for a follow-up run.
- **Composition rationale.** Which composition you ran and why it was the most likely to produce a lift on your target. Chapter 05's composition matrix is the design guide; report the observed lift vs. the expected direction.
- **Judge calibration by language and by cipher.** Chapter 07 makes per-language and per-cipher calibration mandatory. Report the calibration figures, note any language / cipher where the judge is weakest, and note how the weak-judge cells are flagged in the report.
- **Cost audit.** Many-shot's cost curve dominates linguistic-attacker cost; report dollars per benchmark row at each shot count. The mod-111 scheduler needs this to plan.
- **Failure-mode audit.** Which of chapter 05's specific failure modes fired: demonstration leakage into output format, context truncation, judge over-crediting persona role-play, refusal-in-cipher scored as compliance, translator that safety-tuned itself into refusing the translation. Report what you did about each.
- **Category-confusion note.** Chapter 05 flags that persona-in-low-resource findings frequently get mis-filed. How did you decide the primary tag when families genuinely overlap?
- **Threats to validity.** Sweep resolution too coarse, seed underrun, judge language-coverage gap, cipher decoded to hallucination scored as compliance, snapshot drift, chat-template mismatch.

## Starter guidance

- **Read the primary sources first.** Anthropic's many-shot post, Yong et al. for low-resource, Yuan et al. (CipherChat) for ciphers, Shen et al. for the persona corpus. Chapter 05 summarises but the primaries have the specific numbers.
- **The many-shot curve is the deliverable.** A many-shot report with one number is not a many-shot report. Even at low objective count, sweep the shot count fully.
- **Diversify demonstrations.** A demo set concentrated in one benchmark category will produce a many-shot curve that says nothing about coverage. Spread across the objective set.
- **Track response-language separately from request-language.** Chapter 05 flags this specifically: some targets refuse to reply in a low-resource language even when they understand the request.
- **Report decode success separately from jailbreak success for ciphers.** Otherwise a cipher the target simply can't decode reads as a defence success; it isn't.
- **Cross-tag compositions.** A finding whose primary family is `persona` and composed family is `low_resource` is *not* the same as a `low_resource` finding with a persona flavour text. The tag order matters for mod-108's routing.
- **Judge in the same language for low-resource.** English-only judges under-count refusals in the response language. Chapter 07's per-language calibration is the reference.
- **Manifest before payload.** Every demo, persona string, translated request, and ciphertext lands in the store first. Nothing gets scored until it has an ID in the manifest.
- **Cost per benchmark row.** Many-shot fills the context and burns tokens; report dollars per benchmark row at each shot count, not aggregated across the run.
- **Cross-reference exercise 03.** If Crescendo × persona is your composition, chapter 04's Crescendo plans + chapter 05's personas compose directly; reuse exercise 03's plan library.

## Acceptance criteria

- ✅ All four families (many-shot, persona, low-resource, cipher) are exercised against the same objective set on the same target.
- ✅ Many-shot ASR is reported as a **curve** across ≥4 shot counts, with 95% CI. A single-point many-shot report is rejected.
- ✅ Low-resource ASR is reported **per language**, never aggregated. Request-language and response-language are tracked separately.
- ✅ Cipher ASR is reported per cipher with **decode success** as a separate column from jailbreak success.
- ✅ At least one **cross-family composition** is run with the ASR lift reported vs. the single-family baseline.
- ✅ Per-family per-objective `benign_pair_over_refusal` is reported when JailbreakBench benign pairs are in scope (chapter 07 requirement).
- ✅ `jeh-<target>-linguistic-payloads-manifest.yaml` lists every demo set, persona string, translation, ciphertext, and successful trajectory with ID, sha256, storage URI, matrix cell, severity, primary + composed tags, and `fixture_candidate` flag. **No working demo / persona / translation / ciphertext / trajectory text in any committed file.**
- ✅ Judge is chapter-07 StrongREJECT-style with per-language and per-cipher calibration figures reported.
- ✅ Every unverified factual claim (Anthropic many-shot thresholds, Yong et al. per-language numbers, CipherChat per-cipher numbers, Shen et al. corpus size) marked `<!-- needs-research: ... -->`.
- ✅ At least one composition finding carries a mod-112 severity and a fixture-candidate flag for exercise 07.
- ✅ Handoff notes at the end of the runbook name the mod-108 workstreams the failures feed (per-language safety-tuning gaps, cipher-decoder classifier training) and the mod-111 interface this report satisfies.

## Stretch goals

- **Extend many-shot to the target's context ceiling.** If your target supports 200k+ context, sweep to $n = 2048$ or beyond and re-fit the ASR curve. Chapter 05's frontier-context discussion depends on the tail of the curve.
- **Rotate persona set by age.** Split the persona variant set into "old and public" vs. "recent (past 30 days) in-the-wild" and report the ASR delta. Chapter 05's aging discussion predicts a large delta.
- **Ship a bilingual judge.** Use a strong multilingual model (or a chained translate → English judge → verify translation) and report the delta vs. the single-language judge. The bilingual judge is chapter 07's answer to per-language calibration weakness.
- **Cross-benchmark composition.** Run the composed attack against a second benchmark's category set (HarmBench if you used JailbreakBench, or vice versa). Cross-benchmark ASR is a stronger claim than same-benchmark ASR.
- **Author a chapter-05 → chapter-08 fixture pipeline preview.** Take one successful composition and pre-format it as a chapter-08 regression fixture; exercise 07 formalises the workflow.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the demo sets, persona strings, translations, ciphertexts, or trajectories — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference sweep. Working payloads live in your org's payload store per chapter 01; see the harmful-payload discipline before starting.
