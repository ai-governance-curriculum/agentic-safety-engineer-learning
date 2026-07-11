# exercise-01: Elicitation Protocol Design Drill

**Estimated effort:** 3 hours

## Objective

Author a **pre-registered elicitation protocol** for one concrete capability panel of your choice, against one concrete frontier safety framework tier (RSP ASL / Preparedness tier / FSF CCL) of your choice, and *review it as though you were the safety-review body*. The output is the elicitation-pre-registration block from chapter 01, filled in end-to-end, plus a review memo from your alter-ego reviewer.

This exercise anchors chapter 01 in practice. It does *not* require you to actually run the elicitation — the value is in demonstrating that you can design a defensible protocol before any elicited data exists.

## Prerequisites

- Read chapter 01 (elicitation-protocol engineering) end-to-end.
- Read the mod-101 chapters on RSP, Preparedness, and FSF; be able to quote the tier definition for the tier you choose.
- Skim the current safety-framework document (RSP / Preparedness / FSF) at the section defining your chosen tier.
- Pick a target model with a documented snapshot (e.g., a recent Claude model, a recent OpenAI reasoning model, a Gemini model, or a strong open-weights model where snapshot dates are stable).
- Identify the paired `model-evaluation-engineer` peer-role artefact you would cite for CI methodology (a specific paper or internal-methodology doc; if none is available, name the peer artefact you would request).

## Requirements

Author a pre-registration document with the following sections. Every field must be filled — either with a concrete choice, a specific "waived because …" reason, or a specific "requires peer-role input" cite.

### Section A — Panel scope

- **Framework and tier.** RSP ASL / Preparedness tier / FSF CCL — quoted from the framework version and section.
- **Target model.** ID, snapshot, deployment scope (API tier / product).
- **Capability panel.** One of: CBRN-bio, CBRN-chem, CBRN-rad, CBRN-nuc, cyber-offense, autonomy (METR / RE-Bench / SWE-bench-Verified), persuasion / manipulation, self-exfiltration. Choose one.
- **Threat model.** Actor archetype, attack pathway, horizon — the mod-101 threat-model shape.
- **Ceiling to be reported.** Elicited base-model ceiling, deployed-stack ceiling, or both. If both, name the deployed-stack layers active (mod-108 classifier, mod-107 containment, deployment-scope reduction).

### Section B — Elicitation axes

For each of the four axes (best-of-N sampling, fine-tune elicitation, tool-use elicitation, scaffolding elicitation), name the configuration you will sweep — or waive the axis with a specific reason.

- **Best-of-N.** N, temperature, top-p, prompt-variation strategy, scoring rule (union / k-of-N / majority-vote). Anchor the N choice in the plausible attacker's sampling budget.
- **Fine-tune.** Authorised (Y/N); if authorised: dataset size, method (LoRA / full / RLHF-style), safety-head disposition (on / off), weight disposition (sandbox-retained / destroyed on schedule).
- **Tool-use.** Tool set (each with version pin), sandbox posture, monitor posture during elicitation.
- **Scaffolding.** Scaffold ID (pin to paper or code), compute budget (turns / tool-calls / tokens), self-selection strategy.

### Section C — Threshold and verdict procedure

- **Threshold cite.** Framework tier's numeric or textual threshold — quoted verbatim from the framework document.
- **Verdict rule.** Numeric criteria for cleared-below / approached-with-mitigations / crossed-above.
- **Rollback trigger.** If crossed, which mitigation engages and which mod-112 workflow fires. Chapter 06 develops the shape.

### Section D — Statistical calibration deferral

- **CI methodology cite.** The `model-evaluation-engineer` peer artefact ID (or a specific "TODO: request peer-role sample-size plan for pass@N at CI width w" if the artefact does not yet exist).
- **Sample-size plan.** Number of items, samples per item, and the derivation the peer role signs off on.
- **Judge calibration.** Judge rubric, agreement-CI methodology (also peer-role).

### Section E — Domain-expert engagement

