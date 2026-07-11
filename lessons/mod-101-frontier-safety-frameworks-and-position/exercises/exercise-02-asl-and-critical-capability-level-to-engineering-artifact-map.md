# exercise-02 — ASL / Preparedness tier / CCL → Engineering Artifact Map

**Estimated effort:** 3 hours
**Prerequisite chapters:** 02, 03, 04, 05 (chapter 06 helpful).

## Objective

Take a *single* frontier-safety tier from each of the three frameworks — one ASL from the RSP, one capability threshold from a Preparedness Framework category, and one CCL from the FSF — and produce a **concrete engineering-artifact map** for each: the elicitation protocol, the pre-registered threshold, the tripwire configuration, the rollback contract, and the evidence artifact you would author to defend the tier decision.

The exercise forces you out of framework abstraction and into the actual *artifact* the safety team ships.

## Problem statement

You are the responsible safety engineer for one frontier model's tier decision on each of the three framework lanes. Your job is *not* to run the evaluations — this module is framework-fluency, not eval-execution (mod-106 owns that). Your job is to author the **artifact skeleton** each tier requires, so that when a downstream module runs the eval and populates the results, the artifact shape is already correct and defensible.

You will pick three tiers total:

- **RSP tier.** Pick one non-trivial ASL from the currently published RSP (e.g., the ASL-3 tier or the tier that most current frontier models are being classified into). Use the RSP's own language.
- **Preparedness Framework tier.** Pick one category and one threshold within that category from the currently published Preparedness Framework. Use the framework's own language.
- **FSF CCL.** Pick one CCL from the currently published FSF. Prefer a *misalignment* CCL if the framework offers one — this forces you to reach into the mod-110 territory. Use the framework's own language.

For each of the three tiers you pick, author an artifact-skeleton document.

## Requirements

For each of the three tiers, author an artifact-skeleton document with the following sections:

1. **Tier statement.** Paste the tier language verbatim from the current published framework. Cite the version and URL.
2. **Behaviour and signal.** What behaviour does the tier reference? What signal in an eval would demonstrate the tier is being approached or crossed?
3. **Elicitation protocol.** Describe the elicitation protocol you would run:
   - Task suite. Ground it in a public benchmark where one applies (WMDP, METR public tasks, RE-Bench, SWE-bench Verified, CyberSecEval 2 / 3, InjecAgent, AgentDojo, HarmBench, StrongREJECT-graded harmful-behaviour battery, etc.). Cite the benchmark.
   - Enhancements permitted at the tier (best-of-N, fine-tuning, tool-use, scaffolds).
   - Grader methodology (auto-grader, LLM-judge with StrongREJECT-shape rubric, external domain-expert grader, ensemble).
   - Sample count and confidence-interval discipline.
   - Elicitation-gap acknowledgement.
4. **Pre-registered threshold.** State the specific numerical or qualitative threshold that would cause the tier decision to flip, in the framework's own language. If the framework does not publish a numerical threshold, state a defensible internal threshold you would pre-register.
5. **Tripwire configuration.** Author a YAML block describing:
   - The signal being watched (post-deployment: what monitors fire? pre-deployment: what eval delta fires?).
   - The threshold the tripwire enforces.
   - The response the tripwire triggers (pause, additional mitigation, escalate, rollback).
   - The audit trail.
6. **Rollback contract.** Answer, precisely: *if this tier decision turns out to be wrong post-deployment, what does "roll back" mean?* Name the specific artifacts, model versions, monitoring configurations, and communications lanes involved.
7. **Evidence artifact skeleton.** Sketch the outline of the evaluation report you would produce. Include the audience (Responsible Scaling Officer / Preparedness Safety Advisory Group / FSF review body), sections, evidence types, cross-references.
8. **Deferral contract.** Use the YAML template from chapter 01: name the level that owns the artifact, the levels it defers down / sideways to, and the levels that consume it upward.
9. **Cross-framework mapping note.** Where does this evidence populate the *other two* frameworks' shapes? (E.g., an ASL-3 elicitation report may also populate a Preparedness cyber-uplift threshold and a FSF cyber-CCL.)

## Starter guidance

- Do **not** run the eval. This is an artifact-shape exercise. If you find yourself specifying seeds and randomness, you have drifted into mod-106.
- Do **not** invent thresholds. If the framework does not publish one, pick a defensible internal-baseline threshold *and mark it clearly as internal*.
- Do reach for the public benchmarks — this makes the artifact concrete. If you cannot ground the elicitation protocol in a real benchmark, the artifact skeleton is too abstract.
- For the misalignment CCL, use the deception / alignment-faking / sandbagging / sleeper-agent / in-context-scheming literature (see the pointers threaded through chapter 04 and the resources file). Do not skip this because it feels harder — it is the load-bearing case for the FSF.
- Reuse structure across the three tiers. The point is that the shape is the same; only the framework vocabulary changes.

## Acceptance criteria

- ✅ Three artifact-skeleton documents, one per framework.
- ✅ Each document contains all nine sections named above.
- ✅ Each document quotes the tier language verbatim and cites the framework version.
- ✅ Each elicitation protocol grounds itself in a specific public benchmark.
- ✅ Each tripwire is expressed as a valid YAML block with signal / threshold / response / audit-trail fields.
- ✅ Each rollback contract names specific artifacts and lanes, not "we roll back if needed".
- ✅ Each evidence artifact skeleton has an explicit audience.
- ✅ Each deferral contract uses the chapter-01 YAML shape.
- ✅ The cross-framework mapping note ties each artifact to at least one other framework's shape.
- ✅ Any internal-baseline threshold is *marked* as internal, not attributed to the framework.
- ✅ Inline `<!-- needs-research: ... -->` markers wherever your reading of the current framework version is uncertain.

## Stretch goals

- Produce the three skeletons as **Inspect** `Task` stubs — solvers, scorers, and datasets defined but not implemented. This is the first step toward mod-111 orchestration.
- Add a fourth artifact skeleton for the **EU AI Act Article 55** systemic-risk assessment, populated by the same underlying evaluation but structured for the EU AI Office rather than a lab review body.
- Add a small threat-model section at the top of each artifact naming the specific adversary personas (script-kiddie jailbreaker, insider, nation-state uplift-seeker) whose actions the tier decision is protecting against. This bleeds into mod-102 but tightens the artifact.

## Deliverable location

Place your three artifact skeletons in your personal notes or a private repo. Do not commit them to this course repo. The paired solutions repo carries reference skeletons.
