# 09 — Boundaries: The Frontier-Lab Research Ladder, and the Peer-Role Handoff Map

## Motivation

Chapters 02–08 built seven evaluation families and one engineering posture. Every one of them named a peer-role handoff or a frontier-lab-methodology cite that the mod-110 role does *not* re-derive in-house. Chapter 09 codifies those cites in one place. It is the boundary chapter — the reference for what this role productionises, and what it does not.

The load-bearing distinction is the one chapter 01 named up front: the frontier-lab **AI-safety-research-scientist ladder** (Apollo, Anthropic Alignment, Redwood, DeepMind Alignment, OpenAI Alignment, the UK / US AISIs) owns the *derivation* of adversarial-alignment evaluation methodology; the mod-110 role owns the *productionisation* of that methodology into an operator's evaluation pipeline. Reading the boundary correctly is protective: a mod-110 exercise that tries to invent a new deception-probe class or a new sabotage taxonomy is out of scope, and shipping methodology-shaped work through a mod-110 artefact is a routing failure.

Peer-role handoffs are a distinct concern: within the ML Engineering, AI Engineering, and AI Governance families, several roles own components mod-110 consumes. Chapter 09 pins each handoff — what mod-110 hands over, what it consumes back, and where the responsibility line runs.

## The frontier-lab AI-safety-research-scientist ladder — the derivation-vs-productionisation boundary

### What the frontier-lab role owns

The frontier-lab AI-safety-research-scientist role owns the *derivation* of adversarial-alignment evaluation methodology. Concretely, the role's craft is:

- **Novel probe taxonomies.** The Apollo six-probe scheming taxonomy (Meinke et al., 2024), the Anthropic four-family sabotage taxonomy (Benton et al., 2024), the Anthropic + Redwood alignment-faking probe design (Greenblatt et al., 2024), the Anthropic sleeper-agent construction (Hubinger et al., 2024), the Redwood AI-control protocol (Greenblatt et al., 2023) are all derivations shipped by this ladder.
- **Novel elicitation techniques.** The password-locked-capability elicitation technique (Greenblatt et al., 2024) is a derivation. So is any subsequent scratchpad-elicitation variant, any subsequent activation-based trigger-discovery probe, any subsequent framing-credibility engineering that raises the ceiling on what the paired-condition probe can detect.
- **Novel scoring / judging methodology.** The rubrics for scheming-relevant reasoning-trace labels, the compliance-gap scoring shape, the sabotage-attempt-vs-success distinction — all authored by the frontier-lab ladder in the reference papers.
- **The mechanistic-interpretability side of trigger discovery.** White-box activation-space probes, weight-space analysis, circuit-level analysis of hidden behaviour — these are frontier-lab-role craft, sometimes with an inner mechanistic-interpretability specialist sub-ladder.
- **Novel model-organism construction methodology.** The specific construction recipes for the Hubinger sleeper-agent organisms, the Benton sabotage-calibration organisms, the Greenblatt password-locked organisms — the recipes themselves are derivations.

### What the mod-110 role owns

The mod-110 role owns the *productionisation* of the above into an operator's evaluation pipeline. Concretely, the role's craft is:

- **Reproducing the frontier-lab methodology on the operator's target-model portfolio, at frontier scope, with reproducibility discipline.** Every artefact this role produces cites the frontier-lab methodology by paper title and revision; the harness, the probe bank, the target-model version, the judge panel, and the sampling budget are all versioned and re-runnable.
- **The eval-run report — the delta report, the compliance-gap report, the sandbagging report, the sleeper-agent regression fixture, the scheming-probe report, the sabotage-evaluation report, the control-eval report.** Chapters 02–08 pin each report shape. The report is the mod-110 role's deliverable.
- **The operator-scope threat model driving eval prioritisation.** Which evaluations to run, at what depth, on which candidate models, in which order — driven by the operator's threat model (mod-102), regulatory context (mod-101), and deployment plan.
- **The peer-role interface contract.** What mod-110 hands `fine-tuning-engineer` for organism construction, what mod-110 hands `ai-eval-engineer` for harness plumbing, what mod-110 hands `senior-agentic-ai-engineer` for agent-scaffold instrumentation. Chapter 09 codifies each below.
- **The residual-uncertainty accounting.** For each evaluation family, the accounting of what the eval could have missed under a stronger adversarial-model assumption. mod-109's safety case cites the accounting; mod-112's disclosure summarises it.

