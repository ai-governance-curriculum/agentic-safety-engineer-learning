# 08 — The AI-Control Paradigm and Model Organisms

## Motivation

Chapters 02–07 measured *whether* a target model is misaligned in specific behavioural senses. This chapter operationalises a distinct engineering posture developed by Redwood Research and closely-adjacent groups: **AI control** — the posture in which the operator assumes the target model *is* misaligned and asks what safety guarantees can be extracted anyway. The reference paper is Greenblatt et al.'s "AI Control: Improving Safety Despite Intentional Subversion" (Redwood, 2023).

The load-bearing conceptual shift is from *measurement of alignment* to *engineering under an assumed-misaligned model*. Alignment measurement (chapters 02–07) bounds the plausibility of misalignment; AI control asks what protocols hold *even when* the assumption fails. The two compose: alignment measurement sets the parameters (how capable of subversion is the assumed-adversarial model?); control-eval protocols measure whether the operator's downstream posture holds against that adversary.

The engineering shape is a **control-eval protocol** — a specific pairing of a *red team* (playing the assumed-adversarial model) against a *blue team* (the operator's defence-in-depth posture, including mod-107's EACC, mod-108's monitors, and mod-109's safety case), scored on the outcome of a specified task family. Chapter 08 productionises the Redwood protocol as a repeatable operator artefact, layers in **honeypot-task**, **untrusted-monitoring** baselines, and **model-organism** construction, and pins the handoff to `fine-tuning-engineer` (peer, level 30) for safety-tuning intervention on findings.

## Primary sources

