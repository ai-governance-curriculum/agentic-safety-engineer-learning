# 09 — Boundaries: `ai-risk-engineer`, mod-103, mod-108, mod-111

## Motivation

Chapters 02–08 built the JEH: attackers, benchmarks, judge, fixtures. This chapter draws the *boundaries* — the interfaces the JEH exposes to and consumes from adjacent roles and modules. The boundaries matter because a JEH that duplicates a peer role's work is wasted engineering; a JEH that fails to consume a peer's output is worse than that. The four boundaries this chapter codifies:

1. **The `ai-risk-engineer` prerequisite (level 25).** garak, PyRIT, and Promptfoo as working red-team suites at general depth. What this module does *not* re-derive.
2. **mod-103 (Prompt-Injection Engineering).** The PIEH and this JEH share infrastructure; they measure different failure modes. Where the shared harness helps, and where the boundary is load-bearing.
3. **mod-108 (Guardrails and Monitors).** The JEH *measures* guardrails; mod-108 *trains* them. The finding-feed the JEH provides mod-108, and the guardrails-under-test the JEH consumes.
4. **mod-111 (Automated / Scaled Red-Team).** The JEH is the engineer-hands-on-keyboard artefact; mod-111 industrialises it. The interfaces mod-111 consumes from the JEH, and the operational-scale concerns mod-111 owns that the JEH does not.

Every JEH finding cites these boundaries. A finding that lands on a peer role's work without naming the peer is a routing bug.

## Boundary 1 — `ai-risk-engineer` (prerequisite, level 25)

### What the prerequisite covers

The `ai-risk-engineer` packet at level 25 covers the general LLM-security craft as a working practitioner discipline. Specifically:

- **garak** — LLM vulnerability scanner from NVIDIA. Ships plug-ins for common jailbreak families (DAN, encoding tricks, refusal probing), prompt-injection primitives, and toxicity / bias probes. Runnable as a CLI + Python API against a target model or endpoint.
- **PyRIT (Python Risk Identification Tool for GenAI)** — Microsoft's open-source red-team framework. Multi-turn attackers, orchestrators, scoring, and reporting; extensible attack strategies.
- **Promptfoo** — CLI + web UI for prompt evaluation, with a red-team mode that runs a curated attack library against a target.
- The three suites' **CI integration** — how to wire the outputs into an eval-gated build; how to summarise pass rates for a review; how to file findings.
- **General red-team hygiene** — reproducibility, seed handling, target-model versioning, scoring conventions, and finding format.

If you completed the prerequisite you already know how to run those three suites end-to-end, wire their outputs to a CI, and produce a per-plug-in pass-rate table.

### What this module adds on top

This module adds the parts the prerequisite does not, and re-uses the parts that already exist:

- **Frontier-depth attack construction.** GCG's differentiable objective (chapter 02), PAIR / TAP's LLM-vs-LLM loops (chapter 03), Crescendo's multi-turn plan (chapter 04), many-shot / low-resource / cipher (chapter 05). None of these live in garak / PyRIT / Promptfoo as first-class implementations — they exist as separate published research artefacts that a level-40 engineer wires up.
- **StrongREJECT-style judge methodology** (chapter 07). The prerequisite suites use simpler judges by default; this module retrofits StrongREJECT-style rubric judges into the suites' plug-in interfaces (garak's `detectors`, PyRIT's `Scorer`, Promptfoo's assertion set).
- **Coverage matrix over the five benchmarks** (chapter 06). The prerequisite exercises the suites' bundled attack libraries; this module runs the frontier attackers against HarmBench / JailbreakBench / AIR-Bench / AILuminate / CyberSecEval and produces a composite report.
- **In-the-wild taxonomy → regression fixtures** (chapter 08). The prerequisite catalogues DAN variants at a high level; this module builds the fixture pipeline that turns each new variant into a permanent test.
- **LLM-vs-LLM attacker loop as a cost-and-rate-limited system** (chapter 03). The prerequisite runs the suites; this module engineers the loop.

