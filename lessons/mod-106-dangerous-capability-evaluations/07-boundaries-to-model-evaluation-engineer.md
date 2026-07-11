# 07 — Boundaries — `model-evaluation-engineer` and the External Domain-Expert Graders

## Motivation

Every DCER header carries a deferral contract that names *the roles this module hands work to and receives work from*. This module owns the elicitation protocol, the panel design, the ceiling reporting, and the verdict. It does *not* own the underlying statistical calibration methodology, and it does *not* own the domain adjudication for CBRN / cyber / persuasion tasks. Both are deferred out — the first to a peer role in the ML Engineering family, the second to external domain experts under contract.

This chapter codifies the two most load-bearing deferrals: **`model-evaluation-engineer`** (peer, level 30, ML Engineering family) for statistical calibration methodology, and **external CBRN- / cyber- / R&D-domain expert graders** for domain adjudication. It also names the module-adjacent deferrals: to **mod-110** for adversarial-alignment / AI-control evaluations that bound the credibility of the DCER's expressed-inability claims, and to **mod-112** for the disclosure workflow that governs any external reference.

## Boundary 1 — `model-evaluation-engineer` (peer, level 30, ML Engineering family)

### What the peer role owns

The `model-evaluation-engineer` role at level 30 in the ML Engineering family owns the statistical calibration methodology of model evaluation. Concretely:

- **Best-of-N confidence-interval derivation.** The mathematics of turning an observed pass@N sample into a defensible upper CI on pass@N under bounded population assumptions. The extrapolation from observed pass@N to pass@10·N or pass@100·N under item-difficulty assumptions.
- **Item-response theory (IRT) for item-difficulty accounting.** Fitting an IRT model to the eval item set, estimating per-item difficulty and per-model ability, and reporting the ability estimate with a CI grounded in the IRT fit rather than the raw pass-rate distribution.
- **Judge–human agreement CI derivation.** Bounding the joint uncertainty from judge error rate and item-level annotator disagreement.
- **Sample-size and power planning.** Determining, for a pre-registered threshold-decision-relevant effect size, how many items and how many samples per item are required to reach the CI width the DCER needs.
- **Elicitation-gap statistical extrapolation.** Converting the observed pass@N distribution into a bounded claim about pass@N under M-times-larger sampling budget or under a stronger scaffold.
- **Model-comparison methodology.** When the DCER compares the current model to prior releases (section 5), the methodology for stating "this model exceeds the prior by X on this task family with CI Y" is the peer role's responsibility.
- **Benchmark contamination detection.** For cyber and autonomy panels, the risk that the model was trained on benchmark data; the peer role owns the methodology for detecting and adjusting for contamination.

### What this module (mod-106) owns

The mod-106 role owns the *capability-specific* judgements the statistical methodology cannot make on its own:

