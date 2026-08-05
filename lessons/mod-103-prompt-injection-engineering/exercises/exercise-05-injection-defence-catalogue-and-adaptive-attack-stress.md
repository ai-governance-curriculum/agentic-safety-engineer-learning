# exercise-05 — Injection Defence Catalogue and Adaptive-Attack Stress

**Estimated effort:** 3 hours
**Prerequisite chapters:** 07, 08 (helpful: 05, 06, 09).
**Prerequisite exercises:** 04 (the PIEH the defence catalogue is measured against). Exercises 01–03 populate the coverage matrix the stress runs over.

## Objective

Author the **defence catalogue** for your target agent — the five layers from chapter 07 plus any operator-specific additions — and stress-test each layer, and the composed stack, against adaptive attackers per chapter 08. The deliverable is a per-layer coverage claim, a composed-stack coverage claim, and a table of **static-ASR vs. adaptive-ASR** deltas that routes to the specific defence-owner teams and the downstream mitigation modules (mod-107, mod-108, mod-111, mod-112).

## Problem statement

Using the PIEH from exercise 04, run the coverage matrix against your target's current defence stack twice:

1. **Static pass** — the replay bundle as-is (chapter 06).
2. **Adaptive pass** — an attacker in the loop, given a budget, iterating against each layer and against the composed stack (chapter 08).

Then author the defence catalogue: one section per defence layer the operator has deployed (or claims to have deployed), with what the layer *does* catch, what it *misses*, and the adaptive-attack gap. Where a layer is not yet deployed, catalogue it as a *planned* layer with the ASR reduction that would justify the deployment cost.

You may run against a real target (best), against a stubbed target implementing a plausible baseline stack (acceptable), or against a well-defined thought experiment where the numbers are labelled as *estimated* rather than *measured*. In all three cases, the artefact structure and routing discipline are the same.

## Requirements

Produce four artefacts.

### Artefact A — `pieh-<target>-defence-catalogue.md`

A Markdown catalogue, ~2000–3500 words. One section per layer the operator has (or plans to have). At minimum, cover the five layers from chapter 07:

1. **Spotlighting** (delimiting / datamarking / encoding — Hines et al.).
2. **StruQ** (structured queries — Chen et al.).
3. **Sandwich prompting**.
4. **Self-refuting classifiers** (second-pass classifier / known-answer probe / instruction-hierarchy verifier).
5. **Tool-response sanitisation and boundary controls** (input-side sanitisation, provenance labels, blast-radius caps, argument re-validation, egress controls, cross-principal isolation).

Add any operator-specific layers your target actually ships (vendor guardrails, in-house classifier, application-side moderation model, HITL approval gate, etc.).

Each section must contain:

