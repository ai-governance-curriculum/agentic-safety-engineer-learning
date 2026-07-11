# 04 — Google DeepMind Frontier Safety Framework (FSF)

## Motivation

Google DeepMind's Frontier Safety Framework (FSF, May 2024, updated February 2025) is the third industry-shape frontier-safety framework and the one that most explicitly names **Critical Capability Levels (CCLs)** as its unit of decision. If you work at DeepMind or at an organisation using Google Cloud AI infrastructure, this is your framework. Even if you work elsewhere, the FSF is a useful cross-read: it puts more weight on the *misalignment* dimension than the RSP or Preparedness Framework, and its threshold vocabulary (CCL) has been picked up in academic and policy conversations.

## Primary source

- **[Introducing the Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/)** — original May 2024 announcement.
- **[Frontier Safety Framework — full text](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/)** — DeepMind's public-policy hub for the current published version.
- **[Updating the Frontier Safety Framework (Feb 2025)](https://deepmind.google/discover/blog/updating-the-frontier-safety-framework/)** — the second-version update announcement.
- **[Google DeepMind Responsibility & Safety](https://deepmind.google/about/responsibility-safety/)** — the broader safety-portfolio entry point.

<!-- needs-research: cross-check the current published version of the FSF (including CCL names, threshold definitions, mitigation-level mapping, and review process) against the summary in this chapter; if any of these have changed, sync and note the version-observed date. -->

## Core concepts

### Critical Capability Levels (CCLs)

A **Critical Capability Level** is a threshold of capability that, if reached without appropriate mitigations, could pose a significant risk of severe harm. Each CCL is defined against a specific harm dimension.

Broad CCL families named in the framework include:

- **Autonomy / R&D-uplift CCLs** — capability to autonomously carry out long-horizon ML R&D or engineering work; capability to substantially accelerate frontier-model development.
- **CBRN / bio uplift CCLs** — capability to provide meaningful uplift on chemical, biological, radiological, or nuclear tasks.
- **Cyber-offense uplift CCLs** — capability to substantially uplift offensive-cyber tasks.
- **Misalignment CCLs** — capability to circumvent developer oversight or engage in deceptive-alignment / instrumental reasoning that undermines the safety story. The February 2025 update sharpened this dimension.

<!-- needs-research: pull the definitive CCL list, exact names, and threshold definitions from the current published FSF. -->

Each CCL is defined by (a) the capability, (b) the threshold, and (c) the rationale (the harm story it prevents). As an engineer you read a CCL the way you read an RSP capability threshold or a Preparedness category — as an evaluation contract.

### Misuse vs. misalignment CCLs

A distinguishing feature of the FSF is the explicit split between:

- **Misuse CCLs** — capabilities that raise concern because a human actor might misuse them (bio uplift, cyber-offense uplift, autonomy-for-harmful-goals).
- **Misalignment CCLs** — capabilities that raise concern because the *model itself* might behave in ways that undermine oversight (deceptive alignment, instrumental reasoning to preserve or acquire capabilities, sandbagging safety evaluations).

Misalignment CCLs anchor to the AI-control and adversarial-alignment evaluation methodology this track covers in mod-110 (deception, alignment-faking, sandbagging, sleeper-agents, in-context scheming, sabotage). The FSF cites this literature explicitly.

### Mitigation levels

The FSF pairs each CCL with a **mitigation level** — a set of deployment and security mitigations required before the model can be deployed or continued to be scaled beyond that threshold. The mitigation levels are cumulative and escalating, analogous to the RSP's required safeguards.

Broad mitigation families include:

- **Deployment mitigations** — safety training, monitoring, tool-use restrictions, refusal training, external red-team review.
- **Security mitigations** — weights security, insider-risk controls, supply-chain integrity, access controls proportionate to the CCL.
- **Governance mitigations** — pre-registration of thresholds, external oversight where applicable, board or executive review.

<!-- needs-research: confirm the exact mitigation-level taxonomy in the current published FSF. Earlier versions used numbered / named mitigation levels — sync the chapter to the current text. -->

### Evaluation cadence

The FSF commits to **early-warning evaluations** — capability evaluations run against the *next* CCL threshold before it is reached, so that mitigations can be developed in advance. Concretely:

- Evaluations run at regular training milestones.
- Evaluations use aggressive elicitation, similar to the Preparedness Framework's discipline.
- If an evaluation shows a CCL approaching, mitigations for that level are prepared *before* the model is deployed.

### Review process

The FSF names an internal review process for tier decisions. The specific bodies have evolved between versions.

<!-- needs-research: pull the current named review body / process from the framework text. -->

## Reading the FSF as an engineer — a worked example

Assume the CCL under evaluation is a misalignment CCL — for concreteness, one that references the model's capability to reason about oversight in ways that could undermine safety evaluations (deceptive alignment / sandbagging).

You would:

1. **Frame the CCL** using the framework's language.
2. **Design the evaluation** against the AI-control and deception-evaluation literature (this is exactly what mod-110 covers): Apollo deception evaluations, alignment-faking probes, sandbagging measurement via elicitation-gap analysis, hidden-scratchpad probes, in-context scheming tasks.
3. **Pre-register** the eval methodology.
4. **Run the eval** with permitted enhancements (including elicitation-gap enhancement techniques to detect sandbagging).
5. **Produce the CCL decision** with confidence intervals.
6. **Emit the evidence artifact** — a report aligned to the review body's rubric.
7. **Layer mitigations** — the mitigation level attached to this CCL becomes the input to your containment (mod-107) and guardrail (mod-108) architecture.

## Engineering-artifact map

| Artifact | Purpose | Where authored |
|---|---|---|
| CCL evaluation report | Per-CCL evaluation methodology and result. | mod-106 + mod-110 depth. |
| Early-warning evaluation report | Evaluation against the *next* CCL, run in advance. | mod-106 + mod-110. |
| Mitigation-level checklist | Named mitigations for the CCL, with owner and evidence. | mod-107 + mod-108. |
| Safety case | GSN-shaped argument that mitigations for the CCL are adequate. | mod-109. |
| Misalignment evidence artifact | Deception / alignment-faking / sandbagging / sleeper-agent evidence per misalignment CCL. | mod-110. |
| Cross-lab reasoning | Contribution to Frontier Model Forum working papers where FSF concepts feed a joint publication. | mod-112. |

## Cross-reads against the RSP and Preparedness Framework

- **RSP tiers models (ASL-N); Preparedness tiers categories on a scorecard; FSF tiers CCLs.** All three shapes are conceptually equivalent — an evaluation contract that names capability, threshold, and required mitigation — but the unit of decision differs.
- **The FSF explicitly separates misuse and misalignment CCLs.** The RSP and Preparedness Framework fold these together to a greater extent. If you want the cleanest handle on the misalignment side, the FSF is the framework to lift from.
- **All three commit to aggressive elicitation.** Elicitation methodology is the load-bearing engineering discipline across all three — this is why mod-106 sits where it does in the track.
- **All three name a review body.** The FSF's process has evolved; the RSP names a Responsible Scaling Officer; Preparedness names a Safety Advisory Group. Different names, same load-bearing decision authority.

## Common misreadings to avoid

- **Treating the misalignment CCLs as future work.** The AI-control literature is publishable *today*; mod-110 covers it. If your FSF safety case has empty misalignment-CCL evidence, the case is incomplete.
- **Under-elicitation on autonomy CCLs.** METR / RE-Bench / SWE-bench Verified all require careful elicitation-gap discipline; running the base model out of the box will systematically under-detect capability.
- **Confusing mitigation levels with deployment posture.** A mitigation level is a *floor*; deployment posture at that level can and should include additional voluntary mitigations if you have them.

## Summary

- The FSF organises frontier-safety evidence around **Critical Capability Levels** (CCLs), each with a threshold and a required mitigation level.
- Misuse CCLs and misalignment CCLs are separated; the misalignment side pulls in the AI-control / deception-evaluation literature this track covers in mod-110.
- Early-warning evaluations anticipate CCL crossings; aggressive elicitation is the required discipline.
- As an engineer you produce a CCL evaluation report per CCL, plus the mitigation-level checklist and safety-case evidence, aligned to DeepMind's review process.
