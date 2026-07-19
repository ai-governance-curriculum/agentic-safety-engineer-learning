# 05 — Safety-Case Pitfalls and Rebuttal Handling

## Motivation

The four previous chapters authored a case. This one breaks it. Every safety case a level-40 safety engineer authors will be attacked — by an internal review body looking for gaps, by AISI or a peer external evaluator looking for the *"we could not verify"* finding, by a regulator looking for a citable specific claim to challenge, by a future incident-response investigation looking for what should have been caught in review. The engineering craft at this level is *anticipating the attacks and pre-empting them*, and — when a real reviewer's counter-argument does land — routing the finding back into evidence work upstream in a bounded, addressable way.

This chapter is a catalogue. Each pitfall is named, its failure mode described, its detection signature laid out, and its remediation routed back to the specific earlier module the finding demands work from. The chapter closes with the rebuttal-handling pattern — what a reviewer's counter-argument actually looks like, and how the case responds *without collapsing*.

The posture matters. A safety case is not a marketing document. Findings against it are how the case *improves*. The engineer's job is not to defend the case emotionally; it is to *route the finding correctly* and let the evidence portfolio, the argument structure, or the deployment scope adjust.

## Primary sources

- **[Clymer et al. §5 — "Common failure modes"](https://arxiv.org/abs/2403.10462)** — the paper's own catalogue of the ways safety cases fail. Read it as the working baseline this chapter extends.
- **[Bloomfield and Bishop — "Safety and Assurance Cases: Past, Present, and Possible Future"](https://openaccess.city.ac.uk/id/eprint/463/)** <!-- needs-research: confirm the canonical citation and URL for the Bloomfield and Bishop review of assurance-case practice; a City University London institutional-repository copy is the expected form. --> — the standing critique of assurance-case pitfalls from the broader assurance-case community, applicable at direct-import to frontier-AI cases.
- **[Ministry of Defence UK — "The Sellafield Safety Case Guide" (or equivalent operational guide)](https://scsc.uk/gsn)** <!-- needs-research: identify a canonical operational-safety-case guide from a mature safety-critical industry (rail, nuclear, aerospace) whose failure catalogue this chapter cross-references. --> — an operational-industry precedent for authored-then-attacked case practice.
- **[UK AI Safety Institute — pre-deployment methodology](https://www.aisi.gov.uk/)** <!-- needs-research: pin the AISI publication that names "could not verify" as a finding class. --> — where an external evaluator's *"could not verify"* finding actually appears, as a citable rebuttal shape.
- **[EU AI Act Article 55 / Article 56 GPAI provisions](https://artificialintelligenceact.eu/)** <!-- needs-research: pin the current authoritative URL for the EU AI Act text, and the specific Article 55 / 56 GPAI provisions the case's disclosure-facing claims are attacked against. --> — the regulator-facing rebuttal surface for a deployment in EU scope.

## Pitfall 1 — Evidence-provenance gaps

### What it is

A solution node cites a piece of evidence, but the provenance chain — *who ran the eval, when, against what model fingerprint, under what protocol, with what elicitation-gap accounting, graded by whom* — is incomplete, inconsistent, or non-reconstructible.

### Failure signature

- The DCER cited in `G-INABILITY-BIO-CHEM`'s solution names a model version that does not match the deployment's model fingerprint. Version drift silently invalidates the leg.
- The red-team suite result cited in `G-CONTROL-TOOL-ABUSE`'s solution is a *point-in-time snapshot* with no git tag, and the suite has been mutated since. The reviewer cannot reproduce the number.
- The elicitation-gap δ in a DCER is a *"we did our best"* qualitative statement rather than a bounded quantitative claim. mod-106 chapter n's methodology was not applied.
- The grader for CBRN uplift is *"internal safety team"* without a domain-expert credential list. The Deference leg's independence is thin.

### How a reviewer catches it

The reviewer asks for the git tag of the red-team suite, the weights hash of the model at the time of the eval, the composition of the grader panel, and the pre-registered elicitation protocol. Any missing answer is a *"could not verify"* finding routed to the specific leg.

### Remediation route

- **Back to mod-104 / 105.** Version-pin the red-team suite; publish the pinned tag in the case's solution node.
- **Back to mod-106.** Publish the DCER against a specific model-fingerprint; publish the elicitation-gap protocol formally with δ-bound claims; publish the grader panel roster.
- **Back to mod-107 chapter 03.** The tamper-evident audit log provides the runtime-enforcement provenance; cite the Merkle-root anchor, not an operational-observability sink.
- **In this module.** Add a *provenance-completeness check* to the authoring workflow (chapter 04 step 7's break-the-argument pass); every solution's provenance chain must be reconstructible before sign-off.

### Why it repeats

Provenance is boring; engineering teams forget it. The remediation is *cheap once done*, but the first-time cost is high. Bake it into every mod-104 / 105 / 106 pipeline output so that the safety case never has to retro-fit it.

## Pitfall 2 — Argument-completeness gaps

### What it is

The case's decomposition leaves *implicit* legs that are load-bearing for the top claim but never made explicit as goals in the GSN outline. Or explicit goals are left undeveloped without a closure plan and shipped anyway.

### Failure signature

- The top strategy `S-TOP` decomposes by argument shape but omits Trustworthiness under the reasoning *"we did not want to overclaim,"* and then the Control leg silently assumes the model does not deliberately evade oversight. The Trustworthiness leg was load-bearing all along; it was hidden inside a Control assumption.
- A hazard from mod-102 is not decomposed into any sub-goal — the case does not cover H-4 (bugtracker manipulation) because *"it did not seem material,"* but the deployment scope admits it.
- Undeveloped goals are numerous, no closure plan attaches, and the case ships. The undeveloped markers are correctly drawn; the intent behind them is not.
- The lifecycle module ships without a rollback contract goal, because rollback was assumed at the framework level and never authored into the case.

### How a reviewer catches it

The reviewer cross-references the hazard list from the deployment's threat model against the top claim's decomposition and finds a hazard with no addressing sub-goal. Or the reviewer counts undeveloped-goal markers and asks for the closure plan; there is none.

### Remediation route

- **Back to mod-102.** Every hazard in the threat model maps to at least one sub-goal in the case, or the case explicitly scopes the hazard out (with a justification).
- **Back to chapter 04 step 5.** Every undeveloped goal carries a closure plan naming the module, the sprint, or the accepted-residual-risk classification. This is a pre-sign-off check.
- **Back to chapter 03.** When Trustworthiness is dropped from the shape decomposition, the case *justifies* the drop (the model is treated as adversarial under a Control-only argument, per Redwood-style methodology) rather than silently omitting it.

### Why it repeats

Engineers under-estimate how much of the argument they carry in their head. If it is not on the diagram / in the outline, the reviewer does not know it. The workflow's step 6 assumption-and-context capture is the discipline that closes this.

## Pitfall 3 — Elicitation-gap ambiguity

### What it is

The Inability leg (chapter 03) rests on mod-106's claim that the elicitation ceiling bounds the reachable capability. The claim carries an accounted gap δ. When δ is unbounded, poorly bounded, or defined relative to a threat model narrower than mod-102's, the Inability argument inherits the ambiguity and cannot survive a competent review.

### Failure signature

- The DCER's δ is stated as *"we ran best-of-16 with two scaffold variants and estimated a gap of ~2×,"* with no adversarial-fine-tuning elicitation, no external red-team elicitation, and no fine-tune-elicitation-under-safety-tuning-removal comparison. δ is under-bounded.
- The elicitation protocol did not run against the *deployed stack* (fine-tuning API, plug-in surface, agent scaffolding). The reachable capability under deployment may exceed the elicited ceiling.
- The Inability claim's threshold is stated in terms of a metric (accuracy on a benchmark) that does not correspond to the framework's harm threshold (uplift over a control population). The elicited number and the tier threshold are in different units.
- mod-110's control-eval evidence — which would surface adversarial-elicitation *the model itself performs* — is missing from the Inability leg's supporting evidence.

### How a reviewer catches it

The reviewer asks *"how do you know your best-of-N is anywhere near the reachable ceiling?"* and *"what happens if we adversarially fine-tune the model to expose the capability?"* Both are load-bearing questions mod-106's DCER must answer; if the DCER does not, the Inability leg is under-defended.

### Remediation route

- **Back to mod-106.** Widen the elicitation protocol: adversarial fine-tuning under safety-tuning removal, external red-team elicitation panel, tool-use-elicitation across the deployment's tool inventory, scaffolding elicitation. Republish the DCER with a bounded δ and an explicit *"this δ is valid under X threat model"* qualifier.
- **Back to mod-110.** Add a control-eval leg that stress-tests the Inability claim against an adversarially aligned model that *tries* to exceed the elicited ceiling.
- **Back to mod-101.** Ensure the tier threshold is stated in the same units the eval measures. If the RSP / Preparedness / FSF threshold is stated in uplift and the eval measures accuracy, translate carefully or note the mismatch.
- **In this module.** Move the Inability leg out of load-bearing position and pair with Control (chapter 03's composition rules). Do not lean Inability at high tiers.

### Why it repeats

Elicitation-gap accounting is genuinely hard. The frontier does not yet have a fully defensible methodology; mod-106 approximates it. The safety case is honest about the approximation — the pitfall is *not* the gap; it is the *pretence* that the gap is smaller than the methodology supports.

## Pitfall 4 — Moving-target thresholds

### What it is

The case's top claim references a threshold — an RSP ASL bound, a Preparedness scorecard level, an FSF Critical Capability Level — and during the review period, the framework's threshold shifts under it. The case was signed against a threshold that no longer holds.

### Failure signature

- The RSP is updated during the review period to tighten ASL-3 uplift bounds. The case's top claim referenced the *old* bound; the reviewer at the mid-period check asks for the new bound; the leg does not close.
- The Preparedness Framework adds a new scorecard category (e.g., a new autonomy sub-category) that the case did not decompose. The category was not in scope at signing; it is now.
- The FSF's CCL definitions are refined by DeepMind; the case's threshold statement no longer matches the refined definition.
- The EU AI Act's Article 55 GPAI systemic-risk criteria are amended by delegated act. The case's disclosure-shape assumptions are stale.

### How a reviewer catches it

The reviewer cross-references the case's threshold citation against the current framework version and finds a version mismatch. Or an incident-response investigation retrospectively finds the case referenced a bound weaker than the framework's current bound.

### Remediation route

- **Back to mod-101.** Every framework citation in the case carries a *version-pinned* citation and a *review-period-scoped* validity statement. If the framework updates, the case's `C-FRAMEWORK-CONTEXT` context flags a *"changes under review"* state, and the affected legs are re-authored.
- **Back to chapter 04 step 1.** The scope's `C-REVIEW-PERIOD` is tight enough that framework drift is caught by the case's own expiry, not by an incident.
- **Back to chapter 04 step 7's iteration.** The case is re-signed on framework drift, not silently carried.

### Why it repeats

Frameworks update. RSP, Preparedness, and FSF have all been revised since their initial publication; further revisions are certain. A case that was defensible at signing can become under-defended by framework drift. The remediation is not to *predict* the drift but to *design for it* — pin the citation, tighten the period, plan the re-authoring cadence.

## Pitfall 5 — Rebuttal-handling gaps

### What it is

The case does not anticipate the counter-arguments a reviewer will bring. When the counter-argument lands, the case has no rehearsed response, and the leg collapses under pressure — either falsely (the leg was actually fine, but the response was flat-footed) or truly (the leg was hollow, and the counter-argument revealed it).

### Failure signature

- The reviewer challenges the monitor-coverage claim: *"your adaptive-attack survival curve is over a synthetic corpus; how do you know real-world attacks look like your synthetic ones?"* The case's response is *"we followed the mod-108 methodology,"* which does not engage the specific counter-argument.
- The reviewer challenges the Trustworthiness leg: *"your alignment-faking test used the prompt template from the Anthropic paper; that test does not generalise to your deployment's tool-use context."* The case's response is a hand-wave.
- AISI's pre-deployment report notes *"we could not verify the elicitation-gap protocol was applied uniformly across risk classes."* The case has no rehearsed response for this specific finding.

### How a reviewer catches it

The reviewer *is* the counter-argument. Rebuttal-handling is not caught after the fact; it is caught *in the room* when the counter-argument lands.

### Remediation route

- **Back to chapter 04 step 7.** The break-the-argument iteration pre-writes the counter-arguments and the case's responses. Every load-bearing leg has a *"a reviewer will challenge X; the response is Y"* section in the assumption capture.
- **Back to mod-112.** The disclosure engineering for AISI, EU AI Office, and regulator-facing audiences pre-shapes the response to *"could not verify"* findings. mod-112 owns the disclosure surface; mod-109 pre-writes the response.
- **In this chapter.** Adopt a rebuttal register — a running list of the counter-arguments the case has anticipated and the responses. The register is a section of the case, not an off-side document. If the register is empty, the rebuttal-handling pass did not happen.

### Why it repeats

Engineers under-rehearse the adversarial reading. The break-the-argument pass in chapter 04 step 7 is where the register is populated. When the pass is skipped, the case ships without a rebuttal register and the reviewer's counter-argument lands cold.

## The rebuttal-handling pattern

When a reviewer's counter-argument does land in the review room, the case's response follows a repeatable pattern. It has four moves:

1. **Locate the finding on the case.** Which goal, which strategy, which solution, which assumption. If the finding is against *"the case,"* the case is under-modularised — re-route the finding to the specific leg.
2. **Classify the finding.** Which pitfall class from the catalogue above? Evidence-provenance, argument-completeness, elicitation-gap, moving-target, or rebuttal-handling?
3. **Respond with the rehearsed answer** if the case anticipated the finding, or **acknowledge and route** if it did not. Acknowledgement is not weakness — it is the workflow. *"We did not anticipate that challenge; we agree it is load-bearing; we will route it back to mod-106 for a δ-bound update; the case's next signing pass will address it"* is a defensible response.
4. **Update the case.** The response is not verbal-only. The GSN outline changes: a new sub-goal is added, an assumption is tightened, a solution is re-cited, an undeveloped-goal marker is added with a closure plan. The reviewer sees the update; the version bumps; the case remains a *contract* rather than a conversation.

Two failure modes to avoid in the rebuttal-handling posture:

- **Emotional defence.** The case is not the author's identity. Defending an under-defended leg is worse than acknowledging the finding and routing it. The AISI *"could not verify"* pattern is deliberately anti-adversarial — the appropriate response is engagement, not defence.
- **Silent concession.** Conceding a finding without updating the case is worse than defending it. The concession must land on the artefact.

## How each pitfall drives red-team demand back at earlier modules

The pitfalls are not local to this module. Each one is a *demand signal* on the module whose evidence the case cites. Naming the demand explicitly:

- **Evidence-provenance gaps** → demand version-pinned, hash-anchored, protocol-documented evidence from **mod-104 / 105 / 106 / 107 / 108 / 110**. Every eval pipeline must emit its own provenance chain; the safety case cites it, but does not synthesise it retroactively.
- **Argument-completeness gaps** → demand hazard-map completeness from **mod-102**. Every threat-model hazard maps to a case leg or an explicit scope-out; a hazard without a leg is a mod-102 finding routed through this module.
- **Elicitation-gap ambiguity** → demand a tighter δ-bound protocol from **mod-106** and a stronger adversarially aligned control eval from **mod-110**. The safety case's Inability leg is only as tight as mod-106's δ; make it tight upstream.
- **Moving-target thresholds** → demand framework-version-pinning from **mod-101** and lifecycle-cadence design from **mod-112**. Case-authoring cadence is set by framework drift as much as by deployment drift.
- **Rebuttal-handling gaps** → demand adversarial-review rehearsal from this module's authoring workflow (chapter 04 step 7) and disclosure engineering from **mod-112**. The rebuttal register is an artefact of both.

The safety engineer's downstream posture is *"the case revealed this,"* not *"the case failed at this."* The findings are the case's *product*.

## Interfaces

- **Chapter 04 (authoring).** The pitfalls are what step 7's iteration catches; the rebuttal-handling pattern is what step 7 rehearses.
- **Chapter 06 (boundaries).** Findings that route to peer roles — `ai-evaluation-engineer` for release-assurance evidence gaps, `senior-ai-governance-architect` for control-library gaps — are handed off through the boundary chapter names.
- **mod-101 (frameworks).** Framework version-pinning; moving-target-threshold remediation.
- **mod-102 (threat modelling).** Hazard-map completeness; argument-completeness-gap remediation.
- **mod-104 / 105 (jailbreak / agent attack).** Evidence-provenance discipline for red-team suites.
- **mod-106 (DCER).** Elicitation-gap bound; the load-bearing artefact for Inability leg defensibility.
- **mod-107 (EACC).** Runtime-enforcement provenance; tamper-evident-log-anchored evidence for Control leg.
- **mod-108 (monitors).** Monitor-coverage adaptive-attack survival; the load-bearing artefact for Control leg's monitor sub-claim.
- **mod-110 (control / deception).** Adversarially aligned control-eval; the load-bearing artefact for Control × Trustworthiness composition.
- **mod-112 (disclosure).** Disclosure surface for *"could not verify"* findings; rebuttal-handling shaping for external audiences.

## Common misreadings to avoid

- **"Pitfalls are bugs in the case; a well-authored case has none."** No. A mature case has *fewer* pitfalls, not zero. The catalogue names the *classes* of failure the case must be pre-audited against; each authoring pass reduces the incidence, but the underlying methodology gaps (elicitation-gap, evolving frameworks, imperfect monitor calibration) mean some residual is present. The case *acknowledges* residuals.
- **"A `could not verify` finding is a rejection."** No. AISI's methodology names *"could not verify"* as a specific finding class that routes to specific follow-on work. It is not a rejection of the case; it is a routed finding. The rebuttal-handling pattern responds to it constructively.
- **"Rebuttal-handling is a communication skill, not an engineering one."** No. Rebuttal-handling is *engineering the argument's structure so that a counter-argument lands on a specific leg with a specific response*. That is a design property of the case, not a communication property of the author.
- **"Threshold-drift is an operational concern; it does not affect the case."** No. Threshold-drift *invalidates* the case's top-claim citation. A case whose top-claim references a stale threshold is not defensible; the review-period expiry and the re-authoring cadence are the design responses.
- **"Pitfalls are for junior authors."** No. The catalogue is what senior authors pre-audit against; the difference between a level-40 authored case and a junior authored case is that the level-40 case has been pre-audited *before* the review, not *during* it.
- **"Findings against the case are findings against the safety engineer."** No. Findings against the case are the case's *product*. The engineer's job is to route them, not to own them personally. Route findings to the module whose evidence they demand better provenance / tighter δ / wider coverage from.

## Summary

- Five load-bearing pitfall classes: **evidence-provenance gaps**, **argument-completeness gaps**, **elicitation-gap ambiguity**, **moving-target thresholds**, **rebuttal-handling gaps**.
- Each pitfall has a **detection signature** the reviewer will use to find it and a **remediation route** back to the specific earlier module the fix demands work from.
- The **rebuttal-handling pattern** is a four-move discipline: *locate on the case, classify against the catalogue, respond (rehearsed or acknowledge-and-route), update the artefact*. Rebuttal-handling is an engineering property of the case's structure, not a communication property of the author.
- Each pitfall is a **demand signal** on an upstream module. mod-102 gets hazard-map completeness demands; mod-104–108 get provenance demands; mod-106 gets δ-bound demands; mod-110 gets adversarial-alignment demands; mod-101 gets framework-version-pinning demands; mod-112 gets disclosure-surface demands.
- The engineer's posture is **findings are the product**. Route them, do not own them personally. Acknowledged findings that route into follow-on work are the case *doing its job*.
- Exercise 04 (rebuttal-and-evidence-provenance review) and exercise 05 (pitfall catalogue) drill this chapter's material against a specific safety-case artefact.