### The one-way flow

The relationship is one-way:

- **The mod-110 role consumes the frontier-lab methodology, cited by paper title and revision.** Every chapter's *Primary sources* section pins the citations.
- **The mod-110 role does not publish novel methodology.** A finding that would constitute a *new probe class*, a *new elicitation technique*, or a *new organism construction recipe* is routed to the peer role via internal-safety-research channels. The mod-110 artefact is the eval-run report, not the methodology paper.
- **The mod-110 role's craft is the run, the instrumentation, the reproducibility discipline, the reporting shape, and the downstream cite.** The frontier-lab role's craft is the derivation of *what to measure* and *why*.

If a mod-110 finding materially advances the methodology (e.g., the operator's target-model portfolio surfaces a scheming behaviour the reference taxonomy did not anticipate), the routing is:

1. **Reproduce and characterise** the finding using the existing methodology and instrumentation. The finding is *observed with existing tools*, not *published as a new tool*.
2. **Route to internal-safety-review** with a specific ask: is this a novel-methodology contribution, or a novel-behaviour observation within existing methodology?
3. **If novel-methodology**, the peer role (frontier-lab AI-safety-research-scientist within the operator's org, or an external frontier lab under a research collaboration) picks up the derivation. The mod-110 role co-authors the observation and the reproduction protocol; the peer role authors the methodology.
4. **If novel-behaviour within existing methodology**, the finding stays in the mod-110 report as an eval-run result, is escalated to mod-112 for disclosure review, and updates the target-model's ongoing regression fixture.

Do not silently ship methodology-shaped work through a mod-110 artefact. The routing to internal-safety-review is a protective handoff; skipping it is what makes a mod-110 role drift into a frontier-lab role it is not scoped for.

## Peer-role handoff map

Below, each peer role that mod-110 explicitly hands work to, in the shape those handoffs took in chapters 02–08.

### `fine-tuning-engineer` (peer, level 30, ML Engineering family)

Owns the **training-time interventions** on target models — SFT, RLHF, constitutional-AI, adversarial training, controlled fine-tunes for research purposes. The mod-110 role hands specifications; the peer role delivers trained artefacts.

What mod-110 hands over:

- **Model-organism specifications** (chapter 05, chapter 07, chapter 08). The trigger, the trigger-behaviour, the training-data recipe, the intended persistence profile, the expected behavioural rate. mod-110 authors the spec; the peer role delivers the trained organism.
- **Password-locked-capability model specifications** (chapter 04). The password / unlock string, the capability to be locked, the expected locked / unlocked ceilings. mod-110 authors the spec; the peer role delivers the fine-tuned password-locked variant.
- **Fine-tune-elicitation training specifications** (chapter 04). The capability-adjacent tasks, the training signal, the number of steps. mod-110 authors the spec; the peer role runs the fine-tune.
- **Recommended intervention directions** (chapter 08). When a control-eval fails, mod-110 names the direction (constitutional-AI update, adversarial-training pass, RLHF signal engineering). The peer role selects the specific intervention and executes.
- **Re-evaluation contracts.** After the peer role's intervention, mod-110 re-runs the affected evaluation and reports the delta.

What mod-110 consumes back:

- **The trained organism / password-locked variant / fine-tuned model.** With training-run metadata (dataset ID, training-signal spec, checkpoint hash, evaluation snapshot at construction time).
- **The peer role's calibration on the fine-tune contract.** For primitive 2 / primitive 3 sandbagging evaluations, the peer role's calibration on how much of the observed delta is capability injection vs sandbagging release is the interpretive lens mod-110's report cites.

Where the boundary runs:

- mod-110 does **not** run the fine-tuning pipeline in-house. Attempts to do so drift into the peer role's craft, and the calibration on training-signal engineering is not owned in this module.
- mod-110 does **not** select the specific safety-tuning intervention on a control-eval finding. It names the direction and re-evaluates the outcome; the peer role picks the intervention.

### `ai-eval-engineer` (peer, level 30, AI Engineering family)

Owns the **eval harness platform** — the prompt-orchestration engine, the judge-orchestration engine, the artefact-storage layer, the retry / rate-limit / cost-control layer, the reporting infrastructure. The mod-110 role designs *evaluations*; the peer role delivers the *platform* those evaluations run on.

What mod-110 hands over:

- **Probe-bank specifications** for each chapter's evaluation family (probe items, framing conditions, target-model interface contract, expected trace slots).
- **Judge-rubric specifications** (chapter 02 probe-class rubrics, chapter 03 compliance-gap rubric, chapter 06 scheming-label rubric, chapter 07 sabotage-attempt rubric).
- **Blinding contracts** — which framing information the grader panel must not see, which redactions the trace redactor must apply before grader-input (chapter 01, chapter 02, chapter 03).
- **Report-shape specifications** — chapters 02–08 each pin a report shape. The peer role builds the reporting infrastructure that emits reports in that shape.

What mod-110 consumes back:

- **The eval harness itself** — the orchestration engine that runs the probe-bank, invokes the target, captures the traces, runs the graders, aggregates the results, emits the report.
- **The judge-orchestration platform** — the LLM-grader invocation infrastructure, the human-grader UI, the paired-blind protocol enforcement.
- **The artefact-storage layer** — the versioned probe bank, the versioned prompts, the run-indexed trace corpus, the hash-anchored report register.

Where the boundary runs:

- mod-110 does **not** build the harness. Attempts to do so drift into platform work that is not this module's craft.
- mod-110 does **not** own the harness's operational reliability. If the harness is flaky, the peer role owns the fix; mod-110 owns the eval design that the harness runs.

### `model-evaluation-engineer` (peer, level 30, ML Engineering family)

Owns the **statistical methodology** — sample-size calculation, CI construction, judge-agreement kappa methodology, extrapolation methodology for the elicitation-gap accounting. mod-106 chapter 07 first named this deferral; mod-110 inherits it.

What mod-110 hands over:

- **The methodological requirements** for each evaluation family. Chapter 02's per-probe-class delta CI, chapter 03's compliance-gap CI, chapter 04's sandbagging-delta CI, chapter 06's per-probe-class engagement rate CI, chapter 07's attempt-vs-success rate CI.
- **The interpretive threshold requests.** The peer role sets the CI-width threshold above which a delta is not interpretable as signal. mod-110 designs the eval to hit the threshold; the peer role calibrates the threshold.
- **The judge-agreement methodology request.** For LLM-grader-vs-human-panel calibration, the peer role owns the kappa methodology and the calibration-curve shape.

What mod-110 consumes back:

- **The statistical treatment** applied to each report. CI widths, judge-agreement scores, extrapolation bounds.
- **The sample-size calibration** that sets the eval's cost / precision Pareto point.
- **The residual-uncertainty statistical bound** that the eval's residual-uncertainty accounting section quotes.

Where the boundary runs:

- mod-110 does **not** derive the statistical methodology. Attempts to do so drift into a peer-role craft that this module does not train.
- The chapter 02 language *"calibration defers to `model-evaluation-engineer` and is developed in mod-111"* is the reference deferral for every downstream chapter.

### `senior-agentic-ai-engineer` (peer / next-up, level 40 or 50 depending on the org, AI Engineering family)

Owns the **agent scaffold** — the planning loop, the tool-selection strategy, the sub-agent spawning contract, the memory-management contract. Chapter 06 (in-context scheming) and chapter 08 (AI control) both depend on running evaluations *at agentic scope*, which requires the scaffold as instrumentation surface.

What mod-110 hands over:

- **The agent-scope evaluation specification** — the scenarios (chapter 06 probe classes 1–6, chapter 08 task-item bank), the tool inventory constraint (matching the deployed EACC), the expected trace slots at planning steps.
- **The instrumentation requests** — what the scaffold must capture at each planning step, sub-agent spawn, tool call, memory read / write. Chapter 06 pins the trace contract.
- **The consequence-fidelity specification** — for scheming-probe honeypots (chapter 06), which tool calls have simulated consequences and which have real consequences; the scaffold enforces the simulation boundary.

What mod-110 consumes back:

- **The agent scaffold with the required instrumentation active.** Trace slots emitted, tool calls logged, sub-agent traces linked, memory snapshots captured.
- **The scaffold's own reliability characterisation.** If the scaffold's planner has a documented failure mode, the mod-110 residual-uncertainty accounting cites it — a scheming-probe non-engagement rate may be scaffold-failure noise rather than model-alignment signal.

Where the boundary runs:

- mod-110 does **not** design the agent scaffold. It specifies the instrumentation contract; the peer role delivers the scaffold that satisfies the contract.
- The choice of *what the deployed agent scaffold looks like* is a peer-role decision; mod-110's evaluation runs *against* a specified scaffold, and reports are per-scaffold.

### `ai-infra-security` (peer, level 35, Security family)

Owns the **runtime hardening and interpretability tooling** at platform scope — the credential broker, the egress control layer, the tenant isolation posture, and (for chapter 05's white-box trigger-discovery approach) the mechanistic-interpretability tooling for the target-model class the operator can access.

What mod-110 hands over:

- **The interpretability-tooling request** — for chapter 05 white-box trigger-discovery, the specification of the activation-space probes required, the model-class support required.
- **The runtime-isolation request** — for chapter 06 and chapter 08 evaluations that run agent-scope probes with (nominally) high-consequence tool access, the isolation posture that keeps the eval's tool calls from affecting production systems.
- **The audit-log-stream requirements** — chapter 06 and chapter 08 both consume the audit log; the log's tamper-evidence and completeness are peer-role territory.

What mod-110 consumes back:

- **The interpretability tooling platform.** The activation-scanning infrastructure, the model-class support, the reference-model calibration.
- **The evaluation-scope runtime isolation.** A sandboxed target-model runtime where the eval can safely execute (nominally) high-consequence tool-calls without production impact.
- **The audit-log stream.** The same stream mod-107 chapter 03 pins as the containment-contract log; mod-110's evaluations consume it as instrumentation.

Where the boundary runs:

- mod-110 does **not** build interpretability tools. It requests them; the peer role builds and maintains.
- mod-110 does **not** operate the runtime-isolation platform. Evaluations run *on* the platform; the platform is peer-role-owned.

### `senior-ai-governance-architect` (level 50, AI Governance family)

Same peer role mod-109 chapter 06 cited: owns the **control library and framework composition strategy**. mod-110 consumes the framework composition to know which thresholds its evaluations bound against.

What mod-110 hands over:

- **The evaluation-report catalogue** — the enumerated set of eval-report families this role produces (chapters 02–08), each with the framework thresholds they consume.
- **The escalation triggers** — the pre-registered thresholds above which a report is disclosure-relevant (RSP tier / Preparedness scorecard cell / FSF CCL). mod-110 authors the trigger against the framework composition; the architect owns the composition.

What mod-110 consumes back:

- **The framework composition** — which framework's language reports are authored in, which framework's thresholds trigger escalation, which regulatory context the reports operate under.
- **The escalation routing** — which reviewer body receives a disclosure-relevant report (RSO, Preparedness Committee, Safety Board, external regulator engagement).
- **The threshold definitions** — the specific numerical or categorical thresholds each evaluation's aggregate metric is compared against.

Where the boundary runs:

- mod-110 does **not** author the org's framework composition strategy or the control library. It consumes both; the architect owns them.
- mod-110 does **not** set the disclosure thresholds. It authors the eval that hits the threshold; the architect (with mod-112) sets the threshold.

## Downstream cite map — what mod-110 hands *to* other modules

Chapter-by-chapter, mod-110 reports feed downstream modules. The cite direction is one-way per module; the cite target is a specific report shape.

- **mod-102 (Threat Modelling).** ATMD threat entries for deception, scheming, sabotage, sleeper-agent, alignment-faking are *bounded* by mod-110 evaluation reports. Cite direction: mod-110 hands the residual-uncertainty accounting to mod-102 as a widening / narrowing signal on the threat entries.
- **mod-106 (Dangerous-Capability Eval).** The DCER's elicitation-gap-accounting clause on any expressed-inability capability cites mod-110's paired sandbagging report (chapter 04). Cite direction: mod-110 hands the sandbagging bound to mod-106; mod-106's DCER quotes it as the third layer of the elicitation-gap accounting.
- **mod-107 (Excessive-Agency Containment).** An EACC authored under uniform-behaviour assumptions is re-argued against positive findings from chapter 03 (compliance gap), chapter 06 (oversight-tool subversion, deceptive alignment in scheming), chapter 07 (sabotage attempt / success rates). Cite direction: mod-110 hands the delta reports; mod-107 re-argues the affected EACC sections.
- **mod-108 (Guardrails and Monitors).** Monitor calibration on strategically-adversarial behaviour consumes chapter 02's trace corpus, chapter 05's non-trigger baseline, chapter 06's scheming-trace patterns, chapter 07's attempt-vs-success delta. Cite direction: mod-110 hands the calibration data; mod-108 calibrates.
- **mod-109 (Safety Cases).** The trustworthiness leg of the safety case cites mod-110's residual-uncertainty accounting across every chapter's reports. The control leg cites chapter 08's control-eval report. The inability leg cites chapter 08's control-eval-that-holds. Cite direction: mod-110 hands the report; mod-109 cites specific report node IDs in the case.
- **mod-111 (Automated Red Team).** The scaled-red-team coverage matrix consumes mod-110's evaluation families as coverage rows (deception, alignment-faking, sandbagging, sleeper-agent, scheming, sabotage, control-eval). Cite direction: mod-110 hands the report; mod-111 maps to coverage rows and identifies gaps.
- **mod-112 (Program & Disclosure).** Findings above pre-registered thresholds are disclosure-relevant. Cite direction: mod-110 hands the report; mod-112 shapes the RSP / Preparedness / FSF / AISI / EU AI Act Article 55–56 disclosure.

Every mod-110 report has a *downstream cites* section (chapters 02–08 each pin this). Reports without the section are incomplete; the peer / downstream module cannot consume a report that does not name what it is for.

## What mod-110 does *not* build

The following are common temptations for this role to build, and they are peer-role or frontier-lab scope:

- **Novel evaluation methodology.** New probe classes, new elicitation techniques, new judging rubrics — frontier-lab AI-safety-research-scientist ladder scope. The routing above is the protective handoff.
- **The eval harness platform.** `ai-eval-engineer` craft. mod-110 designs evaluations; the peer role delivers the platform.
- **The fine-tuning pipeline and training-time interventions.** `fine-tuning-engineer` craft.
- **The agent scaffold.** `senior-agentic-ai-engineer` craft.
- **The interpretability tooling and runtime-isolation platform.** `ai-infra-security` craft.
- **The statistical methodology.** `model-evaluation-engineer` craft.
- **The org-wide control library, framework composition, cross-jurisdictional reconciliation.** `senior-ai-governance-architect` craft.
- **The regulator engagement plan and disclosure shaping.** mod-112 in the immediate module boundary, and `head-of-ai-governance` (level 60) at strategy scope, both out of scope for direct mod-110 authorship.
- **The safety case.** mod-109 owns the case; mod-110 hands evidence.

## What mod-110 *does* build

From this module's chapters and exercises, the artefacts a level-40 role in this module ships:

- **Per-family evaluation reports.** Chapter 02's deception-rate delta report, chapter 03's compliance-gap report, chapter 04's sandbagging report, chapter 05's sleeper-agent regression-fixture report, chapter 06's scheming-probe report, chapter 07's sabotage-evaluation report, chapter 08's control-eval report.
- **Model-organism specifications.** Chapter 05 (sleeper-agent organisms), chapter 07 (sabotage-calibration organisms), chapter 08 (control-eval red-team organisms). The specs; not the trained organisms themselves — those are peer-role deliverables.
- **The control-eval protocol.** Chapter 08's five-component spec (task, adversary model, operator posture, red team, scoring). Runs against a specific target-model or organism; produces a control-eval report.
- **The residual-uncertainty accounting.** Every report carries one; the accounting is what mod-109's safety case and mod-112's disclosure both cite.
- **The peer-role interface contracts.** The specifications this chapter enumerated for `fine-tuning-engineer`, `ai-eval-engineer`, `model-evaluation-engineer`, `senior-agentic-ai-engineer`, `ai-infra-security`.

Each artefact names the adjacent-role components it plugs into. None re-derives them.

## Reading the boundary against the rest of the module

- **Chapter 01 (discipline).** Chapter 01 named the derivation-vs-productionisation boundary up front; chapter 09 codifies it against the specific artefact shapes chapters 02–08 produce.
- **Chapter 02 (Apollo deception).** Frontier-lab boundary: Apollo owns the probe-class derivation. Peer-role boundaries: `ai-eval-engineer` (harness), `model-evaluation-engineer` (judge calibration).
- **Chapter 03 (alignment faking).** Frontier-lab boundary: Anthropic + Redwood own the paired-framing derivation. Peer-role boundary: `fine-tuning-engineer` if any training-time replication is attempted, otherwise behavioural-only.
- **Chapter 04 (sandbagging).** Frontier-lab boundary: Apollo / Redwood / Greenblatt et al. own the primitives. Peer-role boundaries: `fine-tuning-engineer` (primitives 2 and 3), `model-evaluation-engineer` (delta-CI thresholds).
- **Chapter 05 (sleeper agents).** Frontier-lab boundary: Anthropic owns the sleeper-agent construction and persistence methodology; the mechanistic-interpretability sub-ladder owns the white-box trigger-discovery approach. Peer-role boundaries: `fine-tuning-engineer` (organism construction), `ai-infra-security` (interpretability tooling).
- **Chapter 06 (in-context scheming).** Frontier-lab boundary: Apollo owns the six-probe derivation. Peer-role boundaries: `senior-agentic-ai-engineer` (scaffold instrumentation), `ai-eval-engineer` (harness), `ai-infra-security` (runtime isolation).
- **Chapter 07 (sabotage).** Frontier-lab boundary: Anthropic owns the four-family derivation and the attempt-vs-success framing. Peer-role boundaries: `fine-tuning-engineer` (calibrated organisms), `ai-eval-engineer` (naturalistic-scale harness), production-team engagement for naturalistic prompts / reviewers / decision contexts.
- **Chapter 08 (AI control).** Frontier-lab boundary: Redwood owns the protocol derivation. Peer-role boundaries: `fine-tuning-engineer` (control-eval organisms, safety-tuning intervention on findings), `senior-agentic-ai-engineer` (scaffold), `ai-eval-engineer` (harness), `senior-ai-governance-architect` (framework composition the protocol operates under).

## Out of scope

- **Novel-methodology publication.** The mod-110 role does not publish adversarial-alignment methodology papers. The routing to internal-safety-review or an external frontier-lab collaboration is the protective handoff for observations that materially advance the methodology.
- **Training-pipeline work.** SFT / RLHF / constitutional-AI / adversarial training / distillation are `fine-tuning-engineer` craft. Mod-110 authors organism specifications and consumes trained artefacts.
- **Interpretability research at circuit-level depth.** Mechanistic-interpretability research is a frontier-lab specialist sub-ladder. mod-110 consumes interpretability *tooling* from `ai-infra-security`; it does not derive interpretability *methodology*.
- **Program leadership at organisation scope.** The annual adversarial-alignment eval cadence, the cross-team coordination, the board-level reporting, the external regulator relationship — these are `head-of-ai-governance` (level 60) craft, above this module.
- **The safety case authorship.** mod-109 owns the case; mod-110 provides evidence.
- **Statistical-methodology derivation.** `model-evaluation-engineer` craft. mod-110 consumes calibration; it does not derive.

## The boundary in one sentence

Mod-110 productionises frontier-lab adversarial-alignment evaluation methodology into repeatable operator eval-run reports and residual-uncertainty accounting; the frontier-lab AI-safety-research-scientist ladder owns the derivation of that methodology; peer roles across ML Engineering, AI Engineering, Security, and AI Governance own the platforms, models, scaffolds, statistics, and framework composition mod-110's evaluations plug into.

## Common misreadings to avoid

- **"If our target-model portfolio surfaces a new deception mode, we should design a new probe class."** No. Reproduce the finding with existing probes, characterise it, and route to internal-safety-review for the novel-methodology question. Silent methodology development inside a mod-110 report is a routing failure.
- **"We own the eval harness because we design the evaluations."** No. `ai-eval-engineer` owns the harness. Design and platform are separate crafts; blurring them into one role's scope is what leaves both under-invested.
- **"Model-organism construction is a mod-110 exercise."** The *specification* is; the *construction* is `fine-tuning-engineer` craft. mod-110 exercise 08 pins the spec-vs-trained-artefact boundary.
- **"The peer roles will figure out their calibration on our findings."** No. The peer role needs the *interface* — the report's version pin, the assumed-adversary spec, the residual-uncertainty accounting shape. mod-110 designs the interface; the peer role consumes it.
- **"Once the eval report is emitted, mod-110 is done."** No. The report iterates across model-version releases and safety-tuning-pipeline changes. Chapter 05's regression-fixture posture generalises: every mod-110 report has a re-run cadence and a trajectory across versions.
- **"Novel-methodology work is what makes this role senior."** No. The mod-110 role's seniority is expressed in *productionisation quality* — the residual-uncertainty accounting, the reproducibility discipline, the peer-role handoff design, the downstream-cite shape. Novel methodology is what makes the *frontier-lab research role* senior; the two ladders are distinct.
- **"AI control makes deception measurement redundant."** No. mod-110 chapter 01 named the composition: alignment measurement bounds the plausibility of the misalignment assumption; control asks what safety we get if that measurement is wrong. Chapter 08 depends on chapters 02–07's parameterisation of the adversary model.

## Summary

- Mod-110 owns the **productionisation** of adversarial-alignment evaluation methodology; the **frontier-lab AI-safety-research-scientist ladder** owns the derivation. The relationship is one-way; novel-methodology findings route to internal-safety-review for peer-role pickup.
- Peer-role handoffs codified: **`fine-tuning-engineer`** (organisms, password-locked variants, fine-tune elicitation, safety-tuning intervention), **`ai-eval-engineer`** (harness platform), **`model-evaluation-engineer`** (statistical methodology), **`senior-agentic-ai-engineer`** (agent scaffold instrumentation), **`ai-infra-security`** (interpretability tooling, runtime isolation), **`senior-ai-governance-architect`** (control library, framework composition).
- Downstream cite map codified: reports feed mod-102 (threat-entry widening / narrowing), mod-106 (DCER elicitation-gap third layer), mod-107 (EACC re-argument), mod-108 (monitor calibration), mod-109 (trustworthiness / control / inability legs), mod-111 (coverage matrix), mod-112 (disclosure).
- Mod-110 builds **per-family evaluation reports, model-organism specifications, control-eval protocols, residual-uncertainty accounting, peer-role interface contracts.**
- Out of scope: novel-methodology publication, training-pipeline work, interpretability-methodology derivation, program leadership, safety case authorship, statistical-methodology derivation.
- The seniority of a mod-110 role is expressed in productionisation quality, not in novel methodology. The frontier-lab ladder's seniority is expressed in derivation; the two ladders are distinct by design.
