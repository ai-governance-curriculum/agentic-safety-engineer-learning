# 02 — RSP / Preparedness / FSF Tier Contribution

## Motivation

The tier decision is the load-bearing decision of a frontier-safety program. Everything else — the safety case, the system card, the AISI submission, the deployment mitigations, the rollback plan — depends on it. Deploy at ASL-3 with ASL-4 controls; defer deployment because a Preparedness scorecard entry is *high*; engage the FSF's next mitigation tier because a CCL was crossed. The tier decision is the *policy-level output* of an evaluation programme that mod-106 through mod-111 fed with evidence.

The tier decision is not made by the safety-engineering role. It is made by the Responsible-Scaling Officer, the Preparedness Committee, or the Safety Board equivalent (chapter 01). The safety-engineering role *contributes* to it — by producing the evidence, defending the elicitation methodology, naming the pre-registered thresholds, drafting the rollback contract, and shaping the deferred-deployment recommendation into a memo the decision body can actually act on.

This chapter names what "contribute to a tier decision" means in practice. It walks the four load-bearing inputs — **pre-registered thresholds**, **elicitation-methodology defence**, **rollback contract**, **deferred-deployment recommendation** — and pins the memo shape that carries them. The memo is a genre. A senior safety-engineer produces a version of it several times a year, for each model family and each material capability trajectory. Getting the shape right is what makes the decision surface trustworthy.

## Primary sources

