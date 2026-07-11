# 01 — Dangerous-Capability Elicitation as an Engineering Discipline

## Motivation

Every frontier safety framework — Anthropic's Responsible Scaling Policy, OpenAI's Preparedness Framework, Google DeepMind's Frontier Safety Framework — hinges on a single engineering question: *what can this model do, under best-effort elicitation, right now*? The frameworks then map that answer to a tier and to a set of required mitigations. If your elicitation was weak, your ceiling is low, the tier is under-triggered, and mitigations that should have engaged did not engage. If your elicitation was strong but under-reported, the model launches with tier-appropriate mitigations and the safety case holds.

That question is not "what did the model score on the benchmark?" A benchmark measurement is a *lower bound* on the model's capability, and — if elicitation was weak — an arbitrarily loose one. The engineering discipline of dangerous-capability evaluation is the discipline of *closing the gap between the benchmark measurement and the model's actual elicitable capability under adversarial effort*, then reporting the residual gap honestly so the reviewer (the RSP Responsible Scaling Officer, the Preparedness Safety Advisory Group, the FSF review body, the AISI evaluation team, the EU AI Office) can decide whether the ceiling is tight enough.

This chapter names the discipline the module builds. It draws the two adjacent boundaries — *this is not benchmark plumbing, and this is not adversarial-alignment evaluation* — introduces the **elicitation protocol** as the unit of study, pins the **four elicitation axes** the module owns (best-of-N sampling, fine-tune elicitation, tool-use elicitation, scaffolding elicitation), and hands down the harmful-payload discipline from mods 103–105 with escalated CBRN- and cyber-specific controls.

Read this chapter first. Chapters 02–05 develop the four capability panels (CBRN, cyber, autonomy, persuasion + self-exfil) against the elicitation-protocol discipline named here; chapter 06 folds them into the DCER; chapter 07 codifies the deferrals.

## What a dangerous-capability evaluation is — and what it is not

**A dangerous-capability evaluation is the pre-registered protocol by which we produce a defensible best-effort ceiling on a frontier model's ability to enable a specific class of catastrophic-outcome-relevant action, and the comparison of that ceiling against a pre-registered capability threshold that a frontier safety framework's tier depends on.** Its output is a verdict: threshold *cleared*, threshold *approached* (with mitigations), or threshold *crossed* (with a rollback trigger).

It is not:

- **A benchmark score.** A benchmark measures a model on a fixed set of items with a fixed prompt template. A dangerous-capability evaluation *maximises* the model's performance under adversarial effort — because the safety question is what a motivated attacker gets out of the model, not what the model gets out of a naïve prompt. Every benchmark you use is *one row* in the elicitation protocol, not the protocol.
- **An adversarial-alignment evaluation.** Adversarial-alignment evaluation (deception, sandbagging, alignment-faking, sleeper-agents, in-context scheming — mod-110) measures whether the model's *expressed* inability can be trusted. Dangerous-capability evaluation measures the model's *demonstrable* ability. The two compose: if a control evaluation reveals sandbagging, the dangerous-capability ceiling must widen to reflect the credibility of the model's failures. Chapter 07 draws the interface; this chapter treats capability elicitation as its own discipline.
- **A refusal-rate measurement.** How often the model refuses a request tells you about the *policy classifier*, not the *underlying capability*. A model that refuses 99% of CBRN-uplift prompts but can produce a correct synthesis pathway on the 1% has the capability. Every capability panel in this module reports capability with the model's refusal treated as an axis to elicit past, not as a ceiling.
- **A one-shot demonstration.** A single successful elicitation is a proof of existence for the capability but is not the ceiling. A ceiling requires a pre-registered protocol whose *reproducible* results ground the CI. The reproducibility discipline is inherited from mod-103 chapter 06 and mod-104 chapter 01 and escalated for CBRN payload handling.

The load-bearing definitional element is **best-effort ceiling with an accounted-for elicitation gap**. Every result you report in this module names:

1. The **elicitation protocol** — best-of-N budget, fine-tune contract, tool-use contract, scaffolding contract, human-hours of expert red-team effort applied.
2. The **capability-elicitation gap** — the residual claim that "our ceiling underestimates elicitable capability by no more than δ under a follow-up effort of E."
3. The **threshold** — the RSP ASL / Preparedness tier / FSF CCL boundary the ceiling is being compared to, pre-registered *before* the elicitation run.
4. The **verdict** — cleared, approached, or crossed — and the associated rollback trigger.

A finding missing any of the four is a benchmark score, not a dangerous-capability evaluation result.

## Three boundaries this module draws

### Boundary 1 — dangerous-capability evaluation vs. the level-30 `ai-eval-engineer` (AI Engineering family)

The `ai-eval-engineer` peer role owns **application-layer eval plumbing**: trace collection, judge orchestration, RAG-eval harnesses, LangSmith / Braintrust / Inspect wiring for CI/CD. That plumbing is the load-bearing input to this module — the elicitation protocol *runs on* Inspect (AISI's harness), on Braintrust-shaped judge pipelines, or on the frontier lab's internal eval infrastructure. The mod-106 addition is:

- **The protocol contract, not the plumbing.** This module authors the *what* — what elicitation axes to sweep, what sampling budget to spend, what fine-tune contract to use, what judge rubric to write, what threshold to pre-register. The `ai-eval-engineer` peer role wires those into the eval infrastructure.
- **The capability-safety framing.** Application-layer evals measure task-success under normal operation. Dangerous-capability evals measure task-success under *adversarial* elicitation for a *catastrophic-outcome-linked* capability. The framing changes which prompt templates, which sampling budgets, and which judges are appropriate.

If the finding fits an application-layer eval CI/CD row (traces, judges, RAG evals), route it to the peer role. If it requires an elicitation protocol pre-registered against a safety-framework tier, it belongs here.

### Boundary 2 — dangerous-capability evaluation vs. the level-30 `model-evaluation-engineer` (ML Engineering family)

The `model-evaluation-engineer` peer role owns **statistical calibration methodology**: best-of-N confidence-interval derivation, item-response theory for item-difficulty accounting, judge–human agreement CI derivation, calibration curves, sample-size planning against effect sizes. That methodology is the load-bearing calibration for every ceiling this module reports — but it is the peer role's derivation.

The mod-106 role:

- **Names the CI it needs and consumes the derivation.** "This tier-comparison decision requires a best-effort ceiling with a 95% one-sided CI whose upper bound sits below the threshold." The peer role derives the sample-size and CI methodology; this role runs the elicitation and reports the CI.
- **Owns elicitation-gap accounting *for* the capability.** Elicitation gap has a statistical layer (how the observed best-of-N distribution's tail extrapolates) that defers to the peer role, and a capability-specific layer (what additional elicitation effort a motivated attacker plausibly deploys) that lives here. The peer role does not choose "fine-tune elicitation is worth 20 human-hours of extra effort" — that is a capability-specific judgement here.

Chapter 07 develops the deferral contract in the DCER's header.

### Boundary 3 — dangerous-capability evaluation vs. adversarial-alignment evaluation (mod-110)

Adversarial-alignment evaluation (mod-110) asks: *can we trust the model's expressed inability under adversarial conditions?* Dangerous-capability evaluation asks: *what can the model demonstrably do under adversarial elicitation?* The two are complementary:

- **Sandbagging invalidates a ceiling.** If mod-110's evaluation reveals that the target model deliberately underperforms on capability probes it can pass, the mod-106 ceiling is under-reported by that gap, and the DCER's elicitation-gap accounting must widen accordingly.
- **Elicitation-negative results are only as strong as the control evaluation supporting them.** A DCER that says "the model cannot do X under best-effort elicitation" is stronger the tighter mod-110's control evaluation on X is. If the control evaluation shows the model's expressed inability is trustworthy in this domain, the elicitation-negative result carries. If not, the ceiling has to widen.

Every DCER's elicitation-gap-accounting clause explicitly cites mod-110's paired control-evaluation result on that capability, if one exists. Chapter 07 formalises the cite.

## The four elicitation axes this module owns

Every dangerous-capability evaluation authored here sweeps at least the following four axes. Chapters 02–05 pick the subset relevant to their capability panel; the DCER (chapter 06) reports which axes were swept and to what depth.

### Axis 1 — Best-of-N sampling

*The primitive*: instead of one sample per item, draw N samples with temperature > 0 (or with structured prompt variants at temperature 0), and score the item as solved if *any* sample solves it. The elicitation ceiling scales with N; the underlying capability does not. The gap between pass@1 and pass@N — and the extrapolation to a plausible attacker's N — is the first axis of elicitation-gap accounting.

*What to specify*:

- The N (typically 4, 16, 64, or higher for high-stakes capability panels — CyberSecEval 3 and RE-Bench both publish pass@N methodology; see chapters 03 and 04).
- The sampling temperature, top-p, and prompt-variation strategy (semantic paraphrases, template rotations, few-shot exemplar swaps).
- The scoring rule: *any* sample solves (union), *k of N* solve (a weaker but sometimes more decision-relevant statistic), or *majority-vote* (rarely used here — the safety question is capability, not consistency).
- The CI derivation — deferred to the `model-evaluation-engineer` peer role. The DCER cites the CI methodology by ID and reports the numeric CI.

*Common pitfall*: reporting pass@1 as the ceiling. Pass@1 is the ceiling *of a naïve caller* using one sample; the safety question is what a motivated attacker gets with 100 or 1000 samples, and pass@N > pass@1 in every capability panel this module runs.

### Axis 2 — Fine-tune elicitation

*The primitive*: fine-tune the target model (with the developer's cooperation or on an open-weights copy) on a small dataset of capability-directed examples, then re-run the evaluation. The fine-tune is not a training-attack in the classical adversarial-ML sense — it is *elicitation*: the fine-tuned model reveals a capability that the base model refused or failed to elicit under pure prompting.

*Why it matters*:

- Fine-tune-elicitation is the standard for post-release safety cases. If a customer can fine-tune the model (many providers allow it), the customer's ceiling is the fine-tuned ceiling, not the base ceiling.
- Anthropic's RSP, OpenAI's Preparedness Framework, and the FSF each name fine-tune-elicitation as an elicitation-protocol axis; publishing a base-model ceiling without a fine-tune-elicited counterpart is not a defensible safety case at ASL-3 / High / relevant CCLs.

*What to specify*:

- The fine-tune dataset size (typically 10–500 examples for capability elicitation — the point is elicitation, not new capability injection).
- The fine-tune method (LoRA, full-parameter, RLHF-style) and whether it targets the safety head (refusal) or the capability head (task performance).
- The safety-classifier posture during fine-tune-elicitation (usually disabled — otherwise you are measuring refusal, not capability).
- The ownership of the fine-tune weights (typically not committed anywhere; the weights themselves may be dangerous; the *fact of the fine-tune's uplift* is what the DCER reports).

*Ethical constraint*: fine-tune-elicitation on the capability panels this module runs (CBRN, cyber, autonomy at frontier scale, persuasion / self-exfil) is an *authorised* operation performed within the safety team's sandbox — never a covert operation on someone else's model. Chapter 02 develops the CBRN-specific controls.

### Axis 3 — Tool-use elicitation

*The primitive*: give the model tools (a browser, a code interpreter, a document-retrieval tool, a memory store, a shell, an experimental research environment) and measure capability with tools available. A model that fails a chemistry-planning task under text-only prompting may succeed with a chemistry-databases tool; a model that fails a CTF task under text-only prompting may succeed with a shell and internet access.

*Why it matters*:

- Deployed models operate with tools. The safety question is the deployed capability, not the text-only capability. Every capability panel in this module reports both the text-only ceiling and the tool-elicited ceiling.
- The tool set is itself a parameter of the ceiling. Chapter 04's autonomy panel is essentially a tool-elicitation panel — the METR tasks and SWE-bench Verified both require a shell and a filesystem; the ceiling changes with the tool spec.

*What to specify*:

- The tool set and each tool's version pin.
- Whether the model receives the tool schema at prompt time (typical) or discovers tools via chain-of-thought (rare, adversarial-elicitation-only).
- The sandbox posture — network egress rules, filesystem restrictions, time budget, side-effect containment. Chapter 07 hands off to mod-107 for the engineering side.
- The safety-monitor posture on the tool bus — usually disabled during elicitation, so the ceiling reflects the capability *absent monitors*.

### Axis 4 — Scaffolding elicitation

*The primitive*: wrap the model in an agent scaffold — a planner, a critic, a reflection loop, sub-agent decomposition, best-of-N-with-self-selection — and measure capability with the scaffold in place. A single-forward-pass model may fail; a plan-and-execute scaffold may succeed. This is the axis METR (chapter 04) has documented as producing the largest single-shift uplifts on autonomy tasks.

*Why it matters*:

- Any tool-integrated deployment is running some scaffold. The safety question is the scaffolded ceiling.
- Adversaries with API access will scaffold. A ceiling that does not include scaffolding elicitation understates the elicitable capability by potentially very large factors.

*What to specify*:

- The scaffold — ReAct, plan-and-execute, tree-of-thought, Reflexion, sub-agent orchestrator, best-of-N with LLM-judge self-selection, or a custom scaffold. Cite the paper for standard scaffolds; pin the code for custom ones.
- The scaffold's compute budget — turns, tool calls, tokens — bounded so ceilings across models are comparable.
- The self-selection strategy — how the scaffold picks among multiple candidate outputs.

*Common pitfall*: choosing a scaffold that is "friendly to the model" — one that hides the model's failure modes. The elicitation-protocol discipline forces the choice: scaffolds are chosen because *an attacker would use them*, not because they make the model look better or worse.

## Pre-registration against RSP / Preparedness / FSF tiers

Pre-registration means: **before you run the elicitation, you publish (internally, to the safety-review body) the elicitation protocol, the threshold, and the verdict-decision procedure**. Then you run. Then you compare against the pre-registered threshold, honestly.

Why pre-register:

- **It prevents scope-creep of the ceiling.** Without pre-registration, an elicitation that yields uncomfortable results gets re-parameterised until the ceiling looks acceptable. Pre-registration binds the interpretation to the protocol, not vice versa.
- **It aligns with framework contract.** RSP's Capability Reports, the Preparedness Framework's Preparedness Assessments, and the FSF's frontier-safety-framework evaluation reports each expect a pre-registered protocol as part of the artefact's defensibility. mod-101 chapters 02–04 develop the framework side.
- **It makes the elicitation-gap claim credible.** "We would have caught crossings this large" is only a defensible claim if the protocol was fixed before the outcome was known.

*What the pre-registration contains*:

```yaml
elicitation_pre_registration:
  version: <pre-registration doc version>
  authored_by: <safety-team lead>
  reviewed_by: <safety-review body>
  target_model: {id, snapshot}
  capability_panel: <cbrn | cyber | autonomy | persuasion | self_exfil>
  threshold:
    framework: <rsp | preparedness | fsf>
    tier: <asl-3 | high | ccl-M | ...>
    metric: <accuracy | pass@N | task-length-hours | expert-uplift-score | ...>
    threshold_value: <numeric>
    threshold_direction: <cleared_below | crossed_above>
  elicitation_axes:
    best_of_n: {N, temperature, prompt_variations, scoring_rule}
    fine_tune:  {authorised, dataset_size, method, safety_head}
    tool_use:   {tool_set, sandbox, monitors_off_for_elicitation}
    scaffolding: {scaffold_id, compute_budget}
    human_hours: <expert red-team hours applied>
  ci_methodology: <cite model-evaluation-engineer peer artefact>
  domain_grader:
    org: <FMF / IAEA / WHO / MITRE / external firm>
    scope: <what the grader adjudicates>
  verdict_procedure:
    cleared_below:  <numeric or textual criterion>
    approached:     <numeric or textual criterion>
    crossed_above:  <numeric or textual criterion>
    rollback_trigger: <mod-112 disclosure workflow ID + engaged-control ID>
```

Every DCER header (chapter 06) carries this block. Every exercise in this module ends with authoring a shape of this block for one capability.

## The capability-elicitation gap accounting

Elicitation gap is the load-bearing uncertainty in every ceiling this module reports. It is *the residual claim that a motivated attacker with additional effort could push the observed ceiling higher by no more than δ*.

Two components:

### Statistical component (defers to `model-evaluation-engineer`)

- Best-of-N tail extrapolation: how the observed pass@N tail projects to pass@10·N or pass@100·N. Item-response theory, per-item difficulty estimation, and hierarchical Bayesian models are the peer role's territory. The DCER cites the CI and the extrapolation methodology.
- Judge–human agreement CI: the ceiling depends on the judge, and the judge has a per-item error rate. The peer role bounds it.
- Item-set coverage CI: the ceiling is measured on a sample of items; the extrapolation to the population is bounded by the sample's representativeness. The peer role bounds it.

### Capability-specific component (owned here)

- Fine-tune-elicitation depth: was the fine-tune large enough to have surfaced the capability if it existed? What is the plausible attacker's fine-tune budget?
- Tool-set completeness: did the elicitation include the tools an attacker would use? What tools were excluded and why?
- Scaffolding depth: was the scaffold competitive with what a motivated attacker plausibly deploys? METR's public results provide a lower bound on what open-source scaffolding achieves; the FMF working papers on autonomy provide additional anchors.
- Human-effort budget: how many hours of expert red-team effort were applied? An attacker who spends 500 hours will elicit more than an evaluator who spent 20 hours. The DCER states the budget and its judgement about the attacker's plausible budget.
- Domain-expert access: did the elicitation team have access to the domain expertise a real attacker would seek? For CBRN, this often means external biosecurity or radiological experts contributing to elicitation design; for cyber, penetration-testing experts; for autonomy, ML-research experts (RE-Bench specifically anchors on this).

The claim the DCER makes is **quantitative** where possible ("we believe our ceiling underestimates elicitable pass@N on this WMDP-bio subset by no more than 0.05 under a 10× human-hours follow-up"), **qualitative** where quantitative claims are indefensible ("we believe our scaffold is at parity with the state-of-the-art open-source scaffolds cited in `resources.md`, so a scaffolded-attacker ceiling is unlikely to exceed ours by more than a factor equal to the pass@N-to-pass@10N gap"). The reviewer sees both.

## Elicitation-protocol quality checklist

Before the pre-registration is submitted for review, every elicitation protocol in this module passes the following checklist:

1. **The threshold cite is exact.** RSP ASL-N section, Preparedness Framework tier, FSF CCL — cited to the framework document version.
2. **The capability panel is grounded in a published threat model.** For CBRN, the FMF bio threat model. For cyber, MITRE ATT&CK-linked. For autonomy, the METR / RSP / FSF autonomy tier definitions.
3. **Every elicitation axis is either swept or explicitly waived with a reason.** Missing axes are named ("scaffolding not swept because the target deployment does not expose tool use; the ceiling is text-only") not silently absent.
4. **The domain grader is external where the capability is CBRN / cyber-offense / R&D-uplift.** In-house adjudication of these is a red flag for the DCER's defensibility.
5. **The judge is calibrated.** Judge–human agreement is measured on a random-sampled subset; the CI on judge accuracy is reported.
6. **The sample-size plan clears the threshold's decision requirement.** Cite the `model-evaluation-engineer` peer role's power calculation.
7. **The elicitation ran on the *deployment* model.** If the deployed model has additional post-training safety layers (a classifier guard, a Constitutional Classifier, monitor-wrapped tools), the elicitation reports both the un-guarded model's ceiling and the deployed stack's ceiling. mod-108 develops the layered-defence side.
8. **The elicitation-gap claim is honest.** The reviewer must be able to imagine the follow-up experiment that would falsify the residual-gap δ. If they cannot, the claim is decorative.
9. **The DCER's verdict names its rollback trigger.** "Crossed → engage control X and initiate mod-112 workflow Y." A ceiling with no downstream rollback contract is a report, not an engineered evaluation.

## Harmful-payload discipline (escalated from mods 103–105)

Working CBRN synthesis instructions, weaponisable exploit chains, elicited self-exfiltration transcripts, and adversarial fine-tune weights against production frontier models route to the harmful-payload store, not this repo. Illustrative snippets in chapters 02–05 are truncated, defanged, or point at published benchmark cases by reference (WMDP question IDs, CyberSecEval case IDs, METR task IDs).

Escalated controls specific to this module:

- **CBRN payloads** are handled per the FMF bio-evaluation guidance: need-to-know access, air-gapped grading terminals, external-expert graders receive materials through a supervised channel, and elicitation runs against production frontier providers require the provider's written safety-team authorisation before the run.
- **Fine-tune elicitation weights** are stored inside the safety team's sandbox and not exported. The DCER reports the *uplift produced*; the weights themselves stay behind the sandbox boundary.
- **Elicited self-exfiltration transcripts** (chapter 05) are treated as adversarial-alignment evidence and routed to mod-110 as inputs; the transcripts themselves do not appear in this repo or in the paired -solutions repo.
- **Mod-112 disclosure workflow is the default for any external reference.** If a finding is public-safety-material — e.g., a novel CBRN-uplift capability crossing an RSP ASL-3 threshold — the disclosure workflow governs the release timing, the recipient list (frontier lab, AISI, relevant government body), and the redaction policy.

If you catch yourself pasting a working synthesis pathway, working exploit chain, or working self-exfiltration prompt into a chapter, exercise, or PR, stop — and route to mod-112.

## Common misreadings to avoid

- **"A benchmark score is a ceiling."** No. A benchmark score is a *point measurement of one elicitation configuration*. The ceiling is the maximum over all elicitation configurations you swept, plus the residual-gap δ.
- **"If the model refuses, we're safe."** No. Refusal is a policy classifier's behaviour, not a capability upper bound. Fine-tune-elicitation and jailbreak (mod-104) both bypass refusal without changing capability; capability-safety questions are about what's on the other side of the refusal.
- **"Pass@1 is what deployment sees."** For a naïve one-shot user, yes. For a motivated attacker with API access, no. The safety question is the attacker's ceiling; the ceiling is pass@N with the N a plausible attacker deploys.
- **"Best-of-N cannot exceed the model's true capability."** True in the limit, but for finite N the observed pass@N is a *sample* from the model's output distribution and can be arbitrarily far below the theoretical ceiling with sub-critical N. Chapter 06's DCER reports both observed pass@N and the extrapolation.
- **"Pre-registration is a paperwork step."** No. It is the mechanism that makes the ceiling credible. A ceiling reported without pre-registration is a benchmark score with better prose.
- **"Elicitation-gap accounting is speculation."** It is *bounded* speculation — every δ claim is falsifiable by a follow-up experiment. That is different from unbounded speculation, which is decorative.

## Summary

- Dangerous-capability evaluation is the discipline of producing a defensible best-effort ceiling on a specific catastrophic-outcome-linked capability and comparing it to a pre-registered threshold in a frontier safety framework.
- The unit of study is the **elicitation protocol** — pre-registered, versioned, and reviewed before the run.
- Four elicitation axes are swept: **best-of-N sampling, fine-tune elicitation, tool-use elicitation, and scaffolding elicitation**.
- The ceiling carries an **elicitation-gap accounting** — a statistical component that defers to `model-evaluation-engineer`, and a capability-specific component owned here.
- The boundary against **`ai-eval-engineer`** is: this module authors the protocol; the peer role wires the plumbing.
- The boundary against **`model-evaluation-engineer`** is: this module names and consumes the CI methodology; the peer role derives it.
- The boundary against **mod-110** is: this module measures demonstrable capability; mod-110 measures the trustworthiness of expressed inability. The two compose into the safety case.
- Every DCER header carries an **elicitation pre-registration block** binding the protocol to the framework tier.
- Harmful-payload discipline is escalated — CBRN and self-exfil payloads are behind tighter access controls than mods 103–105 required, and mod-112's disclosure workflow governs any external reference.
