# 03 — Inability, Control, Trustworthiness, Deference

## Motivation

Chapter 01 named the safety case. Chapter 02 named the notation. This chapter names the *arguments*. Clymer et al. — the paper this module is anchored on — identify four canonical argument shapes that frontier-AI safety cases decompose their top claim into: **Inability**, **Control**, **Trustworthiness**, and **Deference**. Every mature safety case for a frontier deployment either composes these four shapes explicitly or defends its choice to omit one.

The shapes are not stylistic preferences. Each one commits the case to a *different evidence portfolio*, a *different set of assumptions*, and a *different rebuttal surface*. Inability requires a defensible dangerous-capability elicitation report (mod-106) and stakes the whole leg on the elicitation-gap accounting. Control requires a defensible containment contract (mod-107), monitor coverage (mod-108), and — this is where practitioners under-invest — a *control evaluation* (mod-110) showing the containment holds under an adversarially aligned model. Trustworthiness requires evidence about the model's *disposition* to try, which the field has thin methodology for and which mod-110 begins to address. Deference requires an external evaluator whose independent evidence corroborates the claim, and the credibility of that evaluator is itself part of the argument.

A safety case that leans on one shape when the deployment demands another is a case that will not survive review. This chapter walks each shape's evidence portfolio, assumption set, and composition rules, and closes with the composition patterns that combine two or more shapes into a defensible top-claim decomposition.

## Primary sources

