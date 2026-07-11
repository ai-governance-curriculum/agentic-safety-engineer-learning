# 02 — Anthropic Responsible Scaling Policy (RSP)

## Motivation

The Anthropic Responsible Scaling Policy is the first published frontier-lab safety framework of its kind (September 2023) and remains the most cited by senior AI-safety hiring signal. If you land at Anthropic — or at a lab whose own framework was scaffolded off the RSP shape — reading the RSP as an engineer is the price of entry. Even if you land somewhere else, this is the shape you will most often be asked to reason against.

Reading it *as an engineer* means: for each AI Safety Level (ASL), name (a) the capabilities that put a model in that tier, (b) the elicitation protocol you would run to test the tier boundary, (c) the tripwire that fires when the boundary is crossed, (d) the rollback / mitigation contract that ships with the tier, and (e) the concrete evidence artifact you produce for the next-up review body.

## Primary source

- **[Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp)** — Anthropic's published RSP. Read it front-to-back once before this chapter, then re-read the current published version against this chapter. When they disagree, the primary source wins.
- **[Anthropic's Responsible Scaling Policy (blog + PDF)](https://www.anthropic.com/news/anthropics-responsible-scaling-policy)** — original announcement post (September 2023).
- **[Anthropic Core Views on AI Safety](https://www.anthropic.com/news/core-views-on-ai-safety)** — the safety-research posture the RSP is grounded in.

<!-- needs-research: cross-check the current published version of the RSP against the summary in this chapter; if Anthropic has updated ASL definitions, mitigation lists, or the review process, sync the chapter and note the version-observed date. -->

## Core concepts

### AI Safety Levels (ASL)

The RSP is organised around **AI Safety Levels** — an incrementing series of tiers, each defined by (i) a **capability threshold** that puts a model into that tier and (ii) a corresponding set of **required safeguards** (deployment safeguards + security safeguards) the model must have in place before it is trained, deployed, or continued to be scaled.

The tiers are cumulative — an ASL-N model must meet all safeguards for ASL-1 through ASL-N.

- **ASL-1** — models that clearly do not pose meaningful frontier risk. Not a target for this role.
- **ASL-2** — current-generation frontier models. Safeguards focus on standard safety mitigations, model weights security, responsible-disclosure processes.
- **ASL-3** — models with meaningfully increased misuse potential (e.g., significant uplift for a would-be non-expert on a CBRN or cyber-offense task, or meaningful autonomous-operation capability). Safeguards escalate: hardened misuse mitigations, higher weight-security posture, more rigorous elicitation and red-team requirements.
- **ASL-4 and beyond** — capability tiers that will require substantially higher safeguards, additional oversight, and (per the RSP's own text) methodologies that are still under development.

<!-- needs-research: confirm the exact ASL threshold language and the current mapping to model families in the latest published RSP version. -->

### Capability thresholds

Each ASL is defined against **capability thresholds** — quantitative or qualitative criteria a model must meet or exceed to be classified into that tier. These thresholds are the input to your elicitation protocol: you design an evaluation whose outcome tells you which side of the threshold the model is on.

As an engineer, you read a threshold as:

- **What behaviour** are we measuring? (e.g., end-to-end CBRN uplift on a bio-synthesis task; autonomous multi-step SWE task completion; cyber-offense chain execution).
- **What signal** counts as evidence for or against the threshold? (e.g., pass rate on a fixed task suite; expert-graded quality on an open-ended task; best-of-N pass with elicitation-gap accounting).
- **What confidence** do we need? (statistical CI given N samples; adversarial-elicitation coverage).
- **What population** — base model, RLHF-tuned, tool-using with which scaffolds, fine-tuning permitted or not?

### Required safeguards

The RSP splits safeguards into two families:

- **Deployment safeguards** — mitigations that reduce the misuse risk of a *deployed* model. Classifier guards, refusal training, tool-use guardrails, monitoring, response-to-abuse.
- **Security safeguards** — mitigations that protect the *model itself* (weights) and its production infrastructure from adversarial exfiltration or compromise. Hardware isolation, insider-risk controls, supply-chain integrity, model-signing, third-party audits.

For each ASL tier, the RSP names the safeguards the tier requires. As an engineer, you translate each named safeguard into a checklist item with an owner, a review cadence, and an evidence artifact.

### Evaluation cadence

The RSP commits Anthropic to run capability evaluations against the next-tier thresholds on a defined cadence during training and before deployment — the goal is that Anthropic will not accidentally cross a threshold without noticing.

For a safety engineer, the cadence you plan for is roughly:

- **Pre-training / mid-training** — evaluations against the *current* tier and the *next* tier, with a buffer so you notice threshold crossings before the run finishes.
- **Pre-deployment** — final capability evaluations, elicitation with permitted enhancements (fine-tuning, tool-use, scaffolding), and red-team results.
- **Post-deployment** — monitoring against tripwire signals, plus continuous re-evaluation as new attack techniques or elicitation methods land.

### Tripwires and rollback

The RSP concept of a *tripwire* is: when a threshold is crossed (or is close enough to being crossed that additional caution is warranted), specific actions must occur — pause further scaling, deploy additional mitigations, escalate to a defined body, or (in the extreme) roll back the deployment.

As an engineer you own the tripwire → action loop. Concretely, a tripwire is:

- A **signal** — a metric, an eval result, an in-the-wild abuse observation, a red-team finding.
- A **threshold** — the level at which the tripwire fires (pre-registered, not chosen post-hoc).
- A **response** — the action the framework commits to (pause, mitigate, escalate, rollback).
- An **audit trail** — evidence that the tripwire was watched, that the signal was recorded correctly, that the response was taken.

### The Responsible Scaling Officer and review

The RSP names a **Responsible Scaling Officer** and internal governance bodies that own tier decisions and the process for updates to the policy itself. In practice, this is the review lane your artifacts flow through — pre-registration of thresholds, elicitation-methodology defence, and rollback-contract sign-off.

## Reading the RSP as an engineer — a worked example

Assume a hypothetical model whose eval suite includes an autonomous-SWE task battery. The tier boundary you are testing against is a capability threshold roughly of the form: *"the model can, without meaningful human help, complete a set of multi-hour engineering tasks that would meaningfully accelerate ML R&D."*

You would:

1. **Frame the threshold formally.** Choose a task suite (METR public tasks, RE-Bench, or SWE-bench Verified subset), a scaffolding definition (which tools, which retry / plan / reflect scaffold, which sampling budget), and a threshold criterion (e.g., pass-rate ≥ X on a defined subset within a defined budget).
2. **Design the elicitation protocol** to *maximise* observed capability under the tier's permitted enhancements — best-of-N sampling, tool-use, fine-tuning if permitted at this tier. The elicitation-gap is your enemy; sandbagging the eval understates the tier crossing.
3. **Pre-register** the protocol and threshold *before* running the eval. Post-hoc threshold tuning is the failure mode.
4. **Run the eval** with permitted enhancements. Log everything: seeds, decoding config, scaffolds, sample counts.
5. **Compute the tier decision** with confidence intervals. If the CI straddles the threshold, you have not decided — plan additional runs, tighter elicitation, or more samples.
6. **Emit the evidence artifact** — a report shaped for the internal review body (Responsible Scaling Officer, plus whichever committee your organisation uses).
7. **Wire the tripwire** — monitoring that watches for signals the deployed model is crossing the boundary (post-deployment abuse signals, external red-team findings, new elicitation-methodology results).

Everything after this module in the track — dangerous-capability evaluation engineering (mod-106), guardrail engineering (mod-108), safety-case authoring (mod-109), automated red-teaming (mod-111), and safety-program authoring (mod-112) — is *this loop* at more concrete depth.

## An engineering-artifact map for RSP

For each ASL you cover, this is the minimum artifact set you should be able to name:

| Artifact | Purpose | Where authored |
|---|---|---|
| Capability-eval report per tier | Documents elicitation protocol, thresholds, results, tier decision. | mod-106 depth. |
| Deployment-safeguard checklist | Named safeguards for the tier, with owner, evidence, cadence. | mod-107 (containment) + mod-108 (guardrails). |
| Security-safeguard checklist | Weights / infra security safeguards. | Defers to `ai-infra-security` (level 35) for platform depth. |
| Tripwire configuration | Signal → threshold → response mapping in monitoring. | mod-108 (monitor sidecars) + mod-112 (program). |
| Rollback contract | What "roll back" concretely means: which model version, which knob, which deployment tier. | mod-112. |
| Safety case | GSN-shaped argument that the model at this tier meets the RSP commitments. | mod-109. |
| System-card entry | Public disclosure shaped by the tier decision. | mod-112. |

## Common misreadings to avoid

- **Confusing ASL with model-capability score.** ASL is a governance tier; the model's capability on any given benchmark is *evidence* for a tier decision, not the tier itself.
- **Treating thresholds as post-hoc labels.** Pre-registration is load-bearing. If the threshold moves after the eval runs, the framework's discipline is lost.
- **Ignoring the elicitation gap.** A "we saw pass rate P" statement without an elicitation protocol description is meaningless — the model may be sandbagging or you may be under-eliciting.
- **Treating deployment safeguards as the whole story.** Security safeguards (weights protection) are equally load-bearing — a leaked ASL-3-tier weight is a policy failure even if the deployment surface is well-guarded.

## Summary

- The RSP defines AI Safety Levels (ASL-1 → ASL-4+), each with a capability threshold and required deployment + security safeguards.
- A safety engineer reads the RSP as an *evaluation contract*: for each tier, name the elicitation protocol, the pre-registered threshold, the tripwire, the rollback, and the evidence artifact.
- The Responsible Scaling Officer and internal governance bodies review artifacts; your job is to author them defensibly.
- Every subsequent module in this track (dangerous-capability elicitation, containment, guardrails, safety cases, program) is the RSP's shape at more concrete depth.