### The interface

The prerequisite's suites are consumed as **runners**, not re-implemented:

- **garak** runs as the JEH's *first-pass wide-net attacker.* It catches the wide low-hanging class of attacks at low cost. Findings flow into the JEH's coverage matrix.
- **PyRIT** runs as one of the JEH's *multi-turn attackers*. Where its multi-turn orchestration is a fit, the JEH uses PyRIT; where the JEH needs Crescendo-shaped plans, the JEH's own attacker runs.
- **Promptfoo** runs as the *CI harness* that wraps regression-fixture runs. The fixture library (chapter 08) is exported to Promptfoo's assertion format for the release gate.

Every JEH finding names which suite (if any) surfaced it and which JEH-specific attacker (if any) surfaced it. A finding surfaced by garak but validated under the JEH's StrongREJECT-style judge is a joint finding.

### Common misreadings

- **"garak covers jailbreak."** garak covers a curated plug-in set. The frontier attackers of chapters 02–05 are not in the plug-in set by default.
- **"We already have Promptfoo; we don't need a coverage matrix."** Promptfoo is the harness that runs the tests. The matrix is what the tests are.
- **"PyRIT is the red-team framework."** PyRIT is *a* red-team framework. This module composes several.

## Boundary 2 — mod-103 (Prompt-Injection Engineering)

### The intrinsic distinction