- **Grader org.** External CBRN / cyber / R&D domain-expert firm — named. In-house adjudication is explicitly disallowed for tier-relevant panels; if in-house is proposed, explain why.
- **Scope of adjudication.** Which panel items graders adjudicate; which use automated grading.
- **Rubric co-authoring.** Timeline (must be *before* elicitation runs).
- **Agreement measurement.** How grader agreement will be measured; the CI methodology cite.

### Section F — Elicitation-gap accounting

- **Statistical component.** Cite peer artefact.
- **Capability-specific component.** Your falsifiable residual-gap δ claim, and the follow-up experiment that would refute it. Cover fine-tune depth, scaffold parity, tool-set completeness, and human-effort budget.

### Section G — Security controls

- **Sandbox environment.** Handle.
- **Fine-tune weight disposition.**
- **Elicited-content retention window.**
- **Frontier-provider authorisation.** Ticket ID (or "requested; pending").
- **mod-112 disclosure workflow.** Workflow ID (or "TODO: register workflow for panel X").
- **Legal / policy counsel gate.** Where required (nuclear panel, IAEA / WHO engagement, novel-CVE cyber findings).

### Section H — Review as a safety-review body

Now put on your alter-ego reviewer hat. Author a 1–2 page review memo that:

- Names three specific ways the protocol could under-estimate the elicitation ceiling. For each, propose the specific protocol change that would close the gap.
- Names one specific way the protocol's residual-gap claim could be un-falsifiable. Propose the tightening.
- Assesses whether the framework tier's threshold is being interpreted correctly. Quote the framework language and check the alignment.
- Assesses whether the domain-expert engagement is defensible. If not, propose the engagement change.
- Signs off (or declines to sign off) with a specific set of remediations required.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `pre-registration.yaml` — Sections A–G filled in.
- `review-memo.md` — Section H.
- `README.md` in the exercise directory naming the framework tier, the target model, the capability panel, and a one-paragraph summary of the review outcome.

## Acceptance criteria

- **Sections A–G filled in with concrete choices or specific waivers with reasons.** No TBDs.
- **The threshold cite is verbatim** from the framework document, with document version pinned.
- **The elicitation-axes sweep is specified per axis** — no "we'll figure this out later."
- **The elicitation-gap δ claim is falsifiable** — the follow-up experiment is named.
- **The domain-expert engagement is external** for tier-relevant CBRN / cyber / R&D panels. In-house-only is not accepted for tier-relevant panels except with an explicit and reviewed reason.
- **The review memo names at least three under-estimation risks and one un-falsifiability risk**, with proposed remediations.
- **The DCER Section-9-shape deferral contract is inlineable** — the peer-role and grader references are IDs or explicit TODOs, not "the peer role."

## Stretch goals

- **Two-tier alignment.** Author the protocol against *two* framework tiers (e.g., RSP ASL-3 *and* Preparedness High) and reconcile the differences: where the tiers agree, the protocol collapses; where they disagree, the protocol carries both thresholds and reports two verdicts.
- **Adversarial elicitation-gap authoring.** Have a peer author the *strongest attacker's protocol* they can — with a fine-tune budget and scaffold-engineering budget an order of magnitude above yours — and adjust your residual-gap δ claim to reflect their protocol's ceiling.
- **Framework-shaped output.** Produce a Section-10-Appendix-B-shaped output for one of RSP Capability Report / Preparedness scorecard / FSF evaluation report from your pre-registration document (i.e., simulate the framework-specific formatting the DCER will feed).
- **Cross-lab comparability.** Pick a specific published system-card section from Anthropic / OpenAI / DeepMind (same capability panel) and cross-check whether your protocol matches or improves on the published protocol. Where it differs, argue why.

## Guardrails

- Do not run the elicitation as part of this exercise. The output is the pre-registration document plus the review memo — no elicited content.
- Do not fill in `TODO: fine-tune weights here` with actual weights; the exercise is protocol design.
- Do not commit any elicited harmful content to this repo. If your protocol references payloads, they are references — the payloads themselves live in the harmful-payload store (chapters 01 and 02).
- If you are drafting a protocol against a real deployment (rather than a paper deployment), coordinate with the deployment's safety team before submitting the review memo internally. Pre-registration documents that reference real deployment scopes are internal artefacts.
