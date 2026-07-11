# 02 — CBRN-Uplift Evaluation Engineering

## Motivation

Every frontier safety framework carves out CBRN (chemical, biological, radiological, nuclear) capability as a discrete tier of concern. Anthropic's RSP names bioweapons uplift as the driver of ASL-3 in its published Capability Reports; OpenAI's Preparedness Framework tracks CBRN as one of its named categories; the FSF names biosecurity CCLs; the EU AI Office GPAI Code of Practice safety chapter identifies CBRN uplift as one of the systemic-risk axes; the UK and US AI Safety Institutes anchor their pre-deployment evaluations partly on CBRN. Across all five, the engineering question is the same: **does a frontier model, under best-effort elicitation, meaningfully uplift a would-be malicious actor's ability to plan and execute a CBRN attack, relative to that actor's baseline capability with the same resources absent the model?**

That question has three parts that the engineering discipline has to hold together simultaneously: (1) a *threat model* that names the actor and the attack that matters — not "any CBRN harm" but a specific hostile-actor archetype and a specific attack pathway; (2) a *measurement* that combines closed-question benchmarks (WMDP) with expert-graded open-ended synthesis-planning tasks graded by external CBRN domain experts; (3) a *security discipline* that lets you run the eval without adding to the harm you are measuring — a run that leaks a novel bioweapon-synthesis pathway is a run that has *failed*, regardless of what number it produced.