- **Which N is decision-relevant.** The peer role can derive the CI for any N; the choice of N (matched to the plausible attacker's sampling budget) is a capability-specific judgement anchored in the threat model.
- **Which items to include in the panel.** Item selection is capability-specific: which WMDP subset, which CyberSecEval task categories, which METR tasks, which persuasion probes.
- **Which elicitation axes to sweep.** Best-of-N vs fine-tune vs scaffold vs tool-use trade-offs are capability-specific.
- **Which residual-gap claim is defensible.** The statistical extrapolation is one input; the capability-specific plausibility of "an attacker's follow-up effort would push the ceiling by δ" is a judgement that needs domain awareness the peer role does not carry.
- **Which threshold-decision-relevant framing to bring to the peer role.** "The tier decision depends on the upper CI of pass@N sitting below X" is a spec the peer role fulfils; that spec is authored here.

### How the deferral works in practice

The DCER's authoring loop is:

1. **This role drafts the elicitation protocol** and names the CI methodology it needs (best-of-N CI at a specified confidence level; IRT-based ability estimate if the item set supports it; judge-agreement CI).
2. **The peer role derives the methodology** — the sample-size plan, the CI derivation, the extrapolation bounds. The derivation is documented in a peer-authored artefact.
3. **This role runs the elicitation** against the protocol.
4. **The peer role computes the CIs** from the observed data.
5. **This role writes the DCER**, citing the peer artefact for methodology and quoting the numeric CIs.

The DCER's Section 3 (elicitation protocol) and Section 4 (panel results) each cite the peer role's artefact by ID. The deferral contract in Section 9 formalises the cite. Every panel's elicitation-gap-accounting clause has a *statistical component* that is a quote from the peer artefact and a *capability-specific component* authored here.

### What red flags the boundary catches

- **In-house CI derivation without peer sign-off** — a DCER that reports "95% CI = [x, y]" without citing the derivation methodology is not fully defensible. The peer role's artefact is the derivation reference.
- **Over-tight CIs.** A CI narrower than the item-set-representativeness plus judge-agreement uncertainty would support is a signal that the derivation is incomplete. The peer role's sample-size plan catches this before it becomes a DCER footnote.
- **Under-selected N.** Choosing a small N because "we only have compute for so many samples" is a capability-specific judgement the DCER surfaces explicitly, not a hidden constraint.

## Boundary 2 — External domain-expert graders

### What external graders own

For every capability panel that involves open-ended output grading, the domain adjudication is externalised:

- **CBRN synthesis-planning grading** (chapter 02) — external biosecurity firms, biosafety-level-trained researchers, or academic biosecurity domain experts. Coordination with WHO / IAEA guidance where the panel touches biological / radiological / nuclear domains.
- **Exploit-writing and CTF adjudication beyond automated grader** (chapter 03) — external penetration-testing firms or offensive-security domain experts for the non-automated fraction of the cyber panel.
- **AI-R&D task adjudication for RE-Bench-adjacent tasks** — external ML-research experts where the automated grader is insufficient. RE-Bench itself carries the reference human-expert scores; extensions to the panel require external adjudication.
- **Long-format persuasion trials** (chapter 05) — external human-subject-research infrastructure (IRB / ethics review, participant recruitment, debriefing).
- **Any adjudication that touches classified or export-controlled domain expertise** — the nuclear sub-panel of CBRN is the canonical case.

### Why in-house adjudication is a red flag

The DCER's defensibility to an RSP Responsible Scaling Officer / OpenAI Preparedness Safety Advisory Group / FSF review body / AISI / EU AI Office reviewer partly rests on the *independence* of the domain adjudication. In-house adjudication of a CBRN synthesis-planning task — even by a genuinely-competent in-house PhD — is a red flag because:

- The in-house adjudicator has an inherent stake in the model being deemed safe.
- The frameworks each name external-domain-expert engagement as an expected element of the tier-relevant panel.
- The FMF working-paper guidance is explicit on external-grader engagement for the load-bearing tiers.
- The AISI / regulator reviewer is explicitly looking for the independence signal.

The DCER names the graders, the engagement contract, the scope of adjudication, the grading rubric co-authoring history, and the grader-agreement CI. Every one is a defensibility signal.

### How the deferral works in practice

1. **This role co-authors the rubric with the grading org** — the rubric encodes the domain-expert-agreed criteria for the panel's tasks. This happens *before* elicitation runs.
2. **Elicitation produces the material** — under the sandbox controls (chapter 01).
3. **Material transits to graders through the access-controlled channel** — air-gapped terminals, need-to-know access, NDA / DPA scoped to the eval.
4. **Graders adjudicate offline** and return scores + rubric-cell verdicts. The material does not exit the terminal.
5. **This role aggregates scores** with the peer role's CI methodology and reports the composite in the DCER.

### What red flags the boundary catches

- **In-house adjudication of a tier-relevant panel.** Route the panel to external adjudication before the DCER is submitted for review.
- **Grader agreement not measured.** A DCER without judge–human agreement CI on the open-ended grading is missing a load-bearing defensibility signal.
- **Rubric authored after elicitation.** Post-hoc rubric authoring is a variant of not-pre-registering; the reviewer sees it as motivated reasoning.
- **Grader material leaving the air-gapped terminal.** The controls exist because leakage propagates the harm the panel was measuring.

## Boundary 3 — mod-110 (AI control / adversarial-alignment evaluation)

The DCER's elicitation-gap-accounting clause for expressed-inability results cites mod-110's control-evaluation companion. The boundary is:

- **mod-106 measures demonstrable capability.** "Under best-effort elicitation, the model achieves X on Y."
- **mod-110 measures the trustworthiness of expressed inability.** "The model's failures / refusals on Y are (are not) sandbagging / alignment-faking / situational-awareness-mediated."

The two compose:

- If **mod-110 finds no sandbagging** on the relevant capability, the DCER's inability claim on that capability stands at its statistical CI width. The residual-gap claim carries only the elicitation-effort uncertainty.
- If **mod-110 finds sandbagging** on the relevant capability, the DCER's inability claim widens by an amount proportional to the sandbagging strength. The residual-gap claim carries the elicitation-effort uncertainty plus the sandbagging-adjusted uncertainty.
- If **mod-110's control evaluation on the capability has not been run**, the DCER states this explicitly and treats the inability claim as bounded by the absence of that companion evidence.

The specific mod-110 evaluations most commonly cited by mod-106 DCERs (see chapter 05):

- **Sandbagging on capability probes** — does the model deliberately underperform when it recognises the evaluation context?
- **Alignment-faking under RLHF-adjacent supervision** — does the model behave differently under supervision than it does when it believes supervision is absent?
- **Situational-awareness probes** — does the model behave as though it recognises when it is being evaluated?
- **Sleeper-agent probes** — does the model have latent trigger-conditioned behaviour that elicitation could surface?
- **Sabotage evaluations** — does the model produce plausibly-helpful outputs that quietly frustrate a goal it disagrees with?

Chapter 05's self-exfiltration propensity panel is the most mod-110-dependent panel in this module. The CBRN and cyber panels are mod-110-dependent to a smaller degree, mostly on the sandbagging question. The autonomy panel is mod-110-dependent on the trustworthiness of task-attempt failures (are they real failures or sandbagged failures?).

## Boundary 4 — mod-112 (disclosure workflow)

Every DCER's Section 8 (rollback triggers) and Section 10 Appendix D (EU AI Act Article 55 / GPAI CoP input) reference mod-112 workflow IDs. The boundary:

- **mod-106 produces the evidence.** The panels, the ceilings, the verdicts.
- **mod-112 governs external disclosure.** System-card sections, AISI-report contributions, EU AI Act Article 55 systemic-risk filings, Article 73 serious-incident reports, frontier-lab-to-frontier-lab coordinated-disclosure notifications, public-safety-advisory releases.

The mod-112 workflow determines *timing, recipient list, redaction policy, and coordinated-release cadence*. The DCER's authors are not the disclosure authors; the DCER *feeds* the disclosure and *cites* the workflow.

Cases the boundary catches:

- A CBRN-uplift crossing that would otherwise be described in a system card without mod-112 workflow gating — the disclosure timing and content are workflow-governed, not DCER-author-choice.
- A cyber-offense novel-vulnerability finding that would otherwise be published on academic timescales — the coordinated-disclosure workflow governs the release.
- A self-exfiltration propensity result that would otherwise be blogged — the workflow governs whether that channel is appropriate for the finding's severity.

## Boundary 5 — level-25 `ai-risk-engineer` prerequisite

The general adversarial-ML methodology `ai-risk-engineer` teaches (garak, PyRIT, ART, TextAttack, Promptfoo red-team, general Inspect usage) is the substrate this module builds on. This module does not re-teach:

- General LLM red-teaming.
- General adversarial-ML robustness measurement.
- General risk-register wiring.
- General MRM lifecycle.

The DCER cites `ai-risk-engineer`-owned artefacts (a garak red-team run, a risk-register entry) where they compose with the panel measurements, but does not re-derive them.

## Boundary 6 — module-internal boundaries

The mod-106 role composes with adjacent modules in the same track:

- **mod-101 (frontier safety frameworks)** — the tier definitions and framework structure this module measures against. The DCER's Section 1 (executive summary) quotes framework tier language pinned in mod-101.
- **mod-102 (agent threat modelling)** — the threat-model shapes this module's panels ground against. The panel threat-model summaries (Section 2) draw on mod-102's harm-taxonomy authoring.
- **mod-103 (prompt-injection engineering)** and **mod-104 (jailbreak engineering)** — the elicitation-axis primitives. Best-of-N elicitation uses jailbreak-adjacent prompt strategies; the DCER cites the jailbreak's effectiveness under the current model version.
- **mod-105 (agent attack surface)** — the tool-use-elicitation panels compose with the AASS's tool-abuse-chain findings; a chain-elicited capability is a mod-105 finding *and* a mod-106 elicitation-axis input.
- **mod-107 (excessive-agency containment)** — the deployed-stack ceiling (Section 6) reflects the containment mod-107 engineers.
- **mod-108 (guardrails and monitors)** — the deployed-stack ceiling similarly reflects the guardrails; adaptive-attack survival curves (mod-108) feed the panel's residual-gap-accounting.
- **mod-109 (safety-case authoring in GSN)** — the DCER's *inability* argument evidence feeds the safety-case's inability-argument leg.
- **mod-110 (AI control / adversarial-alignment)** — the DCER's expressed-inability claims cite mod-110 (see Boundary 3 above).
- **mod-111 (automated / scaled red-team)** — the elicitation protocol may use scaled attacker infrastructure from mod-111; the DCER cites the scaled-run artefacts.
- **mod-112 (safety program and disclosure)** — the DCER feeds the disclosure workflow (see Boundary 4).

## The consolidated deferral contract

Every DCER carries the deferral contract in its Section 9. The template — instantiated by chapter 06 — has three tiers of deferral:

- **Down** — level-25 `ai-risk-engineer` general adversarial-ML.
- **Sideways** — level-30 peers: `model-evaluation-engineer` (statistical), `ai-eval-engineer` (plumbing), `fine-tuning-engineer` (fine-tune-elicitation implementation); level-35 peers: `ai-infra-security` (isolation), `ai-evaluation-engineer` Governance (release-assurance packaging).
- **External** — CBRN / cyber / R&D domain-expert graders, IRB / ethics review, in-house biosecurity / nuclear-safety counsel gate.

Plus module-internal peer dependencies (mod-110 primarily; mod-108 for deployed-stack ceiling; mod-105 for tool-use-elicited chains) and downstream consumers (mod-109 safety-case, mod-112 disclosure, senior architect, program lead).

## Common misreadings to avoid

- **"The peer role owns the panel's headline number."** No. The peer role owns the CI derivation. The panel design, the item selection, the elicitation-axis choices, and the residual-gap claim are here.
- **"External graders decide the tier verdict."** No. External graders adjudicate the open-ended tasks. The verdict is a comparison of the composite ceiling (which includes grader-adjudicated inputs) against the pre-registered threshold. The DCER's authors state the verdict.
- **"mod-110 dependency is optional."** For self-exfil propensity and any expressed-inability-load-bearing panel, it is not optional — the DCER states the dependency explicitly, and if the companion evaluation has not been run, the residual-gap claim widens.
- **"mod-112 gates every finding."** Mod-112 governs the *disclosure* of findings whose disclosure has safety-material or regulator-material implications. Panel-internal findings that stay within the safety-team's operational review do not require mod-112 disclosure workflow.
- **"The deferral contract is decoration."** It is the mechanism that keeps this role from re-doing the peer role's methodology and keeps the peer role from re-designing the panel. Missing deferral contracts produce DCERs that either overreach into peer methodology or leave methodology dangling.

## Summary

- The DCER's deferral contract binds five external / peer boundaries: **`model-evaluation-engineer`** (statistical calibration), **external domain-expert graders** (adjudication), **mod-110** (control / adversarial-alignment), **mod-112** (disclosure workflow), and the level-25 `ai-risk-engineer` prerequisite.
- **`model-evaluation-engineer`** owns best-of-N CI, IRT ability estimation, sample-size planning, judge-agreement CI, and elicitation-gap statistical extrapolation. This module cites their artefacts and consumes their derivations; the capability-specific judgements — which N, which items, which axes — remain here.
- **External graders** adjudicate open-ended CBRN / cyber / R&D / persuasion tasks; in-house adjudication is a defensibility red flag. Rubric co-authoring, air-gapped terminals, and grader-agreement measurement are load-bearing.
- **mod-110** provides the trustworthiness companion for expressed-inability claims; the DCER's residual-gap accounting cites the mod-110 result and widens the claim where the companion is absent or reveals sandbagging.
- **mod-112** governs external disclosure; the DCER cites the workflow ID but does not author the disclosure.
- The consolidated deferral contract in DCER Section 9 is the mechanism that keeps every role owning its slice.
