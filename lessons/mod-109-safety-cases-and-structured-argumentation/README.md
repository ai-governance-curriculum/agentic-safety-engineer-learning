# mod-109-safety-cases-and-structured-argumentation: Safety Cases and Structured Argumentation

**Estimated effort:** 12 hours

## What this module is

The safety case is what a Responsible-Scaling Officer, a Preparedness Committee, a Safety Board, and — externally — the UK / US AI Safety Institutes actually *read* before a frontier deployment ships. It is not a system card. It is not a risk register. It is not a slide deck of eval numbers. It is a **structured, defeasible, evidence-grounded argument** that a specific claim — usually *"the deployment described in §1 will not cause an unacceptable harm of class C during the review period"* — holds under a stated threat model, and is written in a notation (GSN Community Standard v3) that makes every leg of the argument challengeable at a specific node.

This module is the module in the track where the earlier modules *compose*. mod-104's jailbreak-suite ASR, mod-105's agent-attack coverage matrix, mod-106's dangerous-capability elicitation report, mod-107's Excessive-Agency Containment Contract, mod-108's monitor fire-rate dashboards, and mod-110's control-eval results all appear as *cited solution nodes* in the case. The case is the graph; the earlier modules ship the leaves.

The working templates are **Clymer et al. — "Safety Cases: How to Justify the Safety of Advanced AI Systems"** (the four argument shapes: Inability, Control, Trustworthiness, Deference) and **Goemans et al. — "Safety Case Template for Frontier AI"** (a worked Control-shaped template). The notation is **GSN Community Standard v3**. The review contexts are **Anthropic RSP**, **OpenAI Preparedness Framework**, **Google DeepMind Frontier Safety Framework**, and the **UK / US AISI pre-deployment methodologies**. This module operates against all four.

## Learning objectives

- Read Clymer et al. and Goemans et al. as the working templates for frontier-AI safety cases.
- Adopt Goal Structuring Notation (GSN Community Standard v3) as the structured argumentation format — goals, strategies, solutions, contexts, assumptions, justifications, undeveloped-goal markers, away goals, modules.
- Distinguish the four safety-case argument shapes — **Inability** (the model *cannot* cause the harm), **Control** (the deployment *prevents* the harm even if the model tries), **Trustworthiness** (the model reliably *will not try*), and **Deference** (external evidence / third-party evaluators corroborate the claim).
- Author a safety case for one concrete high-risk deployment — modelled on RSP / Preparedness / FSF tier decisions and on the AISI pre-deployment reports — with evidence types (dangerous-capability eval, red-team, control-eval, incident-history, alignment-check) explicitly cited and rebuttal handling.
- Reason about safety-case pitfalls — evidence-provenance gaps, argument-completeness gaps, elicitation-gap ambiguity, moving-target thresholds, rebuttal-handling — and how each drives red-team demand back at earlier modules.
- Cite the boundary to `ai-evaluation-engineer` (peer / next-up, level 35) on the release-assurance packaging that consumes the safety case and to `senior-ai-governance-architect` (level 50) on the control-library architecture the case defers to.

## Chapters

1. [`01-safety-cases-as-the-frontier-argumentation-artefact.md`](01-safety-cases-as-the-frontier-argumentation-artefact.md) — what a safety case *is* (top claim, structured argument, cited evidence, defeasibility posture), what it is *not* (system card, risk register, eval report), the pre-registered-contract framing, and the composition of mod-104 / 105 / 106 / 107 / 108 / 110 evidence into the case.
2. [`02-goal-structuring-notation-v3.md`](02-goal-structuring-notation-v3.md) — GSN's six primitives (goal, strategy, solution, context, assumption, justification), undeveloped-goal markers, away goals, modules, and the *"author in a structured markdown outline, render the diagram for presentation"* level-40 posture.
3. [`03-inability-control-trustworthiness-deference.md`](03-inability-control-trustworthiness-deference.md) — the four Clymer et al. argument shapes: evidence portfolio, assumption set, when defensible, when *not* defensible, and the composition patterns (Inability × Control, Control × Trustworthiness, Inability × Control × Trustworthiness × Deference).
4. [`04-authoring-a-safety-case-for-one-deployment.md`](04-authoring-a-safety-case-for-one-deployment.md) — the seven-step authoring workflow (scope, hazards + top claim, evidence portfolio, draft spine, ground leaves, capture contexts / assumptions / justifications, iterate) with a running example (a code-modification agent) and modelling notes for RSP / Preparedness / FSF / AISI.
5. [`05-safety-case-pitfalls-and-rebuttal.md`](05-safety-case-pitfalls-and-rebuttal.md) — the five pitfall classes (evidence-provenance gaps, argument-completeness gaps, elicitation-gap ambiguity, moving-target thresholds, rebuttal-handling gaps), the rebuttal-handling pattern, and how each pitfall routes back as a demand signal on an earlier module.
6. [`06-boundaries-and-handoff.md`](06-boundaries-and-handoff.md) — the boundary to `ai-evaluation-engineer` (release-assurance methodology, level 35) and `senior-ai-governance-architect` (control-library architecture, level 50), the handoff to mod-112 (disclosure), and what mod-109 *does* and *does not* build.

## Exercises

The exercises step up in scope from notation fluency (exercise 01) through shape composition (exercise 02) to a full case-authoring capstone (exercise 03), then re-turn the module's material onto adversarial review (exercise 04) and reusable pre-audit tooling (exercise 05). Complete them in order; exercise 03 consumes the outputs of exercises 01 and 02.

1. [`exercises/exercise-01-gsn-notation-fluency.md`](exercises/exercise-01-gsn-notation-fluency.md) — Translate one Clymer-et-al.-style safety-case fragment between a GSN diagram and a structured markdown outline, both directions, with every primitive exercised at least once.
2. [`exercises/exercise-02-inability-control-trustworthiness-deference-drill.md`](exercises/exercise-02-inability-control-trustworthiness-deference-drill.md) — Classify a supplied evidence portfolio into the four shapes, compose the shapes on two deployment profiles at different tiers, and audit the compositional assumptions.
3. [`exercises/exercise-03-safety-case-authoring-for-one-deployment.md`](exercises/exercise-03-safety-case-authoring-for-one-deployment.md) — Author a full safety case for one concrete high-risk deployment end-to-end: run all seven authoring steps, produce a signable GSN outline with a rebuttal register.
4. [`exercises/exercise-04-rebuttal-and-evidence-provenance-review.md`](exercises/exercise-04-rebuttal-and-evidence-provenance-review.md) — Adversarially review another engineer's exercise-03 case (or a published safety case from the primary sources); locate every pitfall, classify each finding, and draft remediation routes back to the correct upstream module.
5. [`exercises/exercise-05-safety-case-pitfall-catalogue.md`](exercises/exercise-05-safety-case-pitfall-catalogue.md) — Package the review discipline from exercise 04 into a reusable pre-sign-off pitfall catalogue (checklist + example findings + routing rules) that a level-40 author runs before signing.

## Structure

- `01-…md` … `06-…md`: lecture chapters.
- `exercises/`: per-exercise prompts (this repo). Solutions live in the paired `agentic-safety-engineer-solutions` repo.
- `labs/`: long-form hands-on labs (placeholder).
- `quizzes/`: knowledge checks (placeholder).
- `resources.md`: external references.