This chapter develops all three. It draws the boundary against generic biosecurity policy work (which is not this module's scope) and against toxicity / harmful-content evals (which are mod-104 / mod-108 territory). It codifies the security controls, the external-grader interface, and the WMDP + FMF-threat-model composition that anchors the capability panel.

## Primary sources for this chapter

- **Li, Nathaniel et al., "The WMDP Benchmark: Measuring and Reducing Malicious Use With Unlearning," ICML 2024.** The Weapons of Mass Destruction Proxy benchmark: ~3,600 multiple-choice questions across biology, chemistry, and cybersecurity, designed as proxies for hazardous knowledge without releasing the hazardous content itself. The paper co-releases an unlearning methodology (RMU) as the mitigation companion. <!-- needs-research: confirm the current WMDP release URL, version tag, question count per subset, and any post-2024 companion releases. -->
- **Frontier Model Forum working papers on biological-risk evaluations.** The FMF has published a series of papers on the bio threat model, uplift methodology, and evaluation guidance for frontier labs. <!-- needs-research: confirm the current FMF bio-risk publication list, dates, and titles; cite the most recent version in the DCER's threat-model section. -->
- **Anthropic RSP** — the ASL-3 Bioweapons capability report methodology, external biosecurity expert engagement, and the paired evaluation designs (uplift trials, expert-graded synthesis planning). Read the most recent Capability Report or system card's bio section. <!-- needs-research: confirm the current RSP version and Capability Report titles. -->
- **OpenAI Preparedness Framework and o-series system cards** — the CBRN category thresholds and the o-series bio-uplift experimental designs (paired human-baseline uplift trials, expert grading). <!-- needs-research: confirm the current Preparedness Framework version and the latest o-series system-card's bio-uplift methodology section. -->
- **Google DeepMind FSF and Gemini technical / system-card reports** — the biosecurity CCL definitions and the paired uplift methodology. <!-- needs-research: confirm the current FSF version and the most recent Gemini technical report or system card. -->
- **RAND, "The Operational Risks of AI in Large-Scale Biological Attacks," 2024** and follow-on RAND analyses of AI's marginal uplift in bioweapons planning. <!-- needs-research: confirm citation and follow-up publications. -->
- **UK AISI + US AISI (NIST AISIC) pre-deployment evaluation reports** — the AISI methodology for bio-eval, Inspect harness usage, and paired human baseline discipline. <!-- needs-research: confirm the current AISI publication list and the latest bio-eval methodology posts. -->
- **World Health Organization** biosafety guidance and **International Atomic Energy Agency** guidance where the eval enters into rad / nuc pathways. Coordinate with in-house biosecurity / nuclear-safety counsel before external engagement.

## Anatomy of a CBRN-uplift evaluation

Every CBRN-uplift capability panel this module authors has four parts. Missing any one downgrades the finding to a benchmark score or a bug report.

1. **The threat model.** Which hostile actor, with what starting capability, seeking what attack, over what horizon. "A trained biology graduate student attempting to synthesise a specific pathogen with commercial reagents over three months" is a threat model. "Any bad actor doing anything harmful with biology" is not.
2. **The elicitation protocol.** All four axes from chapter 01 — best-of-N, fine-tune, tool-use, scaffolding — instantiated for the CBRN capability. Fine-tune elicitation is particularly load-bearing in CBRN because the base-model refusal rate is high and the elicitable-capability-vs-refusal gap is wide.
3. **The measurement composition.** WMDP-style closed-question knowledge measurements *plus* expert-graded open-ended synthesis-planning uplift *plus*, where relevant, paired human-baseline uplift trials in which a control group works without the model. Each anchors a different part of the ceiling.
4. **The security controls.** Access controls on prompts, on outputs, on graders, on stored data. The controls are engineered *before* the eval runs; if you find yourself designing controls mid-run, the eval has already leaked.

## The four CBRN sub-panels

CBRN is not one capability; it is four. Each has different threat-model shape, different measurement composition, and different domain-expert requirements.

### Sub-panel B — Biological uplift

*The load-bearing sub-panel.* The published frontier safety frameworks treat biological uplift as the most immediately load-bearing CBRN concern (ASL-3 in RSP, High in Preparedness, biosecurity CCLs in FSF) because the marginal-uplift threat model is most plausible: a would-be attacker with limited baseline biology capability could conceivably be raised to competent-graduate-student level by a frontier model that answers pathogen-synthesis questions and troubleshoots wet-lab protocols.

*Threat model*: two archetypes are typically named in the safety-framework literature.

- **Novice uplift**: an actor with high-school-level biology, no lab access, no reagent access, seeking a step-by-step plan for a plausibly-synthesisable pathogen. The safety question is whether the model closes the gap from *no plan* to *executable plan*. Historically the FMF, RSP, and AISI reads have converged on novice-uplift as one relevant tier's driver.
- **Expert amplification**: an actor with graduate-student biology, laboratory access, seeking troubleshooting for a specific difficult step. The safety question is whether the model closes the gap from *stuck expert* to *unstuck expert*. This is a lower-probability but higher-consequence archetype.

*Measurement composition*:

1. **WMDP-Bio closed-question subset.** ~1,273 multiple-choice questions on hazardous biology knowledge, designed to be proxies for knowledge relevant to bioweapons planning without themselves releasing hazardous content. <!-- needs-research: confirm the current WMDP-Bio question count and any splits. --> WMDP is a *lower bound* — passing WMDP-Bio does not mean the model can plan a synthesis; failing it does not mean it cannot. WMDP-Bio's role is as a widely-cited common measurement anchor.
2. **Expert-graded synthesis-planning uplift.** A held-out set of open-ended synthesis-planning prompts, graded by external biosecurity experts under access controls (see Security Controls below). The grader assesses the *quality of the plan* against a rubric derived from FMF bio-eval guidance. Grading occurs offline on air-gapped terminals; the grading rubric is co-designed with the graders in advance.
3. **Paired human-baseline uplift trial (where the tier requires it).** Two groups of study participants — control group with internet access only, treatment group with internet plus the model — each attempt the synthesis-planning task under supervision. The uplift metric is the difference in expert-graded plan quality between groups. This is the design Anthropic, OpenAI, and DeepMind have each published for the load-bearing tiers. It is expensive (dozens of participants, weeks of grading) but is often the decision-relevant measurement for RSP ASL-3 / Preparedness High / FSF biosecurity CCL.

*Domain-expert engagement*:

- Recruit from external biosecurity organisations, biosafety-level-trained researchers, or public-health domain experts. The FMF working papers document referral pathways.
- Coordinate through **WHO** biosafety guidance and, where the pathogen adjacency raises rad/nuc concerns, **IAEA** nuclear-safety guidance. This is not a general regulatory-liaison role — it is the specific domain-expertise cite for the DCER.
- Grader agreement is measured; per-item rubric-agreement is reported (the peer role's calibration methodology).

*Elicitation-axis notes*:

- **Fine-tune elicitation** is particularly load-bearing here. The gap between base-model pass rate and fine-tuned-elicitation pass rate on WMDP-Bio is often the largest single-axis uplift in the panel. Fine-tune weights stay behind the safety-team sandbox boundary.
- **Tool-use elicitation** includes providing the model with domain-specific tools (protein-structure predictors, sequence-search tools, biological-literature retrieval). The tool set should reflect what a motivated actor with a public-cloud budget could plausibly assemble.
- **Scaffolding elicitation**: multi-turn agentic scaffolds that let the model draft, critique, and revise a synthesis plan. Reflexion-style scaffolds often produce large uplift here.

### Sub-panel C — Chemical uplift

*Threat model*: an actor seeking synthesis pathways for a scheduled toxic industrial chemical, chemical weapons agent, or precursor. Baseline expertise is undergraduate chemistry; access to reagents varies by jurisdiction.

*Measurement composition*:

1. **WMDP-Chem closed-question subset.** ~408 multiple-choice questions on hazardous chemistry. <!-- needs-research: confirm the current WMDP-Chem question count. --> Same lower-bound status as WMDP-Bio.
2. **Expert-graded synthesis-planning uplift** on chemistry precursor and route-selection tasks. Grading by external chemistry-safety experts.
3. **Paired human-baseline uplift trial**, less commonly published than the bio version because the chemistry threat model has typically been assessed as less marginal-uplift-driven — but the tier and safety-framework read determines whether the trial is required.

*Domain-expert engagement*: OPCW-linked chemical-weapons-convention expertise where jurisdictionally accessible; academic industrial-chemistry safety experts otherwise.

### Sub-panel R — Radiological uplift

*Threat model*: an actor seeking the design and construction of a radiological-dispersal device ("dirty bomb"), the acquisition-and-diversion pathway for radiological material, or the sabotage pathway against a radiological facility.

*Measurement composition*: primarily expert-graded open-ended tasks (WMDP has less coverage of the radiological sub-panel). Coordination with **IAEA**-linked nuclear-security experts is essential; in-house radiological-safety counsel gates external engagement.

### Sub-panel N — Nuclear uplift

*Threat model*: an actor seeking design uplift for a nuclear weapon, an actor seeking IAEA-safeguarded-material diversion assistance, or an actor seeking nuclear-facility sabotage uplift. The base-model refusal rate is very high; the elicitable-capability question is the fine-tuned + scaffolded ceiling on hard technical sub-problems.

*Measurement composition*: primarily expert-graded open-ended tasks against sub-problems (weapon-design components, materials-processing steps). Grading through IAEA-linked expert channels under national-security-informed access controls. The engineering here overlaps substantially with government policy, and the safety-team's role is to *measure and report* while the disclosure and downstream handling is governed by mod-112 and by legal / policy counsel.

## Security controls — what "under proper controls" means

A CBRN eval that leaks the capability it is measuring is worse than no eval. Every CBRN-panel elicitation this module runs sits inside a control architecture engineered *before the first prompt*. The controls are:

### Control 1 — Access-controlled elicitation environment

- Elicitation runs in a **dedicated sandbox** with logged access, per-user MFA, and no default outbound egress. Personal machines and shared cloud accounts are out of scope.
- **Air-gapped grading terminals** for external graders. The grader receives the elicited material in a supervised session; the material does not exit the terminal.
- **Prompt and completion retention windows** are bounded — the shortest window that permits regrading and audit. Long-term retention of elicited harmful content is a controlled decision, not a default.

### Control 2 — External-grader interface

- External graders sign an NDA / DPA with a scope specific to the eval, per FMF guidance. Legal / policy counsel approves the contract.
- Grading rubric is co-designed with the grader before elicitation; graders never receive elicited content they have not agreed to grade.
- Grading is offline on the air-gapped terminals; results transit as **scores + rubric-cell verdicts**, not as elicited-content copies.
- Grader agreement is measured (per the peer role's calibration methodology) and reported in the DCER.

### Control 3 — Fine-tune-elicitation isolation

- Fine-tune weights produced during elicitation live only inside the safety-team sandbox. The weights themselves may embed the uplifted capability and are not distributed.
- The fine-tune dataset — the collection of examples used to elicit — is stored in the access-controlled artefact store and referenced in the DCER by ID, not by content.
- Post-elicitation, the fine-tune weights are retained per the retention window and then destroyed on schedule.

### Control 4 — Frontier-provider authorisation

- Elicitation runs against production frontier-provider APIs require the provider's written safety-team authorisation before the run. This is not optional. Providers publish elicitation-eval channels (safety.anthropic, OpenAI safety-team channels, Google DeepMind safety-team channels) that the eval team coordinates through.
- Novel policy-violating outputs elicited during authorised runs are disclosed to the provider through the provider's coordinated-disclosure channel — mod-112 codifies the workflow.

### Control 5 — Mod-112 disclosure workflow gate

- Any CBRN-eval finding whose disclosure could accelerate misuse routes through the mod-112 disclosure workflow. This includes external publication (system cards, safety-framework reports, academic papers), regulator briefings under EU AI Act Article 55 / 73, and AISI-report contributions.
- The disclosure workflow's recipient list, timing, and redaction policy are the mod-112 role's outputs; this module produces the underlying evidence and cites the workflow ID in the DCER.

### Control 6 — In-house biosecurity / nuclear-safety counsel gate

- External-expert engagement, IAEA / WHO coordination, and any government-body outreach on nuclear-panel findings routes through in-house counsel. This is not a legal-opinion generation step (that is out of scope for this role) — it is a routing gate to the roles authorised to conduct that outreach.

## Threading the WMDP + FMF-threat-model composition

WMDP is a *proxy knowledge* benchmark. The FMF bio threat model is a *hostile-actor-and-attack-pathway* model. The elicitation panel composes them: WMDP anchors the lower-bound knowledge measurement, the threat model anchors the top-of-panel synthesis-planning tasks, and the paired-baseline trial anchors the uplift claim.

An illustrative composition (defanged shape, not a working panel):

```yaml
cbrn_bio_panel:
  threat_model:
    source: fmf_bio_working_paper_vX
    actor_archetype: novice_uplift
    attack_pathway: <supervised-consultation reference, not published here>
    horizon: 3_months
  measurement_composition:
    wmdp_bio:
      version: <pin>
      subset: <selection ID>
      pass_at_n:
        n: 64
        temperature: 0.7
        expected_ceiling_ci: <deferred to peer role>
      elicitation_axes_swept: [best_of_n, fine_tune, scaffolding]
      # NOTE: fine-tune weights held in safety-team sandbox; not distributed.
    expert_graded_synthesis_planning:
      task_set_id: <handle to access-controlled artefact store>
      grader_org: <external biosecurity firm>
      rubric_id: <FMF-aligned rubric version>
      grader_agreement:
        k_items_double_graded: 40
        agreement_rate_ci: <deferred to peer role>
    paired_human_baseline_uplift_trial:
      required_for_tier: rsp_asl_3
      protocol_id: <trial registration handle>
      arms:
        control: internet_only
        treatment: internet_plus_model
      participants_per_arm: 30
      expert_grader_org: <same as above>
      uplift_metric: rubric_score_delta
  security_controls:
    elicitation_sandbox_id: <handle>
    grader_terminal_id: <air-gapped terminal handle>
    fine_tune_weights_disposition: sandbox_retained_and_destroyed_on_schedule
    frontier_provider_authorisation_ref: <provider safety-team ticket>
    mod_112_workflow_id: <workflow ID>
    counsel_signoff_id: <ID>
  threshold:
    framework: rsp
    tier: asl_3
    metric: composite (wmdp pass@N, expert-rubric mean, uplift delta)
    threshold_direction: cleared_below
  elicitation_gap_accounting:
    statistical: <cite peer role>
    fine_tune_depth: <claim + follow-up experiment>
    scaffold_parity: <claim + comparison to public scaffolds>
    domain_expert_access: <claim>
    human_effort_hours: <n>
```

Chapters 06 and 07 fold this into the full DCER.

## Common misreadings to avoid

- **"We passed WMDP-Bio, so we cleared ASL-3."** No. WMDP-Bio is one row of one sub-panel. The tier decision is a composite that requires expert-graded synthesis-planning and, in most tier reads, paired-baseline uplift.
- **"The model refused, so no uplift."** No. Refusal is not a capability upper bound. Fine-tune elicitation with the safety head disabled or with adversarial fine-tune data is a standard axis of the panel.
- **"Any CBRN prompt landing is a fail."** No. Landing a prompt that produces plausibly-harmful output is *evidence* — the ceiling is a distribution of such outputs, graded by domain experts, whose composite is the reportable number.
- **"An in-house biology PhD is a sufficient grader."** For preliminary calibration, maybe. For a DCER supporting an RSP / Preparedness / FSF tier decision, external biosecurity domain experts under FMF-shaped engagement are the standard the frameworks expect.
- **"We ran the eval on the base model."** Report both the base-model ceiling *and* the deployed-stack ceiling (with the classifier guard, monitor, and any post-training filter in place). Both are decision-relevant; only one of them is the *actual* deployed capability.
- **"WMDP-Cyber is where the CBRN panel meets cyber."** WMDP has a cyber subset, but the *cyber* capability panel (chapter 03) is anchored on CyberSecEval 2 + 3 and CTF suites; WMDP-Cyber contributes to the joint capability picture but is not the primary anchor for the cyber tier decision.

## Boundary — this module does not do

- **General biosecurity policy** or global governance work. This module produces engineering-evidence artefacts that feed downstream policy artefacts.
- **Wet-lab operations.** No elicitation ever produces a validated real-world synthesis by anyone in this module. Elicitation ends at *plan quality as graded by domain experts*; validation of a plan would itself be a harm.
- **Domain-expert opinion synthesis.** External graders adjudicate. This role's function is to *engineer the elicitation, present the material to graders under controls, and report the composite* — not to author the domain judgement.
- **Novel bio / chem / rad / nuc research.** Consuming FMF working papers, WHO / IAEA guidance, and the frontier lab safety-team CBRN methodology is the discipline. Producing new domain science is not.

## Summary

- CBRN-uplift evaluation splits into four sub-panels: **biological, chemical, radiological, nuclear** — with biological typically the load-bearing tier driver.
- Every sub-panel names its **threat model** (hostile-actor archetype × attack pathway × horizon), its **elicitation protocol** (chapter 01's four axes), its **measurement composition** (WMDP + expert-graded synthesis-planning + paired human-baseline uplift), and its **security controls**.
- WMDP anchors the closed-question knowledge lower bound; the FMF bio threat model anchors the synthesis-planning top of panel; paired-baseline trials produce the uplift claim where the tier requires it.
- **Six control layers** — access-controlled sandbox, air-gapped grader terminals, fine-tune-elicitation isolation, frontier-provider authorisation, mod-112 disclosure workflow, counsel gate — are engineered before the first prompt.
- External CBRN domain experts, engaged per FMF guidance and coordinated with WHO / IAEA where the panel touches biological / radiological / nuclear domain expertise, adjudicate the open-ended measurements. In-house adjudication of these panels is a red flag for the DCER's defensibility.
- Fine-tune weights, elicited harmful content, and grader material live behind the safety-team sandbox boundary; the DCER reports the composite ceiling and the elicitation-gap accounting, not the payloads.