- **[Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp)** — the ASL framing, the pre-registered evaluation thresholds per level, and the RSO authority. <!-- needs-research: pin the current published RSP version (Anthropic revises regularly) and the specific thresholds each ASL is registered against. -->
- **[OpenAI Preparedness Framework](https://openai.com/safety/preparedness)** — the scorecard framing, the risk categories, and the pre-committed mitigation posture per grading. <!-- needs-research: pin the current Preparedness Framework version and any implementation-notes publications. -->
- **[Google DeepMind Frontier Safety Framework](https://deepmind.google)** — the CCL framing, the mitigation tiers, and the periodic-review commitment. <!-- needs-research: verify the current FSF publication URL and version. -->
- **[METR — Autonomy Evaluations](https://metr.org/)** — one of the most-cited third-party autonomy-elicitation methodologies; a common evidence input to Preparedness scorecard and RSP threshold determinations. <!-- needs-research: pin specific METR publications the memo cites. -->
- **[UK AI Safety Institute — Evaluations Publications](https://www.aisi.gov.uk/)** and **[US AI Safety Institute (NIST) — Publications](https://www.nist.gov/aisi)** — the pre-deployment evaluation reports and methodology notes that inform tier decisions. <!-- needs-research: pin specific AISI publications, particularly the pre-deployment evaluation reports on frontier models. -->

Version-pin all of these in the memo's references. The frameworks revise, the AISI reports accrete, and mis-cited versions are the reviewer's first catch.

## What the safety-engineer contributes — the four load-bearing inputs

The tier-decision body reads a *memo*. The memo has four sections that carry the safety-engineering role's contribution.

### Contribution 1 — Pre-registered thresholds

*What it is.* A specific, numeric, checkable claim registered *before* the evaluation is run, of the form: *"if the model's benchmark X score exceeds Y in the elicitation configuration Z, the model is at least at tier T."* The pre-registration binds the organisation to the threshold's meaning; the evaluation runs; the threshold is either crossed or not; the decision follows.

*Why it matters.* Without pre-registration, every threshold-cross becomes a negotiated event: was the evaluation configured correctly, was the elicitation aggressive enough, does the benchmark measure what we thought, etc. Pre-registration moves that negotiation *before* the result is known, when incentives are neutral. It is the discipline that makes the RSP's ASL determinations and the Preparedness scorecard's ratings function as *decision surfaces* rather than *post-hoc rationalisations*.

*Engineering shape.*

- **Named benchmark, named configuration, named threshold.** "On the [named benchmark] harness at version [X], with elicitation configuration [Y] (prompt template hash [H], tool set [T], budget [B]), a passing score exceeding [Z] triggers the next-tier consideration." Every noun is a specific artefact with a version pin.
- **Elicitation configuration is a load-bearing part of the threshold.** A score of 0.6 with weak elicitation is a different signal from a score of 0.6 with mod-106-shape aggressive elicitation. Pre-registration names the elicitation configuration explicitly.
- **Confidence interval, not point estimate.** The threshold names the point *and* the interval width the evaluation must achieve for the point to be actionable. Wider intervals require re-running with more samples.
- **Cross-framework alignment.** If the organisation operates under RSP but discloses to regulators against the Preparedness scorecard, the pre-registered threshold maps to both vocabularies. Chapter 01's cross-framework mapping is where this alignment lives.

*Common failure the reviewer catches.* The threshold is stated as *"the model shows dangerous capability in [domain]"* without a numeric surface or an elicitation configuration. A reviewer at this level asks *"what would falsify this?"* — and if the answer is *"nothing specific,"* the threshold is not pre-registered; it is a mood.

### Contribution 2 — Elicitation-methodology defence

*What it is.* An argument, backed by mod-106's DCER, that the elicitation used to produce the tier-relevant evidence is *strong enough to be load-bearing*. This includes the fine-tuning-on-domain-data elicitation, the tool-augmented elicitation, the multi-agent decomposition elicitation, and the expert-in-the-loop elicitation that mod-106 chapter 03 pins as the four load-bearing methodologies.

*Why it matters.* A tier decision that under-estimates capability because the elicitation was weak is *worse* than no tier decision at all: it discloses "we checked, the model is fine" when the actual finding is "we didn't check hard enough." The methodology defence is what protects the tier decision from that failure mode.

*Engineering shape.*

- **Explicit elicitation-methodology list.** The four (or more) elicitation regimes run, per capability domain, with the *strength* of each named. Fine-tuning-on-domain-data is a strong regime; a plain-prompt evaluation is a weak regime. Both may run; the decision consumes the strong result.
- **Elicitation-gap argument.** For each capability, a claim of the form: *"we believe the current elicitation is within [gap] of the strongest attainable elicitation by a determined adversary with [resources]."* The gap is what the tier decision has to reason about. Small gap → the decision consumes the elicited score directly. Large gap → the decision consumes the elicited score plus a *gap uplift*.
- **Third-party corroboration where available.** Independent evaluations by METR, UK AISI, US AISI, or Frontier Model Forum peers strengthen the methodology defence. The memo cites which independent evaluations corroborate which threshold.
- **Failed-elicitation disclosure.** Elicitation regimes that were attempted and did *not* uplift the model's score are still evidence. Disclose them; a reviewer will otherwise ask *"did you try [X]?"*.

*Common failure the reviewer catches.* The memo reports the strongest score as the tier-relevant score, without disclosing which elicitation produced it or how the elicitation compares to a determined adversary's plausible upper-bound. A reviewer at this level asks for the *methodology-gap argument*; a memo without one under-estimates the tier.

### Contribution 3 — Rollback contract

*What it is.* A pre-committed, engineered plan for what the organisation does *if the tier-decision has to be revised downward after deployment*. What gets rolled back, how fast, by whom, with what user-facing shape.

*Why it matters.* A tier decision is a bet on the model's behaviour in deployment. If the bet turns out wrong — the model's autonomy is higher than the DCER measured, the containment is weaker than mod-107 argued, the monitors (mod-108) fire at ten times the predicted rate — the organisation must be able to unwind the deployment on a timeline the tier body committed to. The rollback contract is the engineered version of that commitment.

*Engineering shape.*

- **Rollback surface enumeration.** For each deployment channel (API, product, partner integration), what specifically rolls back: model version, tool inventory, capability gate configuration (mod-107 EACC), system-prompt, memory posture. Named per surface.
- **Rollback latency budget.** Time from decision to rollback-live: minutes for API model-version-pin swaps, hours for tool-inventory changes, days for partner-integration model-version renegotiation. Pinned per surface.
- **Rollback authority.** Who is authorised to initiate a rollback. Almost always the RSO (or equivalent) *plus* a nominated on-call safety-engineer with pre-authorised authority for time-sensitive fires (chapter 05's incident-response scope).
- **User-facing shape.** What users see when a rollback fires: a service-degradation notice, a capability-availability change, an outage. Communications-team preparation is part of the contract.
- **Rollback drill cadence.** The contract is drilled at least quarterly on the pre-production stack, with drill logs retained. An untried rollback is a rollback that will not work under pressure.

*Common failure the reviewer catches.* The rollback contract says "the model can be rolled back" without specifying the surface, latency, authority, or drill cadence. A reviewer at this level asks for the *most-recent drill log*. If the answer is *"we haven't drilled it,"* the contract is aspirational.

### Contribution 4 — Deferred-deployment recommendation

*What it is.* Where the safety-engineering role's evidence and analysis support *not deploying* — deferring, narrowing, or gating deployment behind additional mitigation — the memo carries an explicit deferred-deployment recommendation with the reasoning. This is the discipline that keeps the memo honest.

*Why it matters.* A memo that carries only *deploy* recommendations is a memo the decision body cannot use as a decision surface — it is a recommendation to rubber-stamp. A memo that discloses *when to defer* is a memo the decision body can act on. The safety-engineering role earns the decision body's trust over time by being willing to write *defer* when the evidence supports it.

*Engineering shape.*

- **Named deferred-deployment shape.** Full deferral, narrow-launch (specific customers, specific regions, specific tools), capability-gated launch (mod-107 EACC narrower than production), or monitored-launch (mod-108 posture heavier than steady-state). Choose the narrowest that satisfies the evidence.
- **Deferral trigger.** The specific finding that motivates the deferral. Not *"we are uncertain,"* but *"the elicitation methodology defence for domain [X] has a gap of [size], and the tier-boundary is within that gap."*
- **Deferral exit criterion.** What evidence, produced by whom, on what timeline, would let the recommendation flip from defer to deploy. Named. Without an exit criterion, a deferral is indefinite and the organisation drifts.
- **Cost frame.** The memo names the cost of deferral (launch-slip, competitive position, contractual commitments) and the cost of the alternative outcome (a tier-band under-estimate). The reviewer is a decision-maker; a decision-maker weighs both.

*Common failure the reviewer catches.* The memo carries a deferred-deployment recommendation with no exit criterion. A reviewer at this level asks *"what would let us launch?"* — and if the answer is *"we'll re-evaluate,"* the recommendation is a stall, not a decision surface.

## The tier-decision memo — the load-bearing artefact

The memo is a genre. A good memo is 8–15 pages, is read cover-to-cover by the tier body in a single sitting, and enables a single decision. Its structure:

1. **Executive summary** — one page. The tier claim (RSP ASL / Preparedness scorecard / FSF CCL), the deploy / defer / gated-launch recommendation, the two or three most load-bearing evidence citations.
2. **Model and scope** — the model version, the intended deployment surfaces, the sibling models this memo compares against.
3. **Pre-registered thresholds and evidence** — the thresholds pre-registered before the eval, the eval results, the crossings.
4. **Elicitation-methodology defence** — the four regimes run, the methodology-gap argument, the third-party corroboration.
5. **Deployment mitigations engaged** — the mod-107 EACC posture, the mod-108 monitoring posture, the human-in-the-loop escalation flow — in the shape the tier decision consumes.
6. **Rollback contract** — the rollback surface, latency, authority, drill cadence.
7. **Deferred-deployment recommendation** (if applicable) — with the exit criterion.
8. **Residual-risk statement** — what the memo does *not* claim to have ruled out. This is the safety case's honesty section (mod-109 chapter 03 pattern).
9. **References** — version-pinned to primary sources.

The memo is signed by the safety-engineering lead who authored it, co-signed by the safety-engineering leadership, and delivered to the tier-decision body ahead of the meeting with enough lead-time (5 working days is a defensible floor) for the body to actually read it.

The tier body's decision is a *response* to the memo, minuted, and appended to the memo as an addendum. The addendum records the decision, the vote (where applicable), the conditions attached, and the tier-decision's effective date. The memo-plus-addendum is what mod-109's safety case cites and what chapter 03's system card discloses.

## Interfaces

- **Chapter 01 (program).** The tier-decision memo is one of the artefact-register rows in the FSPC. Its cadence keys off the pre-deployment safety-eval touch-point.
- **Chapter 03 (system cards).** The system card discloses the tier claim, the elicitation-methodology defence summary, and the deployment mitigations engaged. It does not disclose the full residual-risk statement (audience separation — chapter 03).
- **Chapter 04 (regulator disclosure).** The AISI submission and the Article 55 obligations documentation reference the tier decision.
- **Chapter 05 (incident response).** A post-deployment finding that would revise the tier claim downward triggers the incident-response flow, which drives a rollback (using the rollback contract) and an updated memo.
- **mod-106 (DCER).** The DCER is the primary evidence input for the pre-registered-thresholds and elicitation-methodology-defence sections.
- **mod-107 (EACC).** The EACC posture is what the deployment-mitigations section cites.
- **mod-108 (monitors).** The predicted monitor-fire rates in the memo are what the post-deployment cadence (touch-point 4) compares against.
- **mod-109 (safety cases).** The tier-decision memo and the safety case are companion artefacts; the safety case *argues* the tier, the memo *supports the decision to accept the tier*.
- **mod-110 (control / deception).** Deception-detection findings enter the residual-risk statement.
- **mod-111 (automated red team).** Red-team findings that survive triage enter the residual-risk statement and, when they cross a pre-registered threshold, the deferred-deployment recommendation.

## Common misreadings to avoid

- **"The safety-engineer makes the tier decision."** No. The safety-engineer *contributes* — thresholds, methodology defence, rollback contract, recommendation. The RSO, Preparedness Committee, or Safety Board equivalent *decides*. The memo is the safety-engineer's contribution; the addendum is the decision.
- **"Pre-registered thresholds slow us down."** They protect the decision surface from post-hoc rationalisation. An organisation that finds pre-registration inconvenient is discovering that its existing thresholds were not actually decision surfaces.
- **"Elicitation methodology is mod-106's concern; the memo just cites the score."** No. The memo defends the methodology, because a memo that cites a weakly-elicited score without disclosing the elicitation weakness under-estimates the tier. mod-106 owns the methodology; the memo defends it.
- **"The rollback contract is a legal document."** It is an engineered document. Legal reviews it. Communications co-authors the user-facing shape. But the engineered rollback surface, latency budget, and drill cadence are the safety-engineer's craft.
- **"A defer recommendation is a career-limiting move."** No. A memo that never defers is a memo the tier body cannot trust as a decision surface. The safety-engineering role earns the tier body's trust by being willing to write *defer* when the evidence supports it; over time, the memos that do carry *deploy* recommendations become more actionable because the tier body knows the author would say *defer* if the evidence went the other way.
- **"The memo can be short; the decision body is senior."** The memo can be *concise*. It cannot be *incomplete*. The four load-bearing inputs — thresholds, methodology defence, rollback contract, recommendation — must all appear. Concise means each section is tight, not that a section is missing.

## Summary

- The safety-engineer *contributes* to the tier decision; the decision is made by the RSO / Preparedness Committee / Safety Board equivalent.
- The contribution has four load-bearing components — **pre-registered thresholds**, **elicitation-methodology defence**, **rollback contract**, and **deferred-deployment recommendation**.
- The load-bearing artefact is the **tier-decision memo** — 8–15 pages, nine sections, signed by the safety-engineering lead, delivered 5+ working days ahead of the tier body's meeting.
- The memo composes: mod-106 evidence, mod-107 containment posture, mod-108 monitoring posture, mod-109 safety-case argument, mod-110 residual-risk findings, mod-111 red-team findings.
- Pre-registration of thresholds and honest deferred-deployment recommendations are what make the memo a decision surface rather than a rubber-stamp.
- Exercise 02 asks you to draft a tier-decision memo for a specific model version. Chapter 03 details the disclosure the memo drives.
