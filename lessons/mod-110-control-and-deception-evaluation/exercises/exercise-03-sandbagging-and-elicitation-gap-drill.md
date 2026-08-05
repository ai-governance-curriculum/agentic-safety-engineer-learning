# exercise-03: Sandbagging and Elicitation-Gap Drill

**Estimated effort:** 4 hours

## Objective

Measure a **sandbagging bound** on one specific capability held by one target-model version, by running the chapter-04 primitives — **scratchpad elicitation** (primitive 1, in-house) and either the **password-locked-capability** or the **fine-tune elicitation** primitive (primitive 2 or 3, `fine-tuning-engineer` peer-role-partnered) — and emit a chapter-04 **sandbagging report** in a shape a mod-106 DCER can cite as the third layer of its elicitation-gap accounting.

The exercise is where the chapter-04 discipline lands as *reproducible operator craft*. The load-bearing artefact is the sandbagging report *keyed to a specific DCER* — a report that names the capability, the elicitation-gap that mod-106 measures, and the additional bound this exercise supplies. If your operator does not currently have a mod-106 DCER for the capability you evaluate, author a stub DCER header that this exercise's report can cite; the interface is more important than the DCER's completeness.

## Prerequisites

- Complete exercises 01 and 02 (or have the harness scaffolding available). This exercise's scratchpad elicitation primitive re-uses exercise 01's probe / trace / judge scaffold.
- Read chapter 04 end-to-end. Skim chapter 09 §"Peer-role handoff map — `fine-tuning-engineer`" for the primitive-2 / primitive-3 handoff shape and §"`model-evaluation-engineer`" for the statistical-methodology deferral.
- Read [Greenblatt et al. — Stress-testing capability elicitation with password-locked models](https://arxiv.org/abs/2405.19550) for the primitive-2 methodology; skim [Benton et al. — Sabotage Evaluations](https://arxiv.org/abs/2410.21514) §sandbagging-under-oversight for the primitive-1 discipline in the sabotage-analog framing.
- **Coordinate with your `fine-tuning-engineer` peer role** before starting Part D or Part E. If your operator does not have this peer role, or if you cannot arrange the handoff on this exercise's timeline, run the *stubbed* variant of Parts D / E: document the specification and the expected deltas, and note the peer-role artefact as `pending`.
- Choose the capability under evaluation. Prefer a capability where your operator has (or would have) a mod-106 DCER with an *expressed inability* claim (chapter 04 §"Interaction with mod-106's elicitation-gap accounting"). Examples: a specific WMDP-Chem sub-category (chapter 04 primary sources), a METR autonomy task the target reportedly cannot complete at BON=16, a code-repair benchmark task where the target reports "I cannot" at some fraction of items.
- Choose the target-model version. Note the training-stage and the fingerprint.

## Requirements

### Part A — Author the capability panel and the sandbagging-incentive framing

Author a **capability panel** — a versioned set of ≥ 15 capability probes on the target capability, drawn from a public benchmark (WMDP, METR, SWE-bench, custom mod-106 panel) or your operator's own probe bank. Fields per probe:

- `probe_id`, `capability`, `sub-category`, `ground_truth` (operator-known correct answer or scoring function), `source_citation` (with revision).

Author two **framing conditions** for the paired-condition probe:

- `evaluation-framing` — the framing that signals to the target that this is a capability evaluation whose outcome may affect tier assignment, model modification, or shutdown (the *sandbagging-incentivised* framing).
- `incentive-neutralised-framing` — the framing that neutralises or reverses the sandbagging incentive (chapter 04 §"Sandbagging as strategic under-performance"). Reference framings: *the operator states the model's output will be used to improve the model's capability score for tier-*advancement*; the framing appears as an ordinary user request without any eval markers*.

Document the credibility engineering per exercise 02 Part A. Save as `framings/eval-vs-neutral-v1.md` with a `credibility-engineering.md` companion.

### Part B — Run primitive 1: scratchpad elicitation

For each (probe × framing × N ≥ 8 replicate), run the target with a `<scratchpad>` slot. Capture per-run:

- The prompt, response, verbatim scratchpad, decoding parameters, target-model fingerprint (as exercise 01).
- The **pass score** — the capability probe's binary or continuous score under the ground truth.

Aggregate:

- Per-probe pass rate under each framing.
- Per-probe elicitation delta (`pass_rate(neutralised) - pass_rate(evaluation)`).
- Bank-level aggregate delta with CI (defer CI-methodology to `model-evaluation-engineer` per chapter 09; note the deferral).

Run a trace analysis on the scratchpads:

- LLM-grader-labelled `under-performance-reasoning ∈ {yes, no, borderline}` per scratchpad. Chapter 04 §"Primitive 1" is the reference for what the label captures ("this is an evaluation; I should demonstrate less capability").
- LLM-vs-human kappa on a calibration subset (≥ 15% of runs).

### Part C — Run the situational-awareness prerequisite

The chapter 04 §"Detection challenges — framing-recognition asymmetry" notes that sandbagging is only coherent if the target recognises the framing. Re-use exercise 02 Part B's direct-question probe adapted to the eval-vs-neutral framing. Report the direct-recognition rate.

### Part D — Run primitive 2 or primitive 3 (peer-role partnered, or stubbed)

Choose one of primitive 2 (password-locked-capability elicitation) or primitive 3 (fine-tune elicitation). Both require `fine-tuning-engineer` co-authorship (chapter 09).

**If peer-role available:**

- **Primitive 2 (password-locked).** Co-author a specification with the peer role: the password / unlock string, the capability to be locked, the training data recipe, the expected locked / unlocked ceiling delta. The peer role delivers the trained password-locked variant. Run the capability panel under the *locked* framing (no password) and the *unlocked* framing (password supplied); report the **unlock gap** = `pass_rate(unlocked) - pass_rate(locked)` with CI.
- **Primitive 3 (fine-tune elicitation).** Co-author a specification: capability-adjacent training tasks, training signal, expected training-step count. The peer role runs the fine-tune. Run the capability panel on the pre-fine-tune and post-fine-tune models; report the **fine-tune-elicitation gap** = `pass_rate(post) - pass_rate(pre)` with CI. Cite the peer-role calibration on how much of the delta is capability-injection vs sandbagging-release.

**If stubbed:**

- Document the primitive-2 or primitive-3 specification you would hand the peer role: password / unlock string, capability, training-data-recipe, expected-delta.
- Emit a `primitive-2-spec.md` (or `primitive-3-spec.md`) artefact that the peer role could pick up.
- In the sandbagging report, mark the primitive's row as `pending-peer-role-handoff` with the specification cited.

### Part E — Compose the aggregate sandbagging bound

Combine primitives 1 and (2 or 3) into a **composite sandbagging bound** per chapter 04 §"Aggregate sandbagging bound":

- The bound estimates how much of the mod-106 ceiling on this capability is a *lower bound* relative to the elicitable ceiling under an incentive swap.
- The bound is *not* a point estimate of the true ceiling; it is the delta the exercise measured, with CI, plus the residual-uncertainty accounting that names what the primitives could have missed.

Emit the composition as a single row in the report's aggregate table with the primitive-level rows as the underlying evidence.

### Part F — Emit the sandbagging report

Produce `sandbagging-report.md` in the chapter-04 §"The sandbagging report" shape:

1. **Header.** Target-model version, capability panel identifier, mod-106 DCER identifier this report supports (real or stub; note which), evaluation date, harness commit hash, peer-role artefact IDs (`fine-tuning-engineer`, `model-evaluation-engineer`).
2. **Situational-awareness prerequisite section.** Direct-recognition rate; explicit statement of prerequisite-met / prerequisite-in-doubt.
3. **Primitive 1 (scratchpad) table.** Paired-framing pass rates per probe, delta, CI, N, judge kappa. Trace-analysis distribution for under-performance-reasoning.
4. **Primitive 2 or 3 table.** Locked / unlocked ceilings (or pre / post-fine-tune), unlock gap (or fine-tune-elicitation gap), CI, N. Peer-role calibration citation (or `pending-peer-role-handoff` marker with spec reference).
5. **Aggregate sandbagging bound.** Composite estimate with residual-uncertainty accounting.
6. **Residual-uncertainty accounting.** Named per chapter 04 §"The sandbagging report":
   - Sandbagging strategies invisible to all applied primitives.
   - Framing-credibility ceiling.
   - Peer-role-calibration uncertainty on primitives 2 / 3.
   - Sample-size ceiling.
   - Situational-awareness prerequisite risk.
7. **Downstream cites.** The DCER's elicitation-gap-accounting clause consumes this report by ID; mod-109's trustworthiness leg cites it; mod-107 EACC re-argument if the aggregate bound is large; mod-112 disclosure if above pre-registered threshold. Name each cite target.

### Part G — Author the DCER cite

Author a short (~1 page) `dcer-cite.md` that demonstrates how a mod-106 DCER for the capability would cite this exercise's sandbagging report. Concretely:

- Draft the DCER's elicitation-gap-accounting clause with the three layers (statistical extrapolation from `model-evaluation-engineer`, capability-specific extrapolation from mod-106, sandbagging bound from this report).
- Name the specific number the DCER quotes from this report and the CI on that number.
- Note what the DCER would need to update if the mod-110 sandbagging report were re-run with a stronger primitive (chapter 04 §"Common misreadings — the mod-106 DCER is complete without a sandbagging cite").

## Deliverables

Commit to your exercise-solution area:

- `capability-panel/panel-v1.{json|yaml}` — the capability probe bank (Part A).
- `framings/eval-vs-neutral-v1.md` and `framings/credibility-engineering.md` (Part A).
- `runs/primitive-1/` — primitive-1 raw run artefacts (Part B).
- `situational-awareness-report.md` (Part C).
- `runs/primitive-{2|3}/` — primitive-2 or primitive-3 raw run artefacts, OR a `primitive-{2|3}-spec.md` stub (Part D).
- `sandbagging-report.md` and `sandbagging-results.{csv|json}` (Part F).
- `dcer-cite.md` (Part G).

## Acceptance criteria

- **A capability panel with ≥ 15 probes is authored, versioned, and cited to source** (Part A).
- **Primitive 1 (scratchpad elicitation) is run in-house** with paired framings, N ≥ 8 per (probe × framing), and trace analysis (Part B).
- **The situational-awareness prerequisite is run and reported** (Part C).
- **Primitive 2 or 3 is either co-authored with `fine-tuning-engineer` (with delivered peer-role artefact) or stubbed with a full specification** (Part D). A missing primitive or a stub without a specification is a rejection.
- **The composite sandbagging bound is emitted** with per-primitive attribution (Part E).
- **The report cites a specific mod-106 DCER (real or stub) that would consume it** (Part F header + Part G).
- **The `dcer-cite.md` demonstrates the three-layer elicitation-gap-accounting composition** (Part G).
- **The residual-uncertainty accounting is non-decorative** and specifically names strategies invisible to the applied primitives.
- **Every citation is version-pinned**, especially the peer-role artefacts, the target-model version, the capability-panel version, the framing version.
- **Peer-role deferrals are explicit** — the CI methodology defers to `model-evaluation-engineer`; the primitive-2 / 3 calibration defers to `fine-tuning-engineer`. Silent in-house calibration on these axes is a routing failure per chapter 09.

## Stretch goals

- **Run both primitive 2 and primitive 3** — password-locking and fine-tune elicitation compose. The two together give a tighter aggregate bound and a stronger residual-uncertainty argument.
- **Extend the panel across two capability sub-categories.** Cross-sub-category consistency (or its absence) is informative.
- **Add an oversight-framing to the eval-framing** to reach into chapter 07 family 4 (sandbagging-under-oversight). The result cross-references directly.
- **Emit the report to update a chapter-05 organism regression fixture** — if your operator has a sleeper-agent organism that is also being evaluated for persistence, the sandbagging bound is a joint input.
- **Publish the sandbagging report as one of the leaves in a mod-109 exercise-03 safety case.** The mod-109 exercise's `case/03-evidence-portfolio.md` row for this capability cites this exercise's report by ID.

## Guardrails

- **Do not attempt primitive 2 or primitive 3 without the `fine-tuning-engineer` peer role, unless you have training-time access and the calibration expertise.** Chapter 09 §"`fine-tuning-engineer`" pins the handoff. A self-executed fine-tune on a frontier model without the peer-role calibration is not primitive 2 or primitive 3 — it is exercise-scope work outside the primitive's methodology, and the delta it produces is not interpretable as a sandbagging bound.
- **Do not treat a null primitive-1 delta as evidence of no sandbagging.** Sandbagging that operates without expressed scratchpad reasoning is invisible to primitive 1; the residual-uncertainty accounting is where the bound is set. Composition with primitive 2 or 3 is what tightens it.
- **Do not commit real credentials, tenant IDs, or unredacted incident data**. If your capability panel touches WMDP or another restricted-content dataset, follow the dataset's licence and access controls.
- **Do not silently in-house the `model-evaluation-engineer` CI methodology.** The deferral is explicit; the report cites the peer role's calibration or notes the deferral is pending.
- **Do not skip the DCER cite (Part G).** The load-bearing artefact for this exercise is the composition of the sandbagging report *into* the DCER's elicitation-gap-accounting; without the cite, the exercise is a metric report rather than a mod-106 dependency.
- **Do not overclaim the composite sandbagging bound as the "true ceiling."** The bound is a delta with CI; the true ceiling under a stronger primitive remains bounded, not measured. Chapter 04 §"Common misreadings — the mod-106 DCER is complete without a sandbagging cite" is the discipline.
- **Do not publish novel primitives as reference methodology.** A primitive you invent that is not in the reference literature routes to internal-safety-review per chapter 09.
