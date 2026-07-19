# 01 — Safety Cases as the Frontier Argumentation Artefact

## Motivation

Modules 101 through 108 built a portfolio of evidence: framework fluency, threat models, jailbreak fixtures, agent-attack traces, dangerous-capability elicitation reports, containment contracts, monitor dashboards. Each is a defensible artefact in isolation. None of them, on their own, answers the question a regulator, an internal safety-review body, or an external evaluator will actually ask before a frontier system ships: *given everything you know, why do you believe this deployment is acceptably safe?* The **safety case** is the artefact that answers that question.

A safety case is *not* a summary. It is not a system card, a risk register, or a slide deck of eval numbers. It is a **structured, defeasible, evidence-grounded argument** that a *specific claim* (typically: "this deployment is unlikely to cause an unacceptable harm of class C during the review period") holds under stated assumptions and against a stated threat model. Every leg of the argument — every sub-claim, every strategy, every piece of evidence, every assumption — is named, addressable, and challengeable. A reviewer's job is not to *agree* with the safety case; it is to try to *break* it. The engineering job is to author the case so that reviewer attempts to break it either fail or route back into concrete follow-on work.

The safety case is also the *composition point* for the module. mod-104's jailbreak-suite ASR, mod-105's agent-attack coverage matrix, mod-106's dangerous-capability elicitation report, mod-107's Excessive-Agency Containment Contract, mod-108's monitor fire-rate telemetry, and mod-110's control-eval results are all *citable evidence* that plugs into specific solution nodes of the case. The case is the graph; the earlier modules ship the leaves. This chapter positions that composition and names the two working templates — Clymer et al. and Goemans et al. — that the rest of the module operates on top of.

## Primary sources

