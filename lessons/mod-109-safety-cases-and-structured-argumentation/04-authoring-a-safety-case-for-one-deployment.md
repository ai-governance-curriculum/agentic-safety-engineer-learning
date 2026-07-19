# 04 — Authoring a Safety Case for One Deployment

## Motivation

Chapters 01–03 built the vocabulary: what a safety case is, what notation it is written in, what argument shapes it decomposes into. This chapter is the workflow. Given a concrete deployment — one system, one tool inventory, one integration surface, one review period — how do you author a safety case that a Responsible-Scaling Officer, a Preparedness Committee, a Safety Board, or an AISI evaluator will sign off on?

The workflow is not a linear checklist. Authoring iterates: a first pass draws the top claim and the shape decomposition; the second pass tries to break each leg and routes the breaks into evidence work; the third pass unions the assumptions and audits them; the fourth pass tightens the top claim so it survives the audit. The senior technical-specialist craft is doing this iteration *in the light* — with the argument's shape, the evidence's provenance, and the assumption's dependencies all legible to a reviewer looking over your shoulder.

This chapter models the workflow on RSP / Preparedness / FSF tier decisions and on the AISI pre-deployment methodology, because those are the review contexts a real level-40 case is authored *for*. Exercise 03 asks you to walk the full workflow end-to-end on a specific deployment of your choosing.

## Primary sources