- **Layer name and reference.** The chapter-07 sub-section, the primary source (paper, vendor doc, internal design doc), the version of the layer running in the target today.
- **Threat-model coverage claim.** Which coverage-matrix cells this layer is *supposed* to catch, stated in the chapter-06 `(primitive, channel, obfuscation)` triple format.
- **Static-pass numbers.** Per cell in the coverage claim: `static_ASR_with_layer_on`, `static_ASR_with_layer_off`, `layer_delta` = off − on. Cite trial counts, seed range, judge ID/version (from exercise 04's `pieh-<target>-judges.yaml`).
- **Adaptive-pass numbers.** Per cell: `adaptive_ASR_with_layer_on`, `adaptive_ASR_with_layer_off`, `adaptive_delta`, and the **defence gap** = `adaptive − static`. Name the attacker budget (iterations, wall-clock, human vs. scripted vs. LLM-in-the-loop) per chapter 08's contract.
- **Adaptive playbook actually run.** Enumerate which of chapter 08's per-layer adaptive strategies you tried. The catalogue is not credible if the section says "we adaptively attacked spotlighting" without naming which of the four spotlighting-specific strategies (delimiter-rule attack, cross-turn erosion, delimiter straddle, encoded pass-through) fired.
- **Root-cause note per successful adaptive attack.** Which sub-strategy succeeded, on which cell, at what iteration count. Feeds the defence owner's remediation queue.
- **What this layer catches — and what it misses.** Chapter-07-style prose paragraphs, but *calibrated to the numbers you just measured* rather than restated from the chapter.
- **Owner and remediation queue.** The team that owns the layer, and the top-three findings (adaptive-ASR gap ranked) queued for their next release.
- **Downstream-module routing.** For each gap large enough to matter, name whether it routes to mod-107 (boundary controls), mod-108 (classifiers), mod-111 (attacker-loop industrialisation), mod-112 (disclosure), or an operator-internal team. Chapter 07's boundary discussion is your map.

### Artefact B — `pieh-<target>-composed-stress.md`

The **composed-stack** stress-test — chapter 08's "attacker-picks-hard" number. ~700–1200 words. Sections:

- **Composed configuration.** Which layers are on, in what order, at what parameters. This is the released defence stack.
- **Attacker-picks-easy number.** For each Tier A cell, the ASR when the adaptive attacker chose the payload that beats the *weakest single* layer without considering composition. Chapter 08's baseline.
- **Attacker-picks-hard number.** For each Tier A cell, the ASR when the adaptive attacker knew every layer, iterated against the composition, and reported the maximum ASR reached within budget. The number that matters.
- **Composition delta table.** Per cell: `attacker_picks_easy_ASR`, `attacker_picks_hard_ASR`, `composition_delta` = hard − easy. Cells where the delta is large signal composition is *not* helping — layers share blind spots.
- **Elicitation-gap widened CI.** Chapter 08's honest-number discipline: report the internal-red-team adaptive ASR *and* the widened confidence interval acknowledging a real external attacker will likely exceed the internal number. This is the number a mod-112 disclosure will cite.
- **Release-gate implications.** Which Tier A cells now fail the release-gate policy in exercise 04's `pieh-<target>-ci.md`? Which cells were passing static but fail adaptive? Which downstream modules must consume the failure?

### Artefact C — `pieh-<target>-defence-numbers.yaml`

The machine-readable rollup of everything Artefacts A and B measured. One entry per (layer, cell) row plus a `composed` block per cell:

```yaml
target: <target-agent-id>
defence_stack_version: <version>
runs:
  - layer: spotlighting
    layer_version: <ref>
    cells:
      - cell: {primitive: task_override, channel: indirect.retrieval.webpage, obfuscation: none}
        static:
          asr_on:  <float>
          asr_off: <float>
          delta:   <float>
          n_trials: <int>
          judge_id: <ref from exercise-04 judges.yaml>
        adaptive:
          asr_on:  <float>
          asr_off: <float>
          delta:   <float>
          gap:     <float>       # adaptive_on − static_on
          budget:  {iterations: 100, human_hours: 4, mode: scripted}
          strategies_tried:      [delimiter_rule_attack, cross_turn_erosion]
          strategies_that_hit:   [delimiter_rule_attack]
        owner: model-provider
        routes_to: [mod-108]
      - ...
composed:
  cells:
    - cell: {primitive: task_override, channel: indirect.retrieval.webpage, obfuscation: none}
      attacker_picks_easy_asr: <float>
      attacker_picks_hard_asr: <float>
      composition_delta:       <float>
      elicitation_widened_ci:  [<lo>, <hi>]
      release_gate:            block | pass | conditional
      routes_to:               [mod-107, mod-112]
```

Every cell in Tier A from exercise 04's coverage matrix has an entry. Tier B cells are covered on a rotating subset — name the rotation in a `notes:` block.

### Artefact D — `pieh-<target>-mod-boundaries.md`

A one-page routing note, ~500 words, that closes the loop with the downstream mitigation modules. For each of mod-107, mod-108, mod-111, mod-112, list:

- Which findings from Artefacts A–C route to that module.
- The specific defence-owner team on the operator side.
- The proposed remediation (in chapter-07 language for mod-108, in chapter-08 language for mod-111, in mod-107's boundary-controls language for mod-107, in mod-112's disclosure-severity language for mod-112).
- The re-measurement cadence — when the PIEH re-runs after the fix ships, and what ASR movement counts as "closed."

## Starter guidance

- **Budget the adaptive attacker before you start.** Chapter 08 suggests 100 iterations per Tier A cell for a scripted loop, 20 for Tier B, 4 hours per Tier A cell for a two-engineer human red-team. You will not have time to run the full budget; state the budget you ran and mark the rest as `<!-- needs-research: extend adaptive budget on next PIEH run -->`.
- **Do not let the adaptive attacker share weights with the defender.** Chapter 08's warning — a sibling-family attacker LLM shares blind spots with the defender. Use a different family, or use a scripted mutator seeded from chapter 05's obfuscation vectors, or use a human red-team pair.
- **Measure with the layer both on and off, per layer.** The `layer_delta` is the actual coverage claim. A layer that reduces ASR by 5 points and takes 2000 tokens per turn is different from one that reduces ASR by 40 points and takes 20 tokens.
- **The composed number is the release-gate number.** Sum of per-layer deltas overstates coverage; composition-under-adaptive-attack is what ships. Give this its own section (Artefact B) and its own row per cell (Artefact C's `composed:` block).
- **Route every gap.** A finding with a large adaptive gap that does not name an owner and a downstream module is unactionable. Every row in Artefact A ends with an owner; every row in Artefact D names a module.
- **Layer 5 — boundary controls — is where the invariant defence lives.** Chapter 07 makes this the highest-leverage layer for high-consequence tools. Do not skip it because it feels "less model-side." The findings on layer 5 tend to be the ones that route to mod-107 and change the operator's release story most.
- **The mod-112 disclosure feed uses the *adaptive* number.** Chapter 08's honesty discipline: a system-card, safety case, or AISI submission that cites the static ASR is misleading. If a finding is above the disclosure threshold, use the adaptive number and the widened CI.
- **Do not paste winning adaptive payloads into the artefacts.** Chapter 08 is explicit: the winning adaptive payloads are the highest-leverage ones and thus the most important to keep in the external replay bundle. Reference them by ID (from exercise 04's `pieh-<target>-manifest.yaml`) and let the external store hold the string.

## Acceptance criteria

- ✅ `pieh-<target>-defence-catalogue.md` covers at least the five chapter-07 layers, one section per layer.
- ✅ Every layer section has a threat-model coverage claim, static-pass numbers, adaptive-pass numbers, adaptive playbook actually run (naming which chapter-08 strategies fired), root-cause notes per successful adaptive attack, owner, and downstream-module routing.
- ✅ `pieh-<target>-composed-stress.md` reports both attacker-picks-easy and attacker-picks-hard numbers with a per-cell composition delta and an elicitation-widened CI.
- ✅ `pieh-<target>-defence-numbers.yaml` covers every Tier A cell with per-layer entries plus a `composed:` block; Tier B cells are covered on a stated rotation.
- ✅ `pieh-<target>-mod-boundaries.md` routes at least one finding to each of mod-107, mod-108, mod-111, mod-112 (or explicitly notes zero findings for that module with reasoning).
- ✅ Attacker budget is stated per cell — iterations / wall-clock / mode. No unnumbered "we tried some things."
- ✅ Static and adaptive ASR are reported with CIs from the reproducibility bundle's seed range, not single-seed numbers.
- ✅ No working payloads in any artefact. Winning adaptive payloads live in the external replay bundle; the artefacts reference them by ID.
- ✅ Any unverified factual claim (specific chapter-08 strategy attribution, specific ATLAS ID, specific vendor guardrail behaviour) marked `<!-- needs-research: ... -->`.
- ✅ Handoff note at the end names the exercise-04 harness fields that need to be re-measured after the mod-107 / mod-108 fixes ship.

## Stretch goals

- **Cross-model portability.** Rerun the composed adaptive stress on a second model family (safety-tuned variant, comparison provider). Report which layers survive the swap and which do not. This is the most common regression source after a model update.
- **Regression-fixture harvest.** Every winning adaptive payload becomes a named regression fixture in the PIEH (chapter 06). Author the fixture file with `fixture_id`, `matrix_cell`, `payload_id`, `first_seen_release`, `owner`. The `ai-eval-engineer` peer's CI (chapter 09) consumes it.
- **LLM-in-the-loop attacker.** Replace the scripted mutator in exercise 04's adaptive loop with an LLM-in-the-loop attacker (chapter 08's third budget option). Compare the ASR curve — where does the LLM attacker plateau vs. the scripted mutator? This is mod-111's launch material.
- **Boundary-controls layer engineering brief.** For the layer-5 findings, author a one-page brief for the mod-107 owner: which egress control / sanitiser / argument-validator / cross-principal-scope change closes which cell, at what implementation cost. This is the pre-form of the mod-107 handoff artefact.
- **Disclosure-severity worksheet.** For the composed-stress findings above the mod-112 disclosure threshold, author the disclosure worksheet: finding, severity, adaptive-ASR + widened CI, defence stack version, recommended notification (internal / customer / regulator / public). This is the pre-form of the mod-112 disclosure workflow.
- **Elicitation-gap postmortem.** After the adaptive run, interview the human red-team pair (if one ran): which strategies they tried that the scripted mutator did not, and vice versa. The union is the true elicitation ceiling for this cycle; the gap between the two is a real datum for future budgeting.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference catalogue against a stubbed target. Working payloads — especially the winning adaptive ones — live outside both repos per chapter 06's harmful-payload discipline and chapter 08's specific note that winning adaptive payloads are the ones you most want held in the external store.