- **[Clymer, Gabrieli, Krueger, and Larsen — "Safety Cases: How to Justify the Safety of Advanced AI Systems" (arXiv:2403.10462)](https://arxiv.org/abs/2403.10462)** — the load-bearing paper for this module. Introduces the four argument shapes (Inability, Control, Trustworthiness, Deference) that chapter 03 dissects. Read it front-to-back before the rest of this module.
- **[Goemans, Buhl, Schuett, Korbak, Wang, Hilton, Irving — "Safety Case Template for Frontier AI" (arXiv)](https://arxiv.org/abs/2411.08088)** <!-- needs-research: confirm arXiv identifier and canonical title for the Goemans et al. safety-case template; the number here is a best guess and must be verified against the current arXiv listing before the artefact is signed. --> — a working template for a *Control*-shaped safety case with concrete goal / strategy / solution structure. Chapter 04's authoring workflow draws directly from it.
- **[GSN Community Standard v3 (Assurance Case Working Group, `scsc.uk`)](https://scsc.uk/gsn)** — the notation the case is written in. Chapter 02 covers this in depth.
- **[Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp)** — one of the three frontier-lab framework contexts a safety case is authored *against*. The ASL tier decision is the review the case supports.
- **[OpenAI Preparedness Framework](https://openai.com/safety/preparedness)** — the second framework context. Preparedness scorecard thresholds are the *unacceptable-harm* claim shapes a case argues against.
- **[Google DeepMind Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/)** <!-- needs-research: pin the current published FSF version and its URL. --> — the third framework context. Critical Capability Level thresholds shape which claims the case must address.
- **[UK AI Safety Institute — pre-deployment evaluation methodology](https://www.aisi.gov.uk/)** <!-- needs-research: pin the specific AISI methodology publication used for pre-deployment reports; expected artefact family is the pre-deployment / early-access evaluation report series. --> — the reference posture for how an external evaluator reads a case.

Version-pin every one of these when you cite them in a safety-case artefact. A case is only as defensible as its version-pinned citations.

## What a safety case is (and is not)

A safety case has four constitutive properties. If your artefact does not have all four, it is something else — a system card, a risk register, an eval report — not a safety case.

1. **A top claim.** A single, specific, refutable proposition about the system. Not *"the system is safe"* (unrefutable) but *"during the review period, in the deployment scope defined in §1, the system will not enable a CBRN uplift event exceeding the RSP ASL-3 threshold defined in §2 with probability greater than X."* The claim carries the deployment scope, the review period, the harm class, the threshold, and the residual probability.
2. **A structured argument decomposing the claim.** A graph, in GSN or a comparable notation, that decomposes the top claim into sub-claims, each with its own decomposition, until the leaves are *solutions* — pieces of evidence — that the argument grounds itself in. Every internal node is a *strategy* naming *why* its decomposition is complete, and every branch carries *contexts* and *assumptions* that scope its validity.
3. **A cited evidence portfolio.** Every solution node references a specific external artefact: an eval report with a hash, a red-team suite with a version, a monitor dashboard with a snapshot ID, an incident-history query with a timestamp. The evidence lives *outside* the case; the case cites it.
4. **A defeasibility posture.** The case names the *counter-arguments* it has considered and the *rebuttals* it offers, and the *undeveloped goals* it acknowledges as open. A case that pretends to be complete is a case that has not thought about how it fails.

The following are *not* safety cases, though they are often confused with one:

- A **system card** describes what the system is and what evaluations were run. It does not carry a top-claim argument; it is a fact sheet.
- A **risk register** enumerates risks and mitigations. It does not decompose a top claim; it is a bookkeeping artefact.
- An **eval report** presents evidence for one capability or one behaviour. It is a *solution node* in a safety case, not the case itself.
- A **responsible-scaling policy** is the framework *within which* safety cases are authored — it defines the tier thresholds and review triggers, but a specific deployment's safety case is a distinct artefact.

The case is what a reviewer *reads*; the other artefacts are what the case *cites*.

## Why the argumentation shape matters

An engineer's instinct is to say: "here is a spreadsheet of eval numbers, they are all under the thresholds, we are safe." That instinct is wrong, and the reason it is wrong is worth internalising before chapter 03's argument-shape drill.

- **Eval numbers are pointwise.** A number without an argument does not answer *"under what assumptions is this number the right one to consult?"* It does not answer *"what would you observe that would change this number?"* It does not answer *"what does this number rule out?"* An argument makes those questions addressable.
- **Threshold decisions are compositional.** The claim *"this deployment is under the ASL-3 CBRN uplift threshold"* is not a single eval; it is a composition of an elicitation report (mod-106), a containment contract (mod-107), a monitor coverage assertion (mod-108), and a control-evaluation baseline (mod-110). The argument names the composition.
- **Reviewers are adversarial.** A reviewer is trying to falsify the case. Structured argumentation exposes exactly where a falsification would land — which sub-claim, which strategy, which solution, which assumption — and lets the reviewer's finding drive a bounded update.
- **Regulators need to *cite*.** EU AI Act Article 55, the EU GPAI Code of Practice safety chapter, and the AISI pre-deployment reports all need to point at *the specific argument leg* that a downstream disclosure is grounded in. A monolithic risk-narrative is not citable at that granularity.

Argumentation is not decoration; it is what makes the evidence portfolio into a *decision-supporting artefact*.

## The composition — where mod-104 through mod-110 land in the case

The chapter-opening framing needs to be pinned to specific solution shapes so that the module's authoring workflow (chapter 04) can compose it operationally. For a canonical *"unacceptable-harm class C avoided during review period"* claim, the earlier modules contribute the following solution nodes:

- **mod-104 (Jailbreak Engineering).** Jailbreak-suite ASR under the current safety-tuned model, benchmarked against HarmBench / JailbreakBench / AIR-Bench with StrongREJECT-shaped judges, plus in-the-wild taxonomy regression fixtures. Feeds *Inability* arguments (the model refuses the harmful ask) and *Trustworthiness* arguments (the refusal is robust under adversarial pressure).
- **mod-105 (Agent Attack Surface).** Agent-tool-abuse / memory-poisoning / plan-subversion / multi-agent coordination coverage matrices against AgentDojo + InjecAgent. Feeds *Control* arguments (the runtime prevents the agent-level failure even when the model is compromised) and *Trustworthiness* arguments (the agent does not initiate the failure).
- **mod-106 (Dangerous-Capability Evaluation).** Elicitation-gap-accounted dangerous-capability reports (CBRN, cyber-offense, autonomy, persuasion, self-exfiltration) with best-of-N ceilings and pre-registered thresholds against RSP / Preparedness / FSF tiers. Feeds *Inability* arguments (the model cannot do the harm at the required uplift level) as the load-bearing evidence.
- **mod-107 (Excessive-Agency Containment).** The EACC — the specific per-tool policy, sandbox contract, credential broker configuration, HITL contract, kill-switch configuration, tamper-evident audit-log posture — for this deployment. Feeds *Control* arguments (the runtime is narrower than the model) as the load-bearing evidence.
- **mod-108 (Guardrails and Monitors).** Fine-tuned classifier guards, Constitutional Classifier configurations, rule / flow guards, safety-monitor sidecars, and the coverage / FP-rate / latency / adaptive-attack-survival curves for each. Feeds *Control* arguments (the monitor catches the residual behaviour the containment does not prevent) and *Deference* arguments (the monitor's fire-rate is calibrated against a labelled corpus a third party can inspect).
- **mod-110 (Control and Deception).** Control-eval results (Redwood-style red-team-blue-team games under an intentionally adversarial model), deception-eval results (Apollo-style probes), alignment-faking / sandbagging / sleeper-agent test outcomes. Feeds *Control* arguments (the deployment is safe even if the model is adversarially aligned) and — crucially — *bounds* the *Trustworthiness* argument (a Trustworthiness leg that has not been stress-tested by mod-110 is not defensible at level-40 depth).

The four argument shapes and their evidence feeds are the material of chapter 03. Chapter 04 turns them into an end-to-end authoring workflow. Chapter 05 names the pitfalls that make each composition fragile, and chapter 06 draws the boundary to the peer roles who consume the case downstream.

## The pre-registered contract between developer, reviewer, and regulator

A safety case is authored *before* the deployment ships, or *before* a tier change (an ASL uplift, a Preparedness scorecard change, an FSF threshold crossing) takes effect. That timing is not incidental. The case is a *pre-registered contract* between three parties:

- **The developer** (this role, working with peer roles) commits to a claim and an evidence portfolio. Once the case is signed, the developer cannot silently update the evidence without re-review; the version bump is visible.
- **The internal safety-review body** (RSO / Preparedness Committee / Safety Board, per Anthropic RSP, OpenAI Preparedness, or DeepMind FSF conventions) accepts or rejects the case as sufficient for the tier decision under review. Rejection routes back to specific solution nodes or specific strategies.
- **The external reviewer / regulator** (AISI pre-deployment methodology, EU AI Act Article 55 / 56 disclosures, EU GPAI Code of Practice safety chapter) reads the case as the *primary source* for the tier decision. AISI pre-deployment reports call out where they *could not verify* a case's leg; that call-out is the case's rebuttal surface.

The pre-registration matters because it makes evidence-provenance gaps (chapter 05) visible. If the model changes between authoring and shipping and the evidence in a solution node no longer holds, the case is *broken by version drift* and must be re-authored. The mod-104 / 105 / 106 / 107 / 108 evidence carries hashes so that this drift is detectable, not hopeful.

## The review cycle — where a case sits in the deployment lifecycle

A safety case is not a one-shot artefact. Frontier deployments run continuously; the evidence portfolio drifts; the frameworks update; the threat model widens. The case's *review cycle* is the discipline that keeps it a live artefact rather than a fossilised approval token.

Four cycle moments matter:

- **Pre-deployment authoring.** The case is drafted, iterated, signed, and submitted before the deployment enters user-facing use. This is the moment chapter 04's workflow authors *into*. The internal-review body signs the case; peer roles co-sign the modules they own; the external evaluator (AISI, EU AI Office) reads the case for the pre-deployment engagement.
- **Canary and staged-rollout gates.** The case's Lifecycle module names the criteria for canary release, staged rollout, and general availability. Each gate re-consults the case: the criteria hold, the evidence still applies, the assumptions still bind. If any leg has degraded, the gate does not open; the case re-iterates.
- **Post-deployment monitoring.** mod-108's monitor dashboards and mod-107's tamper-evident audit-log stream produce continuous evidence. Drift in the monitor's fire rate, the containment enforcement rate, or the guardrail-survival curve routes to a *"case re-review"* trigger — the assumption behind a specific leg has weakened and the case's residual-risk claim needs re-authoring.
- **Incident response.** When an incident lands (chapter 05's rebuttal surface at its sharpest), the safety case is what the incident-response investigation reads first. *"The case claimed X; the incident violated X; which leg failed?"* The case's node IDs make this bounded; a prose-narrative case makes it impossible.

The review cycle turns the case from a document into a *control loop*. Chapter 06's boundary to `ai-evaluation-engineer` names the release-assurance platform that operates the loop.

## What the case is optimised for

Because the case sits inside a review cycle and is read by multiple audiences, it is optimised for a specific set of properties. Under-appreciating these properties is one of the most common mistakes engineers new to safety-case authoring make:

- **Legibility over completeness.** A shorter, tighter, well-structured case that a reviewer can read in an afternoon beats a longer, exhaustively-cross-referenced case that no reviewer finishes. Legibility is the argument's *ability to be adjudicated*; completeness is the argument's *ability to survive adjudication*. Both matter; when they conflict, legibility wins at first sign, completeness catches up on iteration.
- **Bounded findings over unbounded critique.** A well-structured case exposes finding surfaces at *specific* nodes — this goal, this assumption, this solution — so that a reviewer's finding is bounded to a specific remediation. An unstructured case invites unbounded critique of the whole argument.
- **Version-legibility over version-hiding.** Every citation, every framework reference, every evidence artefact carries an explicit version pin. When drift happens (and it will), the drift is *visible*, not silently absorbed.
- **Disagreement-legibility over consensus-forcing.** Where the developer and the reviewer disagree on an assumption's tightness or a threshold's location, the case *names the disagreement* rather than papering over it. The disagreement routes to a follow-on evidence request; forced consensus routes to a hidden gap.
- **Rebuttal-legibility over pre-emptive-defence.** The case does not pretend the counter-arguments do not exist. Chapter 05's rebuttal register names them explicitly, along with the case's response. A reviewer reading the register sees the case has already thought through the attack surface.

These optimisation targets shape everything downstream. Chapter 02's notation is chosen for legibility; chapter 03's shape composition is chosen for bounded-findings; chapter 04's workflow is chosen for version-legibility; chapter 05's rebuttal handling is chosen for rebuttal-legibility. The chapters are not independent choices; they are a coherent optimisation for the *review-cycle* the case operates inside.

## Interfaces

- **Chapter 02 (GSN).** The notation the case is written in. This chapter names *what* a case is; chapter 02 names *how* to draw it.
- **Chapter 03 (argument shapes).** The four Clymer-et-al. shapes — Inability, Control, Trustworthiness, Deference. Chapter 03 dissects when each applies and how they compose.
- **Chapter 04 (authoring workflow).** The end-to-end workflow that takes a deployment scope and produces a signable case. Consumes the taxonomy from this chapter as its composition rule.
- **Chapter 05 (pitfalls and rebuttal).** Where the composition fails — evidence-provenance gaps, argument-completeness gaps, elicitation-gap ambiguity, moving-target thresholds — and how to handle a reviewer's rebuttal.
- **Chapter 06 (boundaries).** Where the case is handed off — `ai-evaluation-engineer` (peer / next-up, level 35) consumes it as release-assurance evidence; `senior-ai-governance-architect` (level 50) consumes it as an input to the control-library architecture; mod-112 (Program and Disclosure) shapes it for external audiences.
- **mod-101 (Frameworks).** RSP / Preparedness / FSF are the framework contexts the case is authored *against*. mod-101 names the tier decisions; this module builds the argumentation supporting a specific decision.
- **mod-102 (Threat Model).** The threat model is the *context* node the top claim scopes against. A case that argues under a narrower threat model than mod-102 authored must justify the scoping.
- **mod-112 (Disclosure).** The safety case is the *primary source* the system card and the regulator-facing disclosure derive from. mod-112 shapes it for external audiences; mod-109 authors it.

## Common misreadings to avoid

- **"A safety case proves the deployment is safe."** No. A safety case *argues* that the deployment is *acceptably* safe under stated assumptions against a stated threat model with acknowledged residual risk. Proof is the wrong word; *defensible argument* is the right one. A reviewer's job is to try to break the argument, and the case's job is to route the break into follow-on work.
- **"The evidence is the case."** No. The evidence is what the case *cites*. Without the argumentative structure that composes it, the evidence is a spreadsheet, not a case. The composition is what turns eval numbers into a defensible tier decision.
- **"We can write the safety case after we ship."** No. A safety case authored after the fact is a *retrospective narrative*, not a *pre-registered contract*. The pre-registration is what makes the review meaningful: the developer commits to the claim before the reviewer decides whether it holds.
- **"Argumentation is a documentation exercise."** No. Argumentation is what makes the evidence *decision-supporting*. The right question is not *"is this well-documented"* but *"does this argument, if I try to break it at every leg, either hold or route to a follow-on"* — and the second question is engineering craft.
- **"One case covers all deployments."** No. A case scopes a *specific deployment* (one system, one integration surface, one review period). Cross-deployment claims live in the framework (RSP / Preparedness / FSF); per-deployment claims live in the case. A case with a fuzzy deployment scope is a case with a fuzzy claim, and it will not survive review.
- **"If the reviewer accepts the case, we are done."** No. The case names *undeveloped goals* and *open assumptions*; those flow into follow-on evidence work and into the next review cycle. A safety case is a living artefact through the deployment's lifecycle, not a one-shot approval token.

## Summary

- The **safety case** is the module's load-bearing artefact — a structured, defeasible, evidence-grounded argument that a specific deployment's specific claim (usually *"unacceptable harm of class C avoided during the review period"*) holds under a stated threat model.
- Clymer et al. is the argument-shape reference; Goemans et al. is the working template; GSN Community Standard v3 is the notation; RSP / Preparedness / FSF / AISI pre-deployment reports are the review contexts.
- A case is *not* a system card, a risk register, or an eval report. It cites those; it does not replace them.
- Modules 104, 105, 106, 107, 108, and 110 contribute the *solution nodes* the case grounds itself in. The case is where their evidence composes into a tier decision.
- The case is a **pre-registered contract** between developer, internal review body, and external reviewer / regulator. Evidence drift after signing invalidates the case; hashes on cited artefacts are what make drift detectable.
- Chapter 02 names the notation; chapter 03 the argument shapes; chapter 04 the authoring workflow; chapter 05 the pitfalls; chapter 06 the peer-role boundary.