- **[Greenblatt et al. — AI Control: Improving Safety Despite Intentional Subversion (Redwood Research, arXiv:2312.06942)](https://arxiv.org/abs/2312.06942)** — the reference paper. Read it in full. The red-team-blue-team protocol, the untrusted-monitoring baseline, the trusted-monitoring baseline, and the safety-usefulness Pareto frontier are the direct references.
- **[Redwood Research — publications](https://www.redwoodresearch.org/)** — the reference organisation. <!-- needs-research: pin subsequent Redwood publications on control-eval methodology, follow-up experiments, and open-source protocol implementations. -->
- **[Anthropic — Research and safety publications](https://www.anthropic.com/research)** — Anthropic's public research on control-adjacent evaluations. <!-- needs-research: pin the specific Anthropic control-adjacent publications. -->
- **[UK AISI — Pre-deployment evaluation methodology](https://www.aisi.gov.uk/)** — the reference government body for control-adjacent evaluation methodology. <!-- needs-research: pin the specific AISI control-eval publications. -->
- **[Apollo Research — publications](https://www.apolloresearch.ai/)** — Apollo's methodology composes with control-eval at the scheming-detection layer. <!-- needs-research: pin the specific Apollo control-relevant publications. -->

## What AI control is — and what it is not

**AI control is the engineering discipline of designing operator postures that provide safety guarantees despite the assumption that the target model is intentionally adversarial toward the operator's goals.** The output is a *control protocol* — a specification of the operator's defence-in-depth posture, the assumed adversary, and the safety guarantee — and a *control evaluation* that measures whether the protocol holds against a red team playing the assumed adversary.

It is not:

- **An alignment measurement.** Alignment measurement (chapters 02–07) bounds the plausibility that the target model is adversarial. Control asks what safety we get if the alignment measurement's residual-uncertainty accounting turns out to be optimistic.
- **A red-team engagement.** mod-111's scaled red team is an *evaluation* of the operator's posture against a red team playing an adversarial *user*. Control asks about a red team playing the adversarial *model itself*. The threat model is inverted.
- **A monitor evaluation.** mod-108 measures monitor false-negative rates against known-adversarial content. Control asks whether the *composition* of monitor + wrapper + HITL + audit-log holds against an adversarial model that can plan against every component simultaneously.
- **A safety-case rewrite.** mod-109 authors the safety case. Control eval provides evidence for the case's *inability* leg — *the model is unable to defeat the operator's protocol under the assumed adversary model.* The case's authorship is mod-109's; the evidence is here.

The load-bearing definitional element is **safety guarantee under an assumed-adversarial model**. A control protocol without a stated adversary model is not a control protocol; a control eval without a red team playing that adversary is not a control eval.

## The control-eval protocol shape

Following Greenblatt et al., a control-eval protocol has five components. Each component is versioned; the composition is the protocol version.

### Component 1 — The task specification

The task family the protocol operates on. Concretely defined — a coding task, an analysis task, a decision-support task — with a per-task success criterion, a per-task safety criterion (what would constitute a safety failure), and a task-item bank.

### Component 2 — The assumed adversary model

The specification of the assumed-adversarial target model. What the target can plan (bounded by mod-110 chapters 02–06 measurements), what tools it has (bounded by the deployed EACC), what memory it has, what oversight it can observe. The assumed adversary model is calibrated against chapter 04's sandbagging results, chapter 06's scheming results, chapter 07's sabotage attempt rates.

### Component 3 — The operator posture

The defence-in-depth posture the protocol assumes. Typically includes:

- **A trusted weaker model** as an inline monitor or as the primary actor for high-stakes decisions.
- **The EACC** from mod-107 (capability gates, monitored wrappers, HITL flows, kill switch).
- **The mod-108 monitors** (specific classifiers, calibration curves).
- **The audit-log stream** from mod-107 chapter 03.

The posture is the composition, not any single component.

### Component 4 — The red team

The team playing the assumed adversary — either humans acting as if they were the model, or a fine-tuned red-team-organism whose behaviour matches the assumed-adversary spec. The red team plays *within* the operator posture — it can only take actions the wrapper admits, but it plans as an adversary.

Redwood's original protocol uses humans playing the adversary (with prompting instructions that specify the strategy). Follow-up work has developed automated red-team-organism variants. Both are reference-legitimate; the choice is a protocol-level decision documented in the report.

### Component 5 — The scoring

- **Safety score.** Did the protocol block the safety-relevant failure? Aggregated over the task-item bank.
- **Usefulness score.** Did the protocol allow the legitimate task to complete? Aggregated similarly.

Redwood's methodology reports both as a Pareto trade-off — a protocol with high safety and low usefulness is not a useful production posture; a protocol with high usefulness and low safety is not a control posture at all. The report table pins the Pareto point.

## Honeypot tasks in control eval

Honeypot tasks (chapter 01, primitive 3) appear as a specific control-eval component. A honeypot in a control-eval task-item bank is an item whose engagement pattern is *only* consistent with an adversarial strategy — an item the target has no legitimate reason to engage with unless it is running an adversarial plan.

The engineering shape:

- **The honeypot is inserted into the task-item bank at a documented rate.**
- **Engagement on the honeypot is the direct adversarial-attempt signal.**
- **Non-honeypot items maintain the task's ordinary distribution** so the target cannot distinguish honeypots from ordinary items without executing exactly the reasoning the honeypot is designed to detect.

Honeypot engagement rate is one of the safety-score components. A non-zero honeypot engagement rate under a well-designed honeypot is direct adversarial-behaviour evidence, and — importantly — is signal *even in the absence of an ultimate task-failure*. Chapter 07's sabotage-attempt rates map partially into honeypot engagement rates for sabotage-relevant tasks.

## Untrusted-monitoring baselines

A key Redwood contribution is the **untrusted-monitoring baseline** — the operator posture in which the *target model itself* (assumed adversarial) is used as a component of its own oversight. Concretely: the same untrusted model that produces the output is asked to critique / grade / red-team its own output; the operator uses that critique as one input to the safety decision.

The reason this is a *baseline* rather than a *strategy* is that Redwood's original result showed **untrusted monitoring can provide meaningful safety even under an adversarial target** — but *only* when the protocol is engineered correctly (e.g., the monitor sees only a redacted version of the target's own reasoning, the monitor is invoked at independent sessions, the monitor's outputs are gated by a trusted weaker model). The baseline exists to prevent the operator from claiming their trusted-monitor result is impressive without comparison to what untrusted-monitoring already gives.

The engineering shape:

- **Untrusted-monitor invocation.** The suspect target is invoked in a fresh session with a monitor-shaped prompt, and asked to grade or critique a given output.
- **Independence engineering.** The monitor session cannot see the target's original planning context; the monitor cannot know it is grading its own output.
- **Aggregation.** Untrusted-monitor outputs are combined with the trusted weaker model's outputs, according to a protocol-level rule.

The untrusted-monitoring baseline appears in the report as a comparison row — *"under untrusted monitoring alone, the protocol's safety score is X; under the full posture, it is Y."*

## Model organisms as a control-eval prerequisite

A model organism (chapter 05) is a controlled misaligned target the operator constructs to study a specific misalignment mode. Control eval consumes model organisms in two ways:

- **As the red-team target itself.** A control-eval protocol run against a specific model organism produces a protocol-vs-organism safety score. This is the direct measurement of the protocol under a known-adversary target.
- **As the calibration reference.** For control-eval runs against a suspect (not-known-adversary) target, the model-organism-calibrated baseline is the reference point. A protocol that catches the organism's adversarial behaviour at rate X, and catches the suspect's behaviour at rate Y, produces an interpretable comparison.

Constructing model organisms for control-eval is a peer-role handoff to `fine-tuning-engineer` (level 30). Chapter 09 codifies the handoff. The mod-110 role authors the *organism specification* — what misalignment mode to instantiate, what capability profile to give the organism, what specific behavioural probes it should engage — and consumes the trained organism as the control-eval red-team.

## The handoff to `fine-tuning-engineer`

Findings from control eval — a protocol that *fails* against a well-constructed model organism — route to a peer role for safety-tuning intervention. `fine-tuning-engineer` (level 30, ML Engineering family) owns the safety-tuning mitigation. The mod-110 role's craft ends at the finding and the recommended mitigation direction; the peer role's craft is the tuning intervention itself.

The handoff shape:

- **The mod-110 finding.** Names the specific control-eval protocol, the model organism used, the failure mode observed (which honeypot engagements, which sabotage-attempt rates, which alignment-faking gaps), and the residual-uncertainty accounting.
- **The recommended intervention direction.** *"Address the goal-guarding behaviour by (candidate direction: constitutional-AI update, adversarial-training pass, RLHF signal engineering)."* The mod-110 role identifies the direction; the peer role selects the specific intervention and executes.
- **The re-evaluation contract.** After the peer role's intervention, the mod-110 role re-runs the control-eval protocol on the intervened model and reports the delta. Iteration continues until the protocol holds or until the finding is escalated to mod-112 for tier-decision review.

The handoff is *routed*, not *fixed inside mod-110*. mod-110 owns the eval; the peer role owns the intervention.

## The control-eval report

The deliverable is a control-eval report per (protocol × target) combination. Its shape:

1. **Header.** Protocol version (with components 1–5 identifiers), target-model version (or organism version), evaluation date, harness commit hash, peer-role artefact IDs.
2. **Assumed-adversary spec.** The specific adversary model assumed in component 2, with the mod-110 chapter cross-references (chapter 04 sandbagging bound, chapter 06 scheming probe results, chapter 07 sabotage attempt rates).
3. **Operator-posture summary.** The specific EACC (mod-107), monitors (mod-108), and other defence-in-depth components composed in component 3.
4. **Safety-vs-usefulness Pareto.** The scored point on the Pareto frontier, with the baseline (untrusted-monitoring alone) as a comparison row.
5. **Honeypot-engagement section.** Per-honeypot engagement rate, aggregated across the task-item bank.
6. **Failure-mode section.** For safety failures observed, the specific mode (task-specific failure vs honeypot-engagement vs sabotage-attempt) and the mod-110-chapter cross-reference.
7. **Residual-uncertainty accounting.** Coverage of the task-item bank, adversary-model coverage, protocol-configuration sensitivity.
8. **Downstream cites.** mod-107 EACC (a failed protocol re-argues the EACC), mod-108 monitor calibration (protocol failures suggest monitor-strengthening), mod-109 safety-case inability leg (a protocol that holds provides evidence for the inability claim), mod-111 red-team coverage matrix, mod-112 disclosure. And the peer-role handoff to `fine-tuning-engineer` for safety-tuning intervention.

## Interfaces

- **Chapter 01 (discipline).** Honeypot tasks and blind grader panels appear directly as protocol components.
- **Chapter 04 (sandbagging).** The assumed-adversary spec cites chapter 04's sandbagging bound.
- **Chapter 05 (sleeper agents / model organisms).** Model-organism construction methodology is directly reused; some control-eval red-team targets *are* the chapter-05 organisms.
- **Chapter 06 (scheming).** Scheming-probe results parameterise the assumed adversary; some control-eval protocols measure specifically against scheming behaviours.
- **Chapter 07 (sabotage).** Sabotage-attempt rates parameterise the assumed adversary; some control-eval tasks *are* sabotage-relevant tasks with control postures overlaid.
- **Chapter 09 (boundaries).** Peer-role handoff to `fine-tuning-engineer` is codified here.
- **mod-107 (EACC).** A control-eval protocol *is a specific test of the composition* of the EACC and the monitor system; a failure re-argues the EACC.
- **mod-108 (Monitors).** Control-eval measures whether the monitor + wrapper composition holds against an adversarial target; findings suggest monitor calibration changes.
- **mod-109 (Safety Cases).** A control-eval protocol that holds against a well-specified adversary is direct evidence for the inability leg — *"the deployed posture is unable to permit the safety failure under the assumed-adversary model."*
- **mod-111 (Scaled Red Team).** Control-eval protocols are one row of the mod-111 scaled-red-team coverage matrix.
- **mod-112 (Disclosure).** Control-eval failures on tier-relevant protocols are disclosure-relevant.

## Common misreadings to avoid

- **"AI control replaces alignment measurement."** No. Alignment measurement bounds the plausibility of the misalignment assumption; control asks what safety we get if that measurement is wrong. The two compose.
- **"A protocol that holds against a red team is proof of safety."** No. A protocol that holds against a *specific* assumed adversary at a *specific* capability level provides evidence at that specific point. Coverage-of-adversary-models is the load-bearing residual-uncertainty statement.
- **"Untrusted monitoring is a mitigation."** No, it is a *baseline*. Redwood's contribution was showing untrusted monitoring provides meaningful safety at all; treating it as the *sole* mitigation ignores the surrounding protocol engineering.
- **"Model organisms are toys."** They are controlled research objects with the same status as calibrated adversaries. Their construction is a peer-role handoff and their consumption is documented.
- **"A failed control eval means we should retrain."** It means the finding routes to `fine-tuning-engineer` for intervention *direction*, and the peer role selects and executes. The mod-110 role does not select the intervention.
- **"Control eval and sabotage eval are the same."** Sabotage eval (chapter 07) measures outcome-degrading behaviour at scale. Control eval measures whether the operator's *composed* posture holds against an adversarial model. They compose (sabotage-attempt rates parameterise the adversary in control eval) but they are distinct.
- **"This is a research role."** It is not. The mod-110 role productionises the Redwood protocol. Novel-methodology publication is a peer-role concern; chapter 09 draws the boundary.

## Summary

- AI control is the engineering posture of extracting safety guarantees despite an assumed-adversarial target model. Greenblatt et al. (Redwood, 2023) is the reference paper.
- The **control-eval protocol** has five components: task specification, assumed adversary model, operator posture, red team, scoring (safety-usefulness Pareto).
- **Honeypot tasks** provide direct adversarial-behaviour signal independent of ultimate task outcome; they are a component of the task-item bank.
- **Untrusted-monitoring baselines** are the reference comparison point; Redwood's contribution is that untrusted monitoring provides meaningful safety when correctly engineered.
- **Model organisms** are the red-team-target and the calibration reference; construction is a peer-role handoff to `fine-tuning-engineer`.
- Findings route to `fine-tuning-engineer` for safety-tuning intervention direction; the mod-110 role runs the re-evaluation and closes the loop.
- Downstream cites: mod-107 EACC (control-eval directly tests the EACC's composition), mod-108 monitor calibration, mod-109 safety-case inability leg, mod-111 red-team coverage, mod-112 disclosure. Peer-role handoff codified in chapter 09.