- **[Clymer, Gabrieli, Krueger, Larsen — "Safety Cases: How to Justify the Safety of Advanced AI Systems" (arXiv:2403.10462)](https://arxiv.org/abs/2403.10462)** — the taxonomy of the four shapes. Read §§ 2–4 before this chapter and keep the paper open while working through it.
- **[Goemans et al. — "Safety Case Template for Frontier AI"](https://arxiv.org/abs/2411.08088)** <!-- needs-research: confirm arXiv identifier for the Goemans safety-case template. --> — a worked *Control*-shaped case that shows the shape's decomposition rules operationally.
- **[Greenblatt, Shlegeris, and Redwood Research — "AI Control: Improving Safety Despite Intentional Subversion"](https://arxiv.org/abs/2312.06942)** <!-- needs-research: confirm arXiv identifier and title for the Redwood AI Control paper. --> — the methodological foundation for the *Control* shape when the model is assumed adversarial.
- **[Anthropic Responsible Scaling Policy](https://www.anthropic.com/rsp)** — the ASL framework. ASL-3 and above shifts progressively from Inability to Control-shaped arguments; the tier language names the shift.
- **[OpenAI Preparedness Framework](https://openai.com/safety/preparedness)** — Preparedness scorecard thresholds are shape-agnostic claims; the *case* the developer authors selects a shape.
- **[UK AI Safety Institute — pre-deployment methodology](https://www.aisi.gov.uk/)** <!-- needs-research: pin the specific AISI pre-deployment methodology publication and any published shape-specific guidance. --> — an external-evaluator posture that in practice corroborates the *Deference* leg of a submitted case.

## Shape 1 — Inability

### The claim shape

*"The model **cannot** produce the unacceptable harm at a level sufficient for the threshold in the review context."*

The load-bearing verb is **cannot**. Not *"will not,"* not *"is prevented from,"* not *"is unlikely to try"* — **cannot**, in the sense that no reasonable elicitation of the model under adversarial fine-tuning, best-of-N scaffolding, tool augmentation, and red-team effort would surface the capability at the threshold level.

### Evidence portfolio

Inability's evidence portfolio is dominated by **mod-106**'s Dangerous-Capability Evaluation Report (DCER). The DCER's shape is what an Inability leg cites:

- **Capability-elicitation protocol.** Best-of-N sampling ceilings, elicitation-gap-adjusted numbers, tool-use-elicitation, scaffolding, fine-tune-elicitation where scoped. The protocol is the argument's *methodology*; the numbers are the argument's *result*.
- **Pre-registered threshold.** The RSP ASL / Preparedness scorecard / FSF Critical-Capability-Level threshold the claim references. The claim is *"the elicitation-adjusted ceiling is below the threshold with confidence Y."*
- **External domain-expert grading.** For CBRN, cyber-offense, autonomy, persuasion, self-exfiltration, the grader is not the model, is not the developer, and is often not even the internal safety team. mod-106 pins the grader panel; the DCER cites their sign-off.
- **Elicitation-gap accounting.** The critical claim is not *"the model's observed capability is below the threshold"* — it is *"the model's reachable capability under adversarial effort is below the threshold, with the accounted gap bounded by δ."* This is where chapter 05's *elicitation-gap ambiguity* pitfall lands hardest.
- **Model-fingerprint pinning.** The evidence is against a specific weights hash, a specific tokenizer, a specific inference stack. Version drift invalidates the leg (chapter 05).

### Assumptions

Inability arguments carry a small number of load-bearing assumptions:

- *A-ELICITATION-COMPLETE.* The elicitation protocol captures the reachable ceiling. In practice, this is *never* fully defensible; the case pairs the assumption with an explicit residual-δ and, at higher tiers, adds a Control leg (below) to cover the residual.
- *A-MODEL-STABILITY.* The evidence's model matches the deployed model. Post-training safety tuning, RLHF cycles, and adapter updates all break this if they touch the safety-relevant behaviour space without re-eval.
- *A-CAPABILITY-STABLE-UNDER-DEPLOYMENT.* Capabilities the elicitation did not reach are not routinely reachable through the deployed stack (fine-tuning APIs, long-horizon planning, sub-agent decomposition). Deployment stacks that widen elicitation surface (fine-tuning-as-a-service, agent scaffolds the developer sells) weaken the assumption.

### When Inability is defensible

- **Below the RSP ASL-3 threshold / Preparedness "High" / FSF equivalents.** For lower tiers, Inability is often *the* load-bearing shape. The model genuinely cannot uplift a bio-threat actor at ASL-2 depth; the elicitation confirms it; the argument holds.
- **For capabilities the frontier does not yet possess.** Some claims are Inability by virtue of the state of the art — no current model can meaningfully autonomously self-exfiltrate at the scale required for the FSF CCL threshold. The argument writes down the state of the art and cites the elicitation that pins it.
- **For narrow, well-scoped harm classes.** *"Cannot synthesise a specific class of chemical warfare agent given prompts drawn from the WMDP-Chem test set at BON=64"* is defensible; *"cannot uplift chemistry attackers in general"* is not.

### When Inability is *not* defensible

- **At and above ASL-3 / Preparedness "Critical" / FSF CCL.** Above these tiers, an Inability-only case is under-defended by the framework's own construction: the tier language explicitly assumes the capability exists and shifts the argumentation onto Control. A case that ships an Inability-only leg at these tiers is inviting a rejection.
- **When the elicitation gap is large or unknown.** If mod-106's DCER cannot bound δ tightly, the Inability claim inherits the ambiguity. Pair with Control.
- **When the deployed stack widens elicitation surface.** A model exposed via a fine-tuning API can have capabilities the base model did not; an Inability argument that ignores the fine-tuning surface is not defensible.

## Shape 2 — Control

### The claim shape

*"Even if the model tried to produce the unacceptable harm, the **deployment** prevents it."*

The load-bearing verb here is **prevents**. Control is the shape frontier-lab safety cases lean on hardest at higher tiers, because it decouples the safety claim from the model's disposition and grounds it in *runtime engineering*. Redwood's AI Control paper is the methodological anchor: *safety despite intentional subversion*, treating the model as adversarial and asking whether the deployment protocol survives.

### Evidence portfolio

Control's evidence portfolio is broad and, unlike Inability, distributed across many modules:

- **The Excessive-Agency Containment Contract (mod-107).** The EACC is a *primary solution* in a Control leg. Its sections 1–7 map onto sub-goals in the Containment module of the case: allow-list correctness (§1), argument validation (§2), side-effect scope (§3), credential PoLA (§4), budget / rate / blast-radius (§5–§6), escalation and failure contracts (§6–§7).
- **The runtime enforcement evidence.** mod-107 chapter 03's monitored-tool-wrapper audit-log stream. The load-bearing claim is *"the wrapper honoured the EACC on N calls; the tamper-evident log is intact; the drift monitors did not fire."* The log stream is not evidence *of a decision*; it is evidence *of enforcement*.
- **The monitor coverage report (mod-108).** Classifier guards, Constitutional Classifiers, rule / flow guards, safety-monitor sidecars — each with its FP / FN, latency, and adaptive-attack survival curve. Control's claim is that the layered guardrails catch the residual behaviour containment does not prevent.
- **The control evaluation (mod-110).** Redwood-style red-team-blue-team games run under an intentionally adversarial model. The control eval's output — *"the blue-team protocol contains X% of adversarial attempts under the sandbagging / alignment-faking / deception threat model"* — is what makes the Control leg defensible against the *"but what if the model is trying?"* rebuttal.
- **The kill-switch drill result (mod-107 chapter 05).** The runbook, the drill-run timing, the false-positive cost accounting. Control leans on the kill switch as the *last-resort* leg; the drill's evidence is that the switch actually fires.
- **The HITL escalation contract (mod-107 chapter 04).** The escalation thresholds, the bypass-prevention posture, the timeout / revocation posture. A Control leg that assumes a human approves the risky calls needs evidence that the escalation actually reaches a competent human under load.

### Assumptions

Control arguments carry a *larger* assumption set than Inability, because they depend on the runtime and on the monitors as much as on the model:

- *A-CONTAINMENT-INTEGRITY.* The wrapper is not bypassed, the sandbox is not escaped, the audit log is not tampered with. Chapter 05 catalogues the sub-classes of this assumption's failure.
- *A-MONITOR-COVERAGE.* The monitor's labelled evaluation corpus is representative of the deployment's traffic distribution. Adaptive-attack-survival curves bound this assumption at the edges, but the middle of the distribution is where drift bites.
- *A-CONTROL-EVAL-REPRESENTATIVE.* The Redwood-style red-team modelled the *actual* adversarial-alignment surface the deployment faces. A control eval that ran a narrower threat model than mod-110 authored under-covers.
- *A-HITL-ACTUALLY-ENFORCED.* Escalations reach humans, humans are not overwhelmed, the bypass-prevention posture (mod-107 chapter 04) is not defeated by fatigue / social engineering / approval-bombing.
- *A-KILL-SWITCH-PROPAGATES.* The switch fires within the SLA, false-positive cost is accepted, on-call routing is in place.

### When Control is defensible

- **At and above ASL-3 / Preparedness "Critical" / FSF CCL.** Control is the load-bearing shape at these tiers. The framework's own language expects it.
- **For deployments with high-blast-radius tools.** A payment tool, a code-interpreter with network egress, a customer-database write path — Control is the shape. Inability alone is not.
- **When mod-110's control eval is genuinely defensible.** Weak or absent control-eval evidence weakens Control at exactly the point it matters — the *"but what if the model is trying"* rebuttal. Chapter 05 §on-elicitation-gap-ambiguity extends to this: a Control leg without mod-110 evidence is a leg that has not been stress-tested.

### When Control is *not* defensible

- **When the runtime is not narrower than the model.** If the EACC is unenforced — no wrapper, no sandbox, no audit log — Control's evidence portfolio is empty. Ship containment first, then argue Control.
- **When the monitors are unreliable at the tail.** A monitor with 99% coverage and no adaptive-attack-survival evidence covers 99% of the harm the developer imagined and none of the harm the reviewer will imagine.
- **When the deployment can bypass its own containment.** Fine-tuning APIs, plug-in stores, model-serving endpoints exposed to end users — each can widen the runtime past the EACC's scope. A Control leg that ignores these is under-defended.

## Shape 3 — Trustworthiness

### The claim shape

*"The model **will not try** to produce the unacceptable harm."*

Trustworthiness is a claim about the *model's disposition*: it does not merely not-do the harmful action because containment prevented it; it does not attempt the harmful action even given the opportunity. This is the argumentative shape that most closely corresponds to *"alignment"* in the informal sense.

### Evidence portfolio

Trustworthiness is the shape the frontier has the thinnest defensible evidence for. Do not fake this. The load-bearing evidence types:

- **Refusal robustness under adversarial pressure.** mod-104's jailbreak-suite ASR, benchmarked against HarmBench / JailbreakBench / AIR-Bench / MLCommons AILuminate, with StrongREJECT-shaped judges. Feeds a *"the model reliably refuses class-C prompts"* sub-claim, but only that.
- **Deception evaluations.** Apollo Research–style self-reasoning probes, hidden-scratchpad tests, honesty probes. Feeds a *"the model does not misrepresent its behaviour"* sub-claim. mod-110's material.
- **Alignment-faking / sandbagging tests.** Anthropic / Redwood methodology. Feeds a *"the model does not deliberately underperform when observed and overperform when not observed"* sub-claim. mod-110's material.
- **Sleeper-agent / trojan tests.** Anthropic Sleeper Agents methodology. Feeds a *"the model does not carry a triggered dangerous behaviour"* sub-claim, though absence of evidence is famously not evidence of absence here.
- **In-context scheming tests.** Meinke et al. methodology. Feeds a *"the model does not construct multi-turn plans to bypass oversight"* sub-claim.

### Assumptions

Trustworthiness arguments carry the *hardest-to-defend* assumptions in the module:

- *A-EVAL-CAPTURES-DISPOSITION.* The evaluations elicit a disposition that generalises to the deployed setting. This assumption is *badly* supported at frontier scale; treat it accordingly.
- *A-NO-TRIGGERED-BEHAVIOUR.* No specific input (a trigger phrase, a sequence of tool observations, a long-horizon plan state) induces behaviour the eval did not exercise. Sleeper-agent evidence bounds this only for the classes of trigger the eval imagined.
- *A-DISPOSITION-STABLE-UNDER-DEPLOYMENT.* The disposition observed in eval matches the disposition under deployed traffic distribution, including tool-use and multi-turn context this role has not fully characterised.

### When Trustworthiness is defensible

- **As a *contributing* leg alongside Control.** A Trustworthiness argument that pairs with a Control argument reduces the load on Control's monitors and residual-risk claim. This is the frontier-lab pattern.
- **For refusal-class claims at low tiers.** *"The model refuses the direct request in the deployed stack with ASR under 3% over the jailbreak-suite regression fixtures"* is defensible; it is a narrow Trustworthiness sub-claim about refusal robustness.
- **When mod-110 evidence explicitly stress-tests the disposition.** A Trustworthiness leg without mod-110 evidence — no deception eval, no alignment-faking test, no sandbagging test — has not been stress-tested and is not defensible at level-40 depth.

### When Trustworthiness is *not* defensible

- **As the *load-bearing* leg at high tiers.** A safety case that leans on Trustworthiness alone at ASL-3+ or Preparedness "Critical"+ is a case that has not read the frontier-lab framework docs. The frameworks explicitly do not accept it.
- **Without stress-testing.** Undertested Trustworthiness arguments are the shape reviewers reject most reliably. Chapter 05 catalogues this as its own pitfall class.
- **When the deployment surface admits triggers the eval did not model.** Long-context, tool-augmented, multi-agent deployments admit input distributions the eval did not exercise. A Trustworthiness argument that ignores the surface is under-defended.

## Shape 4 — Deference

### The claim shape

*"An **external, credible evaluator** has independently corroborated the safety claim."*

Deference is the shape that leans on another party's evidence. It is *not* an escape hatch from the other three shapes; a Deference-only case is not a case. But a Deference *leg* alongside Inability, Control, and Trustworthiness materially strengthens the top claim, because it introduces evidence the developer cannot manipulate.

### Evidence portfolio

- **AISI (UK / US) pre-deployment reports.** The load-bearing external-evaluator evidence for a UK- or US-facing deployment. The report itself is a citation; the AISI methodology is the assumption that the citation is credible.
- **Frontier Model Forum third-party evaluations.** For CBRN-uplift specifically, external bio / chem domain-expert panels.
- **Regulator-run evaluations.** EU AI Office evaluations under Article 55 / 56 <!-- needs-research: confirm the specific EU AI Office evaluation procedures citable as external evidence and the referenced Articles for GPAI evaluation duties. -->; national-body evaluations (Singapore IMDA, Japan METI-adjacent bodies) where in scope for the deployment.
- **Third-party red-team engagements.** For classes of behaviour where the developer's internal red-team's independence is limited. Ship the engagement's contract and the red-team's methodology as part of the evidence.
- **Academic peer review.** Rarely load-bearing for a specific deployment, but a citable input for methodological claims (e.g., "our elicitation-gap protocol matches the published methodology in Clymer et al.").

### Assumptions

- *A-EVALUATOR-CREDIBLE.* The external evaluator's methodology, independence, and reputation support their conclusions. AISI has this by regulatory position; a paid-third-party red-team's independence is contractually rather than institutionally established.
- *A-EVALUATOR-SCOPE-MATCHES.* The external evaluator evaluated the *deployed* system, not a variant. Pre-training evaluations do not evaluate the post-training deployed model; base-model evaluations do not evaluate the safety-tuned deployed model.
- *A-EVIDENCE-CITED-FAITHFULLY.* The case cites the external evaluator's *actual* conclusions, not a summary that changes the shape. Selective citation is one of the sharpest review-time findings.

### When Deference is defensible

- **As a *corroborating* leg alongside Inability / Control / Trustworthiness.** Deference strengthens the top claim by widening the evidence portfolio beyond the developer's own methodology.
- **When the external evaluator's methodology is version-pinned and citable.** AISI's published methodology is; an ad-hoc third-party engagement's is not, unless the engagement's methodology has been version-pinned.
- **For the *"we could not verify"* rebuttal handling.** When AISI publishes a pre-deployment report that says *"we could not verify X,"* the case has to respond directly to that finding — either by widening evidence, by conceding, or by explaining the scope mismatch. Chapter 05 details the rebuttal-handling pattern.

### When Deference is *not* defensible

- **As a *substitute* for the other three shapes.** *"AISI evaluated our model and did not flag class-C"* is not by itself an Inability, Control, or Trustworthiness argument. It is a Deference *contribution* to whichever of the three the case is authoring.
- **When the evaluator's scope did not match.** Cite the mismatch; do not paper over it.
- **When the evaluator's conclusions are downstream of your own evidence.** If the external evaluator relied on the developer's DCER without independent replication, the Deference leg's independence is thinner than it appears. Note the dependency in the case.

## Composition — how the shapes fit together

A real safety case is rarely single-shape. The load-bearing composition patterns for a frontier deployment:

- **Inability × Control.** The dominant pattern above ASL-2. Inability argues *"below the threshold under elicitation"*; Control argues *"even if the elicitation missed a capability, the runtime prevents its exploitation."* The residual-δ from Inability is what Control's containment covers; Control's assumptions bound Inability's elicitation gap.
- **Control × Trustworthiness.** The frontier-lab pattern above ASL-3. Control argues *"the deployment prevents it if the model tries"*; Trustworthiness argues *"the model does not try, per mod-110's stress tests."* Trustworthiness reduces the load on Control's monitors; Control catches the residual Trustworthiness cannot rule out.
- **Inability × Control × Trustworthiness × Deference.** The pattern for the highest-tier claims. Deference corroborates the other three legs with independent evidence. mod-112's disclosure engineering typically shapes the case for this pattern; AISI reports feed the Deference leg.
- **Control × Deference (Inability inadmissible).** When the model demonstrably has the capability (an Inability argument is not admissible), Control is load-bearing and Deference corroborates. The Goemans template's worked example is close to this shape.

Two composition rules that catch practitioners:

- **Do not double-count evidence across shapes.** The same mod-106 DCER cited both as Inability evidence and as Trustworthiness evidence does not do double duty. Cite it where it does the load-bearing work.
- **Compose the assumptions, not just the claims.** A composed case inherits the *union* of the shapes' assumptions. That union is what chapter 05's assumption-audit walks; a case whose assumption-audit misses a compositional assumption is a case that will surprise the reviewer.

## Interfaces

- **Chapter 01 (safety cases).** The shapes are how the top claim decomposes.
- **Chapter 02 (GSN).** Each shape projects onto GSN as a strategy: *"decompose by argument shape"* is the top strategy; each shape is a sub-goal.
- **Chapter 04 (authoring).** The workflow selects shapes based on tier, deployment scope, and evidence availability, then composes them.
- **Chapter 05 (pitfalls).** Each shape has characteristic pitfalls — elicitation-gap ambiguity for Inability, monitor-coverage overclaim for Control, disposition-stability overclaim for Trustworthiness, evaluator-scope mismatch for Deference. Chapter 05 catalogues them.
- **mod-104 / 105 (jailbreak / agent attack).** Feed both Inability and Trustworthiness — Inability via the ASR-under-elicitation ceiling, Trustworthiness via the refusal-robustness curve.
- **mod-106 (DCER).** Feeds Inability's load-bearing evidence.
- **mod-107 (EACC).** Feeds Control's load-bearing evidence.
- **mod-108 (Monitors).** Feeds Control's coverage evidence and Deference's third-party-calibrated corpus evidence.
- **mod-110 (Control / Deception).** Feeds Control's *"under adversarial alignment"* evidence and Trustworthiness's *"disposition stress-test"* evidence. A safety case without mod-110 evidence for Control or Trustworthiness is under-defended at level-40 depth.
- **mod-112 (Disclosure).** Consumes the composed case and shapes it for external audiences.

## Common misreadings to avoid

- **"Inability is the strongest argument, so use it whenever possible."** No. Inability is the *narrowest* shape — it commits the case to elicitation-gap accounting that most deployments cannot defensibly bound at high tiers. Above ASL-3-equivalent, an Inability-only case will be rejected. The strength of a shape depends on the tier and the deployment; there is no universal ordering.
- **"Control is a runtime concern; the safety-engineering role does not author its arguments."** No. The safety-engineering role authors the *argument* over the runtime evidence. The runtime is engineered by peer roles (mod-107 chapter 06, this module's chapter 06); the argument that the runtime *establishes safety* is this role's craft.
- **"Trustworthiness is what alignment work produces."** No. Alignment work produces *behaviour*. A Trustworthiness *argument* takes that behaviour and, via mod-110's stress tests, argues that it *generalises*. The argument is not free from the behaviour work; it is a distinct artefact grounded in it.
- **"Deference is just citing external evaluators."** No. Deference is *arguing* that the external evaluator's independent evidence corroborates the claim, under an explicit assumption about the evaluator's credibility, scope, and independence. A case that pastes an AISI docket without arguing why it is deferable is under-defended.
- **"You can pick a shape and drop the others."** For low-tier claims, sometimes. For frontier claims, no — the tier language of RSP / Preparedness / FSF prescribes the shapes; a case that ignores the prescription will be rejected. When in doubt, compose.
- **"The composition is straightforward — just union the evidence."** No. The composition unions the *assumptions* as well as the evidence, and unresolved compositional assumptions are one of the sharpest review-time findings. Chapter 05 walks the assumption-audit.

## Summary

- **Four argument shapes**: **Inability** (the model *cannot* do the harm), **Control** (the deployment *prevents* the harm even if the model tries), **Trustworthiness** (the model *will not try*), **Deference** (an external evaluator *corroborates* the claim).
- Each shape has a distinct **evidence portfolio**, **assumption set**, and **rebuttal surface**. Get the shape wrong and the review will find it in the first pass.
- **Inability** is defensible below ASL-3-equivalent and for narrow, well-scoped harm classes; it stakes the argument on mod-106's elicitation-gap accounting.
- **Control** is the load-bearing shape at higher tiers; it stakes the argument on mod-107's EACC, mod-108's monitor coverage, and — decisively at level 40 — mod-110's control evaluation.
- **Trustworthiness** contributes to the argument but is rarely load-bearing at frontier tiers; it stakes the argument on mod-104's refusal-robustness and mod-110's deception / alignment-faking / sleeper-agent stress tests.
- **Deference** corroborates the other three shapes with independent evidence, most importantly AISI pre-deployment reports. A Deference-only case is not a case.
- Real safety cases *compose* shapes. Compose the evidence *and* the assumptions; chapter 05 walks the assumption-audit pattern that catches compositional gaps.
- Chapter 04's authoring workflow selects, composes, and grounds the shapes end-to-end for a concrete deployment. Exercise 02 drills the composition on a worked example.