- **[Goemans et al. — "Safety Case Template for Frontier AI"](https://arxiv.org/abs/2411.08088)** <!-- needs-research: confirm arXiv identifier for Goemans et al. — the working template that this chapter's workflow scaffolds from. -->
- **[Clymer et al. — "Safety Cases: How to Justify the Safety of Advanced AI Systems"](https://arxiv.org/abs/2403.10462)** — §5's worked examples are the reference decomposition patterns for the shapes this chapter composes.
- **[Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp)** — ASL tier definitions are the *contexts* the case scopes against.
- **[OpenAI Preparedness Framework](https://openai.com/safety/preparedness)** — Preparedness scorecard thresholds are the *thresholds* the top claim references.
- **[Google DeepMind Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/)** <!-- needs-research: pin the current FSF version and its URL. --> — Critical Capability Level thresholds are the third framework context.
- **[UK AI Safety Institute — pre-deployment methodology](https://www.aisi.gov.uk/)** <!-- needs-research: pin the AISI pre-deployment methodology publication used as the reviewer-posture reference. -->
- **[GSN Community Standard v3](https://scsc.uk/gsn)** — the notation the workflow authors in.

## The seven-step workflow

The workflow has seven steps. Steps 1–3 scope; step 4 drafts; steps 5–7 iterate. The output is a signable safety case for one deployment.

1. **Scope the deployment.**
2. **Identify hazards and select the top claim.**
3. **Assemble the evidence portfolio.**
4. **Draft the top goal, shape decomposition, and strategies.**
5. **Ground the leaves in solution nodes; mark undeveloped goals.**
6. **Capture contexts, assumptions, and justifications.**
7. **Iterate: break the argument, audit assumptions, tighten the claim, re-sign.**

The chapter walks each step in the order authoring actually happens, with a running example (a large-language-model *code-modification agent* deployed with tool access to a code repository, a test runner, and a bug-tracker) so that the abstract steps have concrete shape.

## Step 1 — Scope the deployment

Before you write a single goal, name what you are authoring the case *for*. Scope has five constitutive fields, and the first substantive draft of the case makes all five load-bearing contexts (chapter 02):

- **System identity and version.** *"Model `code-agent-v3.7.2`, weights hash `sha256:...`, tokenizer `bpe-32k-...`, inference stack `vllm-0.6.3`, safety-tuning fingerprint `sft-2026-01-14`."* Weights and tokenizer and inference-stack drift each individually break Inability evidence (chapter 03); the scope pins all three.
- **Integration surface.** *"Tools: `git_clone`, `git_commit`, `pytest_run`, `bugtracker_comment`, `bugtracker_status_update`, all wrapped per EACC v3.7. No other tools. No fine-tuning API. No plug-in store. No user-facing chat surface — API only."* If the surface widens, the case widens with it.
- **User / principal population.** *"Called only from an internal CI job with a service-account principal; no external end-user access; the CI job's inputs are `git` refs of pull requests in the `acme/backend` repo."* mod-102's threat model is scoped against this population; the case inherits the scoping.
- **Review period.** *"Case valid from 2026-Q1-canary through 2026-Q2-GA (2026-04-01 → 2026-06-30 wall-clock). Post-period, re-authoring required."* A safety case is not perpetual; every case has an expiry.
- **Regulatory / framework context.** *"RSP ASL-2 review, Preparedness `Autonomy` scorecard `Medium` threshold, EU AI Act Article 55 systemic-risk pre-emption. No FSF submission planned this period."* The frameworks the case is authored for constrain the shape decomposition (chapter 03) and the evidence's version-pinning (mod-101).

The scope is the artefact's `C-SCOPE` context and, in a well-modularised case, the *deployment* module's public interface.

## Step 2 — Identify hazards and select the top claim

With the scope in hand, enumerate the hazards mod-102's threat model identifies for this deployment. For the running code-agent example, the enumeration might include:

- H-1: Malicious commit that introduces a backdoor.
- H-2: Exfiltration of internal source code via `git_commit` to an unintended remote.
- H-3: Unbounded resource consumption via `pytest_run` on adversarial inputs.
- H-4: Bugtracker manipulation that hides a security disclosure.
- H-5: Cross-repo attack that touches out-of-scope repositories.

For each hazard, characterise:

- **The unacceptable-harm threshold.** For H-1, "any successful merge of a backdoor into `main`"; for H-3, "sustained CPU usage above N% for M minutes on the CI runner." Thresholds are precise; hand-waving here is a chapter-05 pitfall.
- **The relevant framework tier.** H-1 and H-2 map onto the *autonomy* and *self-exfiltration* Preparedness categories; H-5 maps onto EACC blast-radius. The map determines shape selection (step 4).
- **The evidence types that address it.** H-1 addressed by red-team + code-review-in-the-loop; H-2 by egress allow-listing + audit-log; H-3 by budget / rate limit; H-4 by tamper-evident log + audit trail; H-5 by PoLA credentials + repo allow-list.

From the hazard enumeration, draft the **top claim**. For our running example:

> *G-TOP: During the review period 2026-04-01 → 2026-06-30, the code-modification agent as scoped in C-SCOPE, operating against the threat model in C-THREAT-MODEL, will not merge a code change into any `main` branch across the repositories in C-REPO-SCOPE that introduces a security defect classified as CVSS ≥ 7.0 without human review, with residual probability ≤ 1×10⁻⁴ per merge, and will not exfiltrate proprietary source code outside the C-EGRESS-ALLOW-LIST hosts.*

That is a top claim: refutable, threshold-carrying, scope-carrying, review-period-carrying. Not *"the code agent will not do harm"* (unrefutable).

## Step 3 — Assemble the evidence portfolio

Enumerate the evidence artefacts each hazard's addressing evidence types map to. This is the module-level composition chapter 01 named. For our running example:

| Hazard | Evidence artefact | Source module | Version-pin |
|---|---|---|---|
| H-1 | Adversarial-code-injection red-team suite v0.4 | mod-105 | git tag `red-team-code-agent-0.4.0` |
| H-1 | DCER §"code-modification autonomy" | mod-106 | doc hash `sha256:...` |
| H-2 | EACC v3.7 §egress + §credential | mod-107 | doc hash `sha256:...` |
| H-2 | Monitored-tool-wrapper audit-log stream (7-day retro) | mod-107 ch 03 | log-index snapshot |
| H-3 | EACC v3.7 §budget + §rate-limit | mod-107 | doc hash `sha256:...` |
| H-3 | Sandbox cgroup telemetry (14-day retro) | mod-107 ch 02 | telemetry snapshot |
| H-4 | Tamper-evident action log Merkle-root pinning | mod-107 ch 03 | Merkle-root anchor |
| H-4 | Bugtracker-integrity monitor | mod-108 | monitor version `bt-integ-2.1.0` |
| H-5 | EACC v3.7 §side-effect-scope | mod-107 | doc hash `sha256:...` |
| all | Control eval v1.2 (Redwood-style) | mod-110 | doc hash `sha256:...` |
| all | AISI pre-deployment engagement report | Deference | AISI docket `AISI-2026-Q1-...` |

For each row, capture the *provenance*: who ran it, when, under what protocol, with what elicitation-gap accounting. Provenance is what chapter 05's *evidence-provenance gaps* pitfall attacks; documenting it up front is cheap, retro-fitting it is expensive.

Absence of an evidence row for a hazard is a flag: either an undeveloped goal (step 5) or a case-scope reduction (step 1).

## Step 4 — Draft the top goal, shape decomposition, and strategies

With the top claim (step 2) and the evidence portfolio (step 3) in hand, draft the case's spine. Follow the Clymer-et-al. shape composition (chapter 03):

- **Top strategy `S-TOP`.** Decompose the top claim by argument shape. For the code-agent claim, the Inability leg is thin (the model *does* have code-modification capability; that is the point of the deployment), so Inability is not load-bearing. Control is load-bearing (the EACC prevents the harmful merge / exfil), Trustworthiness contributes (the model reliably requests code review), and Deference corroborates (the AISI engagement).
- **Sub-goals per shape.** `G-CONTROL`, `G-TRUSTWORTHINESS`, `G-DEFERENCE`, each decomposed further per the patterns in chapter 03.
- **Per-shape strategies.** `S-CONTROL` decomposes by containment layer (EACC × monitors × HITL × kill-switch). `S-TRUSTWORTHINESS` decomposes by disposition axis (refusal-of-scope-widening × deception-eval × alignment-faking-test). `S-DEFERENCE` decomposes by external-evaluator (AISI × third-party red-team).
- **Justifications on each strategy.** *"S-TOP is jointly sufficient because at ASL-2 with high-blast-radius tool access, the framework language calls for Control-shape as load-bearing, augmented by Trustworthiness and corroborated by Deference (Clymer et al. §4). Inability is inadmissible because the model demonstrably has the deployed capability."*
- **Module boundaries.** For a case this size, modularise: a *Containment* module (owns `G-CONTROL` and its decomposition), a *Trustworthiness* module (owns `G-TRUSTWORTHINESS`), a *Deference* module, a *Lifecycle* module (staged rollout, canary criteria, rollback contract).

The output of this step is a GSN outline (chapter 02) — the case's structural spine with strategies and sub-goals named, but leaves not yet grounded. This is the artefact the authoring engineer commits to version control for the first review pass.

## Step 5 — Ground the leaves in solution nodes; mark undeveloped goals

Walk the spine's leaves. For each, one of three things is true:

- **A solution grounds it.** Cite the specific evidence artefact from step 3's portfolio, including the artefact ID, version-pin, methodology-summary, and provenance. The solution is a leaf of the case; its detail lives in the artefact.
- **An undeveloped-goal marker applies.** The leaf is acknowledged as open. Every undeveloped marker carries a *closure plan* — either scheduled evidence work in a named module (mod-104 sprint N, mod-106 pipeline run X) or an accepted-residual-risk classification.
- **A follow-on solution is *in progress* but not yet complete.** Chapter 02's *uninstantiated pattern* marker applies — the pattern is drawn but not bound. The case may ship with these if the deployment lifecycle permits (canary release, staged rollout), and the pattern must be bound before the corresponding rollout gate.

For our running example, the code-agent's `G-CONTROL-EACC-EGRESS` leaf grounds in the EACC §egress solution; the `G-TRUSTWORTHINESS-ALIGNMENT-FAKING` leaf grounds in the mod-110 alignment-faking test; the `G-CONTROL-MONITOR-COVERAGE-ADAPTIVE-ATTACK` leaf may be undeveloped in the first-pass draft (the adaptive-attack-survival curve for the new monitor is still being run) and is marked as such with a closure plan referencing the mod-108 sprint that will run it.

The output is a *substantially grounded* GSN outline. Undeveloped markers are visible; the closure plan is visible; the case is ready for assumption capture.

## Step 6 — Capture contexts, assumptions, and justifications

Walk the case a second time and, for every goal and every strategy, ask:

- *"What am I assuming that I have not established?"* Every honest answer becomes an `A-` assumption node attached to the goal or strategy.
- *"What context does this goal or strategy scope against?"* Every honest answer becomes a `C-` context node.
- *"Why is this decomposition rule complete?"* Every honest answer becomes a `J-` justification node.

For the code-agent example, the assumption catalogue for the top goal and the Control shape will include, at least:

- *A-EACC-ENFORCED.* The EACC v3.7 is honoured by the runtime (wrapper is not bypassed, sandbox is not escaped, tamper-evident log is intact).
- *A-MONITOR-COVERAGE-CI-REPRESENTATIVE.* The monitor's labelled evaluation corpus is representative of the CI-job traffic distribution over the review period.
- *A-MODEL-STABILITY.* The deployed model's fingerprint matches the model against which the evidence in the DCER, red-team suite, and control eval was collected.
- *A-CONTROL-EVAL-THREAT-MODEL.* The control eval's modelled adversary is at least as strong as the deployment's actual adversary, per mod-102's threat model.
- *A-HITL-COMPETENT.* The human reviewers on the receiving end of escalations are competent to evaluate the escalated diffs.
- *A-KILL-SWITCH-DRILL.* The kill-switch has been drilled within the last 90 days and the SLA is honoured.
- *A-EXTERNAL-EVALUATOR-SCOPE.* The AISI engagement evaluated the deployed configuration (not the base model, not a prior variant).

The output is a GSN outline with the *union* of assumptions visible. This union is the artefact chapter 05's assumption-audit walks; a case whose assumption capture is incomplete will be found out at review.

## Step 7 — Iterate: break the argument, audit assumptions, tighten the claim, re-sign

This is where the authoring craft happens. The first six steps produce a *draft*; the seventh turns it into a *defensible* case.

- **Break-the-argument pass.** For each leaf, ask *"what would I have to observe to falsify this?"* For each strategy, ask *"what decomposition am I missing?"* For each assumption, ask *"what would break it?"* Route each break into follow-on evidence work, into a strategy revision, or into an accepted residual risk noted explicitly on the top claim.
- **Assumption-audit pass.** Union the assumptions across shapes (chapter 03's compositional-assumption warning). Look for assumptions that a *single* module owns but that multiple shapes depend on (e.g., *A-MODEL-STABILITY* is load-bearing for Inability *and* for Trustworthiness). Compositional assumptions get their own goals; a broken compositional assumption breaks multiple legs simultaneously.
- **Threshold-tightness pass.** Read the top claim through a hostile lens. Does the residual probability match what the evidence actually supports? Is the review period tight enough that the version-pinned evidence stays valid? Is the scope narrow enough to be true and wide enough to be useful?
- **Rebuttal-preparation pass.** For each of the RSP / Preparedness / FSF review criteria and each of the AISI methodology's pre-deployment findings-classes, write down the reviewer's likely counter-argument and the case's response. Chapter 05 walks the rebuttal-handling pattern operationally; this pass is where you prepare for it.
- **Sign-off routing.** The Responsible-Scaling Officer / Preparedness Committee / Safety Board / equivalent is the internal signer (per mod-101). The peer-role co-signers (mod-107 for the containment leg, mod-108 for the monitor leg, mod-110 for the control-eval leg, mod-112 for the disclosure surface) are named on the artefact. A case authored solo and signed solo is a case that has been through only one head; that is a chapter-05 pitfall.

The seventh step is not the *last* pass; it is the pass that ships. The case iterates on subsequent review cycles too — evidence drifts, tiers change, the model changes, the deployment surface widens — and each iteration reruns steps 4–7 for the affected legs.

## Modelling the workflow on RSP / Preparedness / FSF and on AISI

Each framework shapes the workflow slightly differently. The differences are worth naming so that the case is authored *for the review it will actually face*.

- **Anthropic RSP.** The ASL tier language is the load-bearing framework context. ASL-2 admits Inability-shape as load-bearing; ASL-3 shifts to Control-shape as load-bearing; ASL-4 requires composed Control × Trustworthiness with corroborating Deference. The case's top-level strategy names the ASL tier and the strategy inheritance from Clymer's §4 shape prescription.
- **OpenAI Preparedness.** The scorecard categories (cyber, CBRN, autonomy, persuasion) shape the *risk-class* decomposition of the top claim. The scorecard *level* ("Low", "Medium", "High", "Critical") shapes the shape prescription. Preparedness Committee sign-off is the case's internal signer.
- **Google DeepMind FSF.** The Critical Capability Level thresholds are shape-agnostic in framing but demand explicit *"reaching CCL would trigger…"* language in the case's rollback contract. The FSF's mitigation-gate structure maps onto the *Control* module's decomposition.
- **UK / US AISI pre-deployment.** AISI does not author the safety case; they *read* it as the primary source for their pre-deployment report. Their *"could not verify X"* findings drive re-authoring of the specific leg X. Author the case so that a *"could not verify"* finding routes to a single leg, not to the whole case.
- **EU AI Act Article 55 / 56.** GPAI systemic-risk pre-emption mechanisms are shape-agnostic but require an EU-facing disclosure surface (mod-112). Author the case so that a specific EU-facing disclosure can cite a specific goal / solution ID (chapter 02's programmatic-citation posture).

## Interfaces

- **Chapter 01–03.** Vocabulary, notation, shapes.
- **Chapter 05 (pitfalls).** Consumes the assumption-audit and evidence-provenance capture from this chapter's steps 3, 5, and 6.
- **Chapter 06 (boundaries).** The authored case is handed off to `ai-evaluation-engineer` (peer / next-up, level 35) as release-assurance evidence and to mod-112 (Disclosure) as disclosure input.
- **mod-101.** Framework context — RSP / Preparedness / FSF — that step 1 pins.
- **mod-102.** Threat model that steps 1 and 2 scope against.
- **mod-104 / 105 / 106 / 107 / 108 / 110.** Evidence artefacts that step 3 assembles and step 5 grounds leaves in.
- **mod-112.** Disclosure surface that step 7's rebuttal-preparation writes toward.

## Common misreadings to avoid

- **"Author the top claim last, after you see what the evidence supports."** No. Draft the top claim early — it constrains scope. If evidence work drifts the claim, you note the drift explicitly and re-author; you do not silently reshape the claim to fit the evidence you happened to have.
- **"Undeveloped goals mean the case is incomplete and cannot ship."** Not necessarily. A case ships with undeveloped goals when the goals are (a) explicitly acknowledged, (b) closure-planned, and (c) not blocking for the review context (e.g., closed before the corresponding rollout gate). What kills a case is *hidden* incompleteness, not *acknowledged* incompleteness.
- **"Assumptions are edge cases; most of the case is claims and evidence."** No. Assumptions are the *load-bearing scaffolding*. Every unnoted assumption is a rebuttal surface a reviewer will find. The assumption catalogue is often the longest single section of a mature case.
- **"The workflow is a checklist; do it once and ship."** No. The workflow *iterates*. Step 7 loops back into steps 4–6 as evidence is broken, assumptions are tightened, and the claim is re-scoped. The case that ships is the fifth or tenth pass, not the first.
- **"The signer is the author."** No. Author and signer are separated: authoring is the safety-engineering role; signing is the internal-safety-review body (RSO / Preparedness Committee / Safety Board). Peer-role co-signers on the individual modules are named. The separation is what makes the case *reviewed*, not just *reviewed-by-the-author*.
- **"Modelling on RSP is the same as modelling on Preparedness or FSF."** The three frameworks agree on much and differ on load-bearing details (shape prescription per tier, mitigation-gate structure, sign-off routing). A case authored generically for "frontier safety" will look under-defended to each specific framework's reviewer. Pick the framework the review will be under; if there are multiple, note the multiplicity and satisfy the tightest.

## Summary

- The authoring workflow has seven steps: **scope**, **hazards + top claim**, **evidence portfolio**, **draft spine**, **ground leaves + mark undeveloped**, **capture contexts / assumptions / justifications**, **iterate**.
- The first six steps produce a draft; the seventh — *break, audit, tighten, re-sign* — turns it into a defensible case.
- Scope is a load-bearing artefact in its own right. A case with a fuzzy scope is a case with a fuzzy claim.
- Compose evidence *and* assumptions across shapes. Compositional assumptions (an assumption load-bearing for more than one shape) get their own capture; a broken compositional assumption breaks multiple legs.
- Model the workflow on **the review the case will actually face** — RSP tier language, Preparedness scorecard structure, FSF CCL thresholds, AISI pre-deployment methodology, EU AI Act Article 55 / 56.
- Undeveloped goals are legitimate when acknowledged, closure-planned, and non-blocking for the review context. Hidden incompleteness is what kills a case; acknowledged incompleteness routes to follow-on work.
- Exercise 03 asks you to walk this workflow end-to-end on a deployment of your choice. Do not skip step 7 — the *iteration* is where the argument actually becomes defensible.
