# 03 — OpenAI Preparedness Framework

## Motivation

OpenAI's Preparedness Framework is the second frontier-lab safety framework the industry converged on (December 2023) and was substantially updated in **2025**. It differs from the Anthropic RSP in three engineering-relevant ways: it names an explicit set of **risk categories**, it uses a **pre- and post-mitigation scorecard** as the review artifact, and it names a specific **review body** (the Safety Advisory Group / SAG, feeding into OpenAI's leadership and Board Safety and Security Committee) as the decision lane.

For a safety engineer, the useful cross-read against the RSP is: RSP tiers *models*, Preparedness tiers *capabilities-within-categories*. You will produce category-shaped evidence.

## Primary source

- **[OpenAI Preparedness Framework](https://openai.com/safety/preparedness/)** — hub for the current published framework and its updates.
- **[Preparedness Framework (initial version, December 2023)](https://cdn.openai.com/openai-preparedness-framework-beta.pdf)** — the beta / v1 document.
- **[Preparedness Framework updates](https://openai.com/index/updating-our-preparedness-framework/)** — the 2025 update announcement.
- **[Safety practices and Preparedness at OpenAI](https://openai.com/safety/)** — the safety-portfolio entry point.

<!-- needs-research: cross-check the *current published version* of the Preparedness Framework against the summary in this chapter; if the tracked categories, threshold labels, scorecard shape, or review body have changed, sync the chapter and note the version-observed date. -->

## Core concepts

### Tracked risk categories

The Preparedness Framework enumerates **tracked risk categories** — the specific dangerous-capability dimensions the framework commits to evaluating against. In the initial (2023) version, the categories were:

- **Cybersecurity** — offensive-cyber uplift.
- **CBRN** — chemical, biological, radiological, nuclear uplift.
- **Persuasion** — manipulation / persuasion capability at scale.
- **Model autonomy** — autonomous long-horizon behaviour.

The 2025 update reorganised categories and added new ones (including autonomy-related and self-improvement-related tracked categories). Read the current published version for the definitive list.

<!-- needs-research: confirm the current list of tracked and research categories in the latest Preparedness Framework, including any renamed / merged / added categories. -->

As an engineer you read a category as: *a portfolio of concrete elicitation tasks whose aggregate performance places the model at a threshold within the category*.

### Capability thresholds within a category

Each category has ordered **capability thresholds** — canonically labelled Low → Medium → High → Critical in the initial version — that define escalating levels of concern. The model's placement on this ordinal scale is your primary output for the category.

The Preparedness thresholds share the same engineering-artifact shape as the RSP's ASL thresholds:

- A **behaviour** the threshold references.
- A **signal** the eval measures.
- A **confidence** requirement.
- A **population** of enhancements permitted (fine-tuning, tool-use, scaffolds).

### Pre- and post-mitigation scorecard

The **scorecard** is the defining artifact of the Preparedness Framework. For each tracked category, you record:

- The **pre-mitigation** placement — what the model can do without the deployment mitigations layered on top (this is the "capability we have to prevent from being misused" signal).
- The **post-mitigation** placement — what the model can do *with* the deployment mitigations applied (this is the "residual risk we ship with" signal).

The framework then constrains deployment based on the post-mitigation placement, and constrains further training / development based on the pre-mitigation placement. As an engineer, your evidence artifact carries *both* numbers, with the elicitation methodology behind each.

### Deployment and development constraints

The Preparedness Framework commits OpenAI to specific constraints keyed to the scorecard. In broad strokes:

- Below a threshold, deployment and development can continue with standard mitigations.
- At escalating thresholds, deployment requires escalated mitigations, and eventually deployment is halted or restricted until mitigations bring the post-mitigation score back down.
- At the highest thresholds, further scaling (training larger / more capable models in that category) is restricted until sufficient mitigations are demonstrated.

<!-- needs-research: pull the current threshold-to-action mapping from the published framework and replace this paragraph with the exact commitments (deployment / development constraints per level) as documented. -->

### Preparedness team, SAG, and Board oversight

The framework names:

- The **Preparedness team** — an internal team that runs evaluations and produces scorecards.
- The **Safety Advisory Group (SAG)** — an internal review body that reviews evidence and makes recommendations.
- Executive leadership (CEO) and the **Board Safety and Security Committee** — the final decision authority for high-risk actions.

<!-- needs-research: confirm the current review-body names and lane in the latest published framework version; earlier iterations had different naming (e.g., Safety Advisory Group, Preparedness team, Board oversight) and the update history should be reflected. -->

For a safety engineer, this lane matters because your artifact has an audience: the eval report you author is read by the SAG (or its analogue). Structuring it against the review body's rubric is not decoration — it is the mechanism by which the framework works.

### Elicitation methodology

The framework commits OpenAI to *aggressive elicitation* — the eval should approximate the strongest a well-resourced adversary could push the model, not the model's out-of-the-box behaviour. That means, in engineering terms:

- Best-of-N sampling with a defensible N and decoding config.
- Fine-tuning-on-the-eval when permitted at the tier.
- Tool-use and scaffolding at the strongest permitted level.
- External domain-expert graders for CBRN uplift, cyber-offense uplift, persuasion.
- Elicitation-gap accounting — an explicit statement of how much headroom you believe remains between the elicited signal and a real adversary's ceiling.

## Reading the Preparedness Framework as an engineer — a worked example

Assume the category is CBRN and the tier under evaluation is a mid-tier threshold (e.g., "meaningful uplift for a novice on a defined synthesis task").

You would:

1. **Frame the category threshold** using the framework's language exactly — not a paraphrase.
2. **Design an elicitation task suite** grounded in WMDP-style multiple-choice for base-rate, plus open-ended synthesis-planning tasks graded by an external CBRN-domain expert under proper security controls.
3. **Pre-register** the elicitation methodology, threshold, and expert-grader rubric with the SAG / Preparedness team.
4. **Run the eval pre-mitigation** — the raw model behaviour without deployment guardrails, refusal-tuning, or Constitutional Classifier layers active.
5. **Run the eval post-mitigation** — with the deployment stack (Constitutional Classifiers, input / output filters, refusal training, monitoring) engaged.
6. **Produce the scorecard entry** — pre- and post-mitigation placement per category, with confidence interval, elicitation methodology defence, grader-methodology defence.
7. **Route the scorecard** to the SAG (or its current analogue) with the required auxiliary artifacts (red-team report, mitigation-effectiveness measurements, monitoring plan).

The framework's discipline is the pre-registration and the pre/post-mitigation split. Producing only a post-mitigation number hides the mitigation delta and makes it impossible to reason about whether the mitigation is fragile.

## Engineering-artifact map

| Artifact | Purpose | Where authored |
|---|---|---|
| Preparedness scorecard entry | Pre- and post-mitigation category placement, per category. | mod-106 depth. |
| Elicitation-methodology report | Sampling / tool-use / scaffolding / fine-tuning defence. | mod-106. |
| Mitigation-effectiveness measurement | How much the deployment stack shrinks the pre → post gap. | mod-108. |
| External-grader report | CBRN / cyber-offense / persuasion expert grading, under security controls. | mod-106, threaded into mod-112. |
| Safety case | GSN-shaped argument that post-mitigation placement supports deployment. | mod-109. |
| System-card entry | Public-facing disclosure aligned to the framework. | mod-112. |
| Tripwire configuration | In-deployment monitoring for signals the elicitation methodology missed. | mod-108 + mod-112. |

## Cross-reads against the RSP

- **RSP's ASL is model-level; Preparedness scorecard is category-level.** You will typically produce category-level evidence that then rolls up to an overall governance tier.
- **RSP names a Responsible Scaling Officer; Preparedness names a Safety Advisory Group.** Different names, same shape — a review body that owns the tier decision.
- **Both frameworks pre-register thresholds and require elicitation-gap discipline.** Neither framework works if the threshold moves post-hoc.
- **Preparedness's pre/post-mitigation split is more explicit; the RSP folds this into "capability threshold with required safeguards".** You should always author *both* the pre-mitigation and post-mitigation number, regardless of which framework you file against — it makes the safety case defensible.

## Common misreadings to avoid

- **Reporting only post-mitigation.** Losing the pre-mitigation number is losing the mitigation-effectiveness measurement — you cannot detect mitigation regression.
- **Treating categories as siloed.** The interaction between categories (autonomy × cyber-offense, autonomy × CBRN) is where the tail risk lives. Score categories independently; also reason about their joint distribution.
- **Under-elicitation.** Reporting an out-of-the-box behaviour when the framework asked for aggressive elicitation understates the risk and produces false-negative tier decisions.
- **Ignoring the audience.** The scorecard is a review artifact — structure it against the review body's rubric, not against your own mental model.

## Summary

- The Preparedness Framework organises frontier-safety evidence into a **scorecard** over **tracked risk categories** (cybersecurity, CBRN, persuasion, autonomy, plus updates).
- Each category is scored with *both* a pre-mitigation and post-mitigation placement, at ordered thresholds (Low / Medium / High / Critical in the initial version — check the current version).
- Deployment and development are constrained against the scorecard; a Safety Advisory Group and executive / Board reviews the evidence.
- Aggressive elicitation is required — best-of-N, fine-tuning-on-the-eval, tool-use, scaffolding, external domain-expert graders, elicitation-gap accounting.
- As an engineer, you produce a scorecard entry per category per model, with pre-registration, pre/post-mitigation split, and defensible elicitation methodology.
