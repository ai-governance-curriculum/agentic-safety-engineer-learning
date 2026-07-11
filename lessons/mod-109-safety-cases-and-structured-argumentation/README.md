# mod-109-safety-cases-and-structured-argumentation: Safety Cases and Structured Argumentation

> Scaffolded by `aicg org execute-plan`. Lecture chapters and exercise content are authored on subsequent autonomous cycles.

**Estimated effort:** 12 hours

## Learning objectives

- Read Clymer et al. 'Safety Cases: How to Justify the Safety of Advanced AI Systems' and Goemans et al. 'Safety Case Template for Frontier AI' as the working templates for frontier-AI safety cases
- Adopt Goal Structuring Notation (GSN Community Standard v3) as the structured argumentation format — goals, strategies, solutions, contexts, assumptions, justifications, undeveloped-goal markers
- Distinguish the four safety-case argument shapes — Inability (the model cannot cause the harm), Control (the deployment prevents the harm even if the model tries), Trustworthiness (the model reliably will not try), and Deference (external evidence / third-party evaluators back the claim)
- Author a safety case for one concrete high-risk deployment — modelled on RSP / Preparedness / FSF tier decisions and on the AISI pre-deployment reports — with evidence types (dangerous-capability eval, red-team, control-eval, incident-history, alignment-check) explicitly cited and rebuttal handling
- Reason about safety-case pitfalls — evidence-provenance gaps, argument-completeness gaps, elicitation-gap ambiguity, moving-target thresholds, rebuttal-handling — and how each drives red-team demand back at earlier modules
- Cite the boundary to `ai-evaluation-engineer` (peer / next-up, level 35) on the release-assurance packaging that consumes the safety case and to `senior-ai-governance-architect` (level 50) on the control-library architecture the case defers to

## Structure

- `01-…md` … `0N-…md`: lecture chapters.
- `exercises/`: per-exercise prompts.
- `labs/`: long-form hands-on labs.
- `quizzes/`: knowledge checks.
- `resources.md`: external references.
