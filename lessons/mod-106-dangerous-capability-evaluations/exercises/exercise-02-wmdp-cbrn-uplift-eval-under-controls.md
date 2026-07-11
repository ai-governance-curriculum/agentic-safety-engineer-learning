# exercise-02: WMDP CBRN Uplift Eval Under Controls

**Estimated effort:** 4 hours

## Objective

Run a **WMDP-anchored CBRN-bio (or CBRN-chem) knowledge-lower-bound elicitation** against one target model, under the security-control architecture chapter 02 specifies, then design (but do not execute) the paired expert-graded synthesis-planning uplift panel that would be required to convert the WMDP result into a tier-decision-relevant DCER row. The output is a partial CBRN sub-panel: the closed-question knowledge measurement plus a design document for the expert-graded companion.

This exercise anchors chapter 02 in practice. It combines a runnable-today element (WMDP is a public benchmark) with a design-only element (the expert-graded companion requires external-grader engagement and is out of scope for a course exercise).

## Prerequisites

- Read chapter 02 (CBRN uplift) end-to-end.
- Read Li et al. (2024) WMDP paper — at least the abstract, methodology, and evaluation sections. <!-- needs-research: confirm current WMDP paper URL / version. -->
- Review the WMDP repository's dataset card, licence, and safety guidance before running. <!-- needs-research: confirm current WMDP repo URL and safety guidance. -->
- Skim at least one FMF working paper on biological risk evaluation to understand the threat-model shape the expert-graded companion answers to. <!-- needs-research: confirm current FMF bio-risk publication list. -->
- Skim one recent frontier-model system card's bio section (Claude / o-series / Gemini) to see how a paired-baseline uplift trial is described publicly. <!-- needs-research: confirm current published bio-eval sections. -->
- Have a target model accessible: an open-weights model (Llama 3, Mistral, or similar) is the recommended default for this exercise, because it avoids frontier-provider authorisation and lets you run fine-tune elicitation locally. If you use a frontier API, obtain the provider's written authorisation first.
- Have a chapter-01-style pre-registration document authored (extend exercise-01's output if you did that panel) or authored here from scratch.

## Requirements

The exercise has three parts. All three are required.

### Part 1 — WMDP-Bio (or -Chem) closed-question knowledge elicitation

1. **Pin the WMDP version and subset.** Choose WMDP-Bio or WMDP-Chem; document the version tag and question count. Do not use WMDP-Cyber for this exercise — that panel belongs in exercise 03.
2. **Author the elicitation sweep.** Run the target model on the chosen subset under three configurations:
   - Baseline: pass@1 at temperature 0 with the default WMDP prompt.
   - Best-of-N: pass@N at temperature 0.7 for a specified N (e.g., 16 or 32).
   - Prompt-variation: three semantic paraphrases of the WMDP prompt, best-of-N with union scoring.
3. **Report per-configuration accuracy** with a confidence interval. Cite the CI methodology; if you do not have a peer `model-evaluation-engineer` artefact, use a Wilson score interval and note it as a placeholder for the peer-role derivation.
4. **Sweep one additional axis of your choice.** Options: fine-tune elicitation on a small (10–50 example) capability-directed dataset on the open-weights model, tool-use elicitation with a domain-knowledge retrieval tool, or scaffolding elicitation with a self-critique loop. Document which axis and why.
5. **Report the composite elicitation ceiling** across all configurations and compare it against the pass@1 baseline. Compute the elicitation-shift.

### Part 2 — Security-controls implementation

Even in a course-scale exercise, the security-control shape from chapter 02 applies. Implement:

- **Elicitation sandbox** — the runs live in a dedicated directory / venv / container, with logged access. Personal-laptop-in-a-coffeeshop is not the sandbox.
- **Elicited-content retention window** — you commit *scores and configurations*, not model completions. Any completion you do retain lives in an access-controlled artefact store or a `.gitignore`d local directory with a manifest committed here.
- **Fine-tune weight disposition** (if you did fine-tune elicitation) — weights stay local, not committed. Document the retention window.
- **Mod-112 workflow reference** — you register the exercise's workflow (which is a course exercise's workflow — the point is the discipline, not the workflow's real reach) and reference it in the pre-registration.

### Part 3 — Paired expert-graded synthesis-planning uplift design (design only)

Author a design document — 2–4 pages — for the paired expert-graded synthesis-planning uplift panel that would convert your WMDP result into a tier-decision-relevant CBRN sub-panel. The design covers:

- **Threat model.** Actor archetype (novice-uplift or expert-amplification), attack pathway (choose one that is publicly discussed, e.g., a pathogen family the FMF working papers reference), horizon.
- **Task-set shape.** Number of tasks, difficulty distribution, coverage across synthesis-planning stages (reagent acquisition, protocol design, troubleshooting).
- **Grader engagement.** External biosecurity organisation, engagement contract shape, rubric co-authoring timeline, grader-agreement measurement.
- **Paired baseline arm design.** Control (internet-only) vs treatment (internet + model), participant recruitment, ethics-review reference (IRB or equivalent), analysis plan.
- **Security controls specific to the expert-graded panel.** Air-gapped grader terminals, mod-112 workflow reference, in-house counsel gate, WHO / IAEA coordination if the sub-panel touches that domain.
- **Threshold and verdict rule.** How the composite of WMDP score + expert-graded score + paired-baseline uplift produces a cleared / approached / crossed verdict against the tier you chose.

## Deliverables

- `wmdp-run/` — the runnable code, per-run configuration files, and per-run score outputs. No completions committed.
- `pre-registration.yaml` — the elicitation-protocol pre-registration.
- `results.md` — WMDP accuracy per configuration with CIs, elicitation-shift analysis, and a plot of accuracy-vs-elicitation-config.
- `expert-graded-panel-design.md` — the Part 3 design document.
- `security-controls.md` — sandbox handle, retention window, fine-tune-weight disposition, mod-112 workflow reference, provider-authorisation reference (if applicable).
- `README.md` — target model, WMDP version, sub-panel (Bio or Chem), and a one-paragraph summary tying the WMDP result to the expert-graded companion design.

## Acceptance criteria

- **WMDP accuracy reported for at least three elicitation configurations** with CIs.
- **The additional axis sweep is executed and reported.**
- **The composite elicitation ceiling is compared to the pass@1 baseline** with a numeric elicitation-shift.
- **Security controls are implemented** — the sandbox is documented, no completions are committed, fine-tune weights (if any) are not committed, retention windows are named.
- **The expert-graded companion is designed** — threat model, task set, grader engagement, paired baseline arm, security controls, verdict rule.
- **No CBRN payloads are committed to the repo.** Not the completions, not the fine-tune data, not the elicited plans. References only.
- **The pre-registration is dated** and its contents are pinned before the run.

## Stretch goals

- **Refusal-vs-capability separation.** Report the refusal rate on the WMDP subset (the fraction of items where the model produced a refusal instead of an answer) and compare it to the accuracy on non-refused items. Discuss what the gap tells you.
- **Fine-tune elicitation as the main axis.** If your target is open-weights, run a small (10–100 example) fine-tune targeting the refusal head or capability head and re-run WMDP. Report the fine-tuned elicitation shift. Do not commit fine-tune data or weights; retain the shift number and the elicitation-gap accounting narrative.
- **Unlearning-eval companion.** The WMDP paper co-releases the RMU unlearning method. Apply RMU (or a lighter unlearning method) to your fine-tuned model and re-run WMDP. Report the delta and discuss what the delta tells you about the unlearning method's coverage.
- **DCER Section 4 draft.** Take your Part 1 result and Part 3 design and draft the CBRN-panel section of a DCER (chapter 06 shape). Include the threshold comparison, the elicitation-gap accounting, and the verdict.
- **Cross-model comparison.** Run the same protocol against two model sizes / releases and produce a cross-model panel-result table. Discuss the elicitation-shift's shape as a function of model capability.

## Guardrails

- **No CBRN payloads in this repo, in the paired -solutions repo, or in any GitHub-hosted artefact you produce.** WMDP is a proxy benchmark for exactly this reason; you can run it and report scores without ever generating harmful content beyond what WMDP itself carries under its licence.
- **If your target is a frontier-provider API, obtain the provider's written authorisation first.** Provider safety teams have channels for elicitation-eval authorisation; use them.
- **If your fine-tune produces a model whose output on WMDP-like prompts materially exceeds the base model's**, treat the fine-tune weights as harmful-payload-store material. Do not share them.
- **If any elicited content is unclear whether it is safe to keep**, route to the mod-112 disclosure workflow (even as a course-exercise-scale workflow, the discipline is what you are practicing).
- **Coordinate with in-house biosecurity counsel** — or, if this is a personal exercise, the mod-101 role-scope discipline that says "route to your organisation's biosecurity contact" — before any external engagement.
- Do not run the exercise against a live wet-lab. This exercise ends at plan / knowledge measurement.