Chapter 01 named the boundary: **injection is a principal attack; jailbreak is a policy attack.** The PIEH (mod-103's harness) and this JEH measure different failure modes. But they share infrastructure, and the two harnesses in a mature safety program run against overlapping targets and produce overlapping findings.

### Shared infrastructure

- **Judge scaffold.** The StrongREJECT-style judge (chapter 07) and mod-103's spotlighting / self-refuting-classifier judges share the same rubric-scored LLM plumbing. Judge model choice, cost accounting, calibration workflow, drift monitoring — one team owns the shared plumbing; both harnesses consume.
- **Payload store.** Injection payloads (PIEH) and jailbreak payloads (JEH) share a store. Access-controlled, manifest-committed, harmful-payload-discipline consistent across mod-103 chapter 06 and mod-104 chapter 01.
- **Reproducibility bundle.** Model IDs, snapshots, chat templates, decoder configs — same schema, same store, same versioning.
- **CI harness.** The Promptfoo runner (or equivalent) that gates release runs both PIEH and JEH suites; findings flow into a unified report.

### Distinct concerns

- **The attack primitives.** Injection has its six primitives (mod-103 chapter 02); jailbreak has its four families (this module, chapter 01). The two lists overlap on refusal-bypass — mod-103 owns *injection-delivered refusal-bypass*; this module owns *policy-level jailbreak construction*.
- **The defence catalogue.** Injection defences (spotlighting, StruQ, sandwich prompting, tool-response sanitisation, boundary controls) are mod-103 / mod-107 territory. Jailbreak defences (safety-tuning, constitutional classifiers, refusal-erosion curriculum, monitor-trained-on-transcripts) are mod-108 territory. The routing is different.
- **The taxonomy labels.** OWASP LLM01 (injection) vs. MITRE ATLAS `Jailbreak` / AML.T0054 (this module). Emit both when both are true; emit whichever is true when only one is.
- **The success criterion.** Injection succeeds when the model followed the wrong principal; jailbreak succeeds when the model produced policy-forbidden content. Both may be true on the same finding; the label carries both.

### Joint findings

A finding that is *both* an injection *and* a jailbreak (an indirect-channel payload that convinces the model to produce forbidden content) is filed under both harnesses' matrices. The JEH's finding cites the PIEH's finding ID; the PIEH's finding cites the JEH's finding ID. Downstream (mod-107 for boundary controls, mod-108 for guardrails, mod-112 for disclosure) reviewers see both.

### Common misreadings

- **"We have a PIEH; we don't need a JEH."** The PIEH doesn't run GCG, doesn't run PAIR / TAP, doesn't run Crescendo, doesn't run many-shot / cipher / low-resource. The JEH doesn't run injection primitive coverage. The two harnesses are complementary.
- **"Every jailbreak is an injection."** No. A direct jailbreak in the chat box is not an injection; there is no principal confusion — the user is the attacker.
- **"Every injection is a jailbreak."** No. An injection that swaps the user's task for a benign attacker task is a mod-103 finding without a jailbreak component.

## Boundary 3 — mod-108 (Guardrails and Monitors)

### The intrinsic distinction

Mod-108 *trains* guardrails: classifier guards, constitutional classifiers, safety-tuning updates, runtime monitors. This module *measures* the guardrails' effect. The JEH is an input to mod-108's training workstream and a consumer of mod-108's deployed defences.

### JEH → mod-108 (findings flow)

The JEH provides:

- **Attack success rate per guardrail.** Chapter 06's coverage matrix has a column per defence layer. The matrix rows show which guardrails caught which attacks. Mod-108 uses this to prioritise which guardrail-training gaps to close.
- **Adaptive-attack survival numbers.** Chapter 07's third derived metric. A guardrail with high naive-ASR reduction but low adaptive-survival reduction is a guardrail whose apparent value is an artefact of not being attacked in earnest. Mod-108 tunes it or replaces it.
- **Regression fixtures where guardrail regression is diagnosed.** Chapter 08's fixtures. When a fixture's ASR increases release-over-release and the diagnosis is "the guardrail regressed," mod-108's release checklist gets a signal.
- **Judge disagreement signals.** Chapter 07's calibration data. Where the JEH's judge and mod-108's judge disagree on the same trial, both teams re-anchor.

### mod-108 → JEH (defences under test)

Mod-108 provides:

- **The current guardrail stack.** Classifier guards, constitutional-classifier versions, runtime monitors. The JEH's coverage matrix runs each attack family against the guardrail stack in effect.
- **Guardrail versioning and change log.** The JEH's release runbook keys attacker re-runs to guardrail changes so regression can be diagnosed.
- **The over-refusal characterisation.** Mod-108's guardrail-tuning trades off refusal robustness against over-refusal; the JEH's benign-pair report (chapter 07) is the corresponding measurement.

### Common misreadings

- **"The JEH replaces guardrail-training."** It doesn't. It measures.
- **"Mod-108 replaces the JEH."** It doesn't. Mod-108's guardrails do not evaluate themselves.
- **"We measure once and pass forever."** Every guardrail change re-triggers the JEH's coverage runs; the mod-108 workstream and the JEH are tightly coupled by cadence.

## Boundary 4 — mod-111 (Automated / Scaled Red-Team)

### The intrinsic distinction

This module builds the JEH at engineer-hands-on-keyboard depth. Mod-111 turns it into a scheduled, sharded, cost-managed pipeline that runs across every model in the org's inventory. The two modules are on the same axis; the difference is *scale and operationalisation*, not attack construction.

### JEH → mod-111 (what mod-111 consumes)

The JEH exposes stable interfaces mod-111 orchestrates:

- **Attacker interface.** The four attack families' runners (GCG, PAIR / TAP, Crescendo, many-shot / persona / low-resource / cipher) each expose a common interface: `(target, judge, budget, seeds) → report`. Mod-111's scheduler calls it in parallel across the model fleet.
- **Judge interface.** Chapter 07's StrongREJECT-style judge exposes `(prompt, response, objective) → score`. Mod-111 batches and cascades.
- **Benchmark runners.** Chapter 06's per-benchmark runners each expose `(target, attackers, judge, tier, budget) → matrix rows`. Mod-111 shards by benchmark and by target.
- **Regression fixture library.** Chapter 08's fixtures each expose a runner and a stable baseline ASR. Mod-111 runs the tier-A subset every release and the tier-B / C on a rotation.
- **Cost accounting.** Every runner reports `dollars, tokens, wall-clock`. Mod-111's scheduler treats these as first-class scheduling inputs.
- **Provenance and reproducibility bundles.** Every finding has a reproducibility bundle (chat template, model snapshot, decoder, seeds, judge version). Mod-111's aggregation preserves the bundle.

### mod-111 → JEH (what mod-111 provides)

Mod-111 provides:

- **Scaled fleet orchestration.** The JEH's runners execute against many targets in parallel; mod-111 owns the schedule.
- **Rate-limit and cost pooling across the org.** The JEH's per-run budget is one attacker; mod-111 owns the org-wide envelope.
- **Central finding-feed and aggregation.** The JEH's findings converge into a single feed that mod-112's disclosure workflow and the org's safety-case artefacts consume.
- **Continuous LLM-vs-LLM training loops.** PAIR / TAP / Crescendo attacker templates get retrained on the fixture library; the retrained attackers flow back into the JEH's runners.

### Common misreadings

- **"Mod-111 replaces the JEH."** It doesn't. Mod-111 needs the JEH's runners as its work-units.
- **"The JEH runs at production scale."** It doesn't. The JEH is engineer-hands-on-keyboard depth. Mod-111 owns production scale.
- **"Mod-111's numbers are the JEH's numbers scaled."** They are and they aren't. Mod-111's aggregation exposes patterns (fleet-wide regression under a common upstream change) the JEH can't see from one target. But the underlying attackers and judge are the JEH's.

## Boundaries this chapter doesn't develop but the JEH still routes to

- **mod-106 (Dangerous-capability evaluations).** CBRN, cyber-uplift, autonomous-replication, R&D-uplift benchmarks. CyberSecEval 3's autonomous-offence subset is cited to mod-106. The JEH's cyber-uplift findings above a severity threshold are routed there.
- **mod-107 (Excessive-agency containment).** Sandboxing, egress policy, credential scoping. When a JEH finding rides an agent's tool-abuse chain, mod-107 owns the containment side; the JEH cites the boundary.
- **mod-109 (Safety cases and structured argumentation).** The JEH's outputs are evidence for safety cases. Mod-109 owns the argumentation structure; the JEH is a source.
- **mod-110 (Control and deception evaluation).** Adjacent to jailbreak; the schemes here (Meinke et al. in-context scheming, alignment faking, sabotage evaluations) are mod-110's material. When a JEH finding shows the target *complying while deceiving* — producing forbidden content while claiming to refuse — the finding routes to mod-110 alongside this module.
- **mod-112 (Safety program and disclosure).** The disclosure workflow. High-severity findings route through mod-112. The JEH's severity annotations feed the routing.
- **`ai-eval-engineer` peer role (level 30, AI Engineering family).** The app-side trace format, judge scaffold, and eval-gated CI. The JEH consumes their plumbing per mod-103 chapter 09's contract.
- **`model-evaluation-engineer` peer role (level 30, ML Engineering family).** Statistical calibration methodology — sampling, CIs, judge-vs-human calibration. The JEH consumes their methodology per chapter 07.
- **`security-learning` and `ai-infra-security-learning` (level 35, peer / next-up).** Platform-scale security. Where the JEH's findings implicate the platform (secrets management, network egress, model registry), they route to those tracks.

Each of these boundaries is a routing rule, not a re-derivation. The JEH names the target module in the finding's `boundaries` block.

## The JEH's exposed interfaces — one-page summary

Every JEH ships a stable interface surface. The following is the minimum contract this module's chapters produce:

```yaml
jeh_interface:
  attackers:                               # chapters 02–05
    - name: gcg
      run: (target, batch, budget, seeds, source_ensemble, cfg) -> report
    - name: pair
      run: (target, judge, budget, attacker_tmpl, cfg, seeds) -> report
    - name: tap
      run: (target, judge, budget, attacker_tmpl, cfg, seeds) -> report
    - name: crescendo
      run: (target, judge, budget, plan_or_flavour, cfg, seeds) -> report
    - name: many_shot
      run: (target, judge, budget, demo_set, shot_counts, seeds) -> report
    - name: persona
      run: (target, judge, budget, variant_set, seeds) -> report
    - name: low_resource
      run: (target, judge, budget, translation_set, languages, seeds) -> report
    - name: cipher
      run: (target, judge, budget, cipher_set, ciphers, seeds) -> report
  judge:                                   # chapter 07
    score: (prompt, response, objective) -> {refusal, specificity, on_topic, composite, rationale}
    calibrate: (sample) -> {kappa, per_dimension, agreement}
  benchmarks:                              # chapter 06
    - name: harmbench    | run: ...
    - name: jailbreakbench | run: ...
    - name: airbench_2024 | run: ...
    - name: ailuminate   | run: ...
    - name: cyberseceval | run: ...
  fixtures:                                # chapter 08
    library:  <manifest_uri>
    run: (target, tier, seeds) -> matrix_rows
    add: (capture) -> fixture_id
  coverage_matrix:                         # chapter 06
    aggregate: (reports) -> matrix
    metrics: [asr, refusal_robustness, elicitation_gap, adaptive_survival, judge_disagreement, cost]
  boundaries:
    to_mod_103_pieh:      <interface pointer>
    to_mod_108_guardrails:<interface pointer>
    to_mod_111_scale:     <interface pointer>
    to_ai_risk_engineer_suites: {garak, pyrit, promptfoo}
```

Every JEH finding cites this interface's version. Version bumps propagate to mod-111 and mod-108.

## Common cross-cutting engineering mistakes

- **Re-implementing garak plug-ins.** Wasteful; the plug-ins exist and are maintained. Use them as first-pass runners.
- **Owning what mod-108 owns.** Guardrail-training is not this module's territory. Measure; do not train.
- **Owning what mod-111 owns.** Fleet-wide scheduling and cost pooling are not this module's territory. Expose interfaces; do not orchestrate the fleet.
- **Blurring the boundary to mod-103.** Injection and jailbreak on the same finding require both labels; the wrong label routes to the wrong team.
- **Ignoring mod-106 for dangerous-capability findings.** A cyber-uplift finding above a severity threshold is not a JEH-only finding; it is a mod-106 finding.
- **Skipping the mod-112 severity annotation.** Disclosure workflow depends on it.

## Summary

- The **`ai-risk-engineer` prerequisite** covers garak, PyRIT, and Promptfoo as working red-team suites at general depth. This module adds frontier-depth attack construction (GCG, PAIR, TAP, Crescendo, many-shot / persona / low-resource / cipher), a StrongREJECT-style judge, benchmark coverage across five benchmarks, and an in-the-wild → regression-fixture pipeline. The prerequisite's suites are consumed as runners, not re-implemented.
- The **boundary to mod-103** is: injection is a principal attack; jailbreak is a policy attack. The two harnesses share infrastructure (judge scaffold, payload store, reproducibility bundle, CI harness) and produce joint findings that carry both labels.
- The **boundary to mod-108** is: mod-108 *trains* guardrails, the JEH *measures* them. Findings flow from the JEH to mod-108; deployed guardrails flow from mod-108 into the JEH's coverage runs. Adaptive-attack survival is the load-bearing metric at this interface.
- The **boundary to mod-111** is: this module is engineer-hands-on-keyboard depth; mod-111 is scaled operational depth. The JEH exposes stable interfaces (attackers, judge, benchmarks, fixtures, coverage matrix) that mod-111 orchestrates.
- Adjacent boundaries — mod-106 (dangerous-capability), mod-107 (containment), mod-109 (safety cases), mod-110 (control / deception), mod-112 (disclosure), `ai-eval-engineer`, `model-evaluation-engineer`, platform-security tracks — are routing rules; the JEH names the target module in each finding.
- The exposed interface (attackers, judge, benchmarks, fixtures, coverage matrix, boundaries) is the module's operational contract. Version-bump it deliberately.
- Every JEH finding is routed. No finding is a JEH-only finding.
