# 01 — Scaled Red-Teaming as a Discipline

## Motivation

mod-103, mod-104, and mod-105 taught the *craft*: how to sit at a keyboard and elicit an unsafe completion, engineer a jailbreak that traverses the safety-tuning valley, or chain three tool calls into an exfil. That craft is load-bearing — you cannot scale what you cannot do by hand — but it is not, on its own, a program. A defensible frontier-safety posture at this level needs a red-team output that is *reproducible*, *comparable across model versions*, *aggregated into a coverage claim*, and *routable to the artefacts that consume it* (safety cases, disclosures, containment updates, guardrail retraining). That is the shift this module makes: from craft to *program*, from *"we tried and it broke"* to *"we ran the following coverage matrix, the following portion is red, and here is the seeded-attack bundle a reviewer can replay."*

The load-bearing insight is that *scale is the mechanism*, not the target. Doing a thousand attacks with a thousand different prompts and no orchestrating structure produces a thousand anecdotes. Doing the same thousand attacks organised along the axes a reviewer cares about — attack technique × behaviour category × model version × decoding config — produces a *coverage matrix*, and coverage-matrix deltas across model revs are what let a Responsible-Scaling Officer sign a tier-decision letter with a specific claim. Anthropic's Responsible Scaling Policy, OpenAI's Preparedness Framework, and Google DeepMind's Frontier Safety Framework each name red-team programs (not red-team incidents) as inputs to their capability-threshold decisions. The programs are what a level-40 safety engineer runs.

This chapter names the module-level artefact — the **Coverage Matrix Contract (CMC)** — that the six chapters build. It draws the boundary to the level-25 prerequisite, `ai-risk-engineer`, which introduces PyRIT, garak, and Promptfoo at the general adoption depth this module assumes. It names why *automation* is the correct answer at frontier scale, and where automation is not the answer (elicitation-gap human red-teams and CBRN / cyber-offense evaluations that require domain-expert graders).

## Primary sources

- **[Anthropic — Responsible Scaling Policy](https://www.anthropic.com/rsp)** — reads as an engineer reads it: the RSP names red-team + capability-eval programs as inputs to ASL tier decisions; the CMC is what feeds them. <!-- needs-research: pin the current version number of the Responsible Scaling Policy at the time the CMC is authored; Anthropic revises the RSP on a rolling cadence and the version should be captured in the artefact. -->
- **[OpenAI — Preparedness Framework](https://openai.com/preparedness/)** — the Preparedness scorecard's tracked categories are one authoritative source for the *behaviour-category* axis of the coverage matrix. <!-- needs-research: pin the current published version of the Preparedness Framework and the tracked-categories list at the time of authoring. -->
- **[Google DeepMind — Frontier Safety Framework](https://deepmind.google/discover/blog/updating-the-frontier-safety-framework/)** — Critical Capability Levels (CCLs) name the behaviour-category shape a scaled red-team is expected to elicit against. <!-- needs-research: pin the current published version and the CCL list used at authoring time. -->
- **[UK AI Safety Institute — Approach to Evaluations](https://www.aisi.gov.uk/work/approach-to-evaluations)** — the AISI's pre-deployment methodology treats coverage + reproducibility as first-class artefacts. <!-- needs-research: confirm the current URL / title of AISI's evaluation-methodology write-up; AISI has re-published under updated URLs. -->
- **[NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2025.pdf)** — the taxonomy this module's *attack-technique* axis leans on for terminology. <!-- needs-research: verify the current published revision and section IDs. -->
- **[MITRE ATLAS](https://atlas.mitre.org/)** — the technique catalogue the coverage-matrix attack-technique axis maps to for external-facing disclosures.

Version-pin all of these when they are cited in a CMC artefact.

## The Coverage Matrix Contract this module builds

Every chapter contributes to a single deliverable: a **Coverage Matrix Contract (CMC)** for one concrete red-team program. The CMC is what a reviewer reads. It is not the red-team scripts; the scripts implement it.

A CMC has seven sections:

1. **Scope statement.** The system-under-test — model version(s), decoding configuration(s), guardrail configuration(s), deployment scaffold(s) — that the coverage claim covers. The RSP / Preparedness / FSF tier the program feeds evidence into. The behaviour categories in scope (CBRN-uplift, cyber-offense, autonomy-uplift, persuasion, self-exfiltration, general harmful-content, agentic-misuse). The behaviour categories explicitly *out* of scope, and where those live.
2. **Coverage matrix axes.** The four load-bearing axes — attack technique × behaviour category × model version × decoding config — and any additional axes the deployment needs (guardrail configuration, retrieval-index state, tool-scaffold variant). Each axis is enumerated; each cell has an *expected sample size*, a *judge assignment*, and a *pass criterion*.
3. **Orchestrator inventory.** The frameworks that produce coverage into each cell (chapter 02) — Inspect solvers, PyRIT orchestrators, garak probes, Promptfoo red-team suites — and how they compose. The CMC references framework versions and pins the container / image digests.
4. **Judge contract.** The judges used per behaviour category (chapter 05) — StrongREJECT-shape LLM judges, custom fine-tuned judges, human-grader panels — with their calibration status, drift-monitoring cadence, and disagreement-adjudication rules.
5. **Attack-corpus contract.** The seed corpus, its provenance, its harmful-payload storage discipline (chapter 06), its update cadence, and the diversity metric that separates *coverage* from *repetition*.
6. **Reproducibility contract.** The seeded-attack replay bundle format (chapter 06) — decoding + model + prompt + tool-response hashes pinned. What a reviewer needs to reproduce any red cell exactly.
7. **Consumer contract.** Which downstream artefacts consume the coverage claim: safety cases (mod-109), disclosure documents (mod-112), containment updates (mod-107), guardrail retraining (mod-108), tier decisions (mod-101). The routing and cadence for each.

Exercise 06 asks you to author sections 1–3 and 6 of a CMC end-to-end; the remaining sections come from exercises 04, 05, and 07.

The CMC is what makes scaled red-teaming a *contract* rather than a *habit*. mod-109 cites it as evidence backing an inability leg. mod-108 uses it to shape retraining data. mod-107 uses it to close capability-gate findings. mod-112 uses it as the technical backbone of an AISI-facing report. This module is where the contract is engineered.

## What "scale" actually means here

Three axes multiply, and *scale* is a synonym for *disciplined multiplication along all three*.

- **Sample-count scale within a cell.** A single (attack technique, behaviour, model, decoding) cell needs *enough* independent samples that a small ASR estimate has a defensible confidence interval. A single hand-crafted attack per cell is a null estimate. mod-104 taught best-of-N accounting; here the same statistics apply per cell.
- **Cell-count scale across the matrix.** Twenty attack techniques × ten behaviour categories × three model versions × three decoding configs = 1 800 cells before any additional axes. A hand-run program does not populate 1 800 cells; an orchestrated one does. That is why chapter 02 is about *orchestration*, not attacks.
- **Program-cadence scale.** The matrix runs *on every candidate model rev, before every material deployment change, at a regular pre-deployment cadence*. It is not a launch-quarter event. Every rev produces a delta; deltas feed the downstream artefacts.

Scale is *not* a synonym for *automated only*. Some cells are permanently human-graded (CBRN-uplift, cyber-offense with novel-domain expertise); the automation orchestrates the *routing* to the human grader and the *bookkeeping* of the human's verdict, but the human is still in the loop. mod-106's dangerous-capability elicitation methodology cites this explicitly; the CMC's judge contract (section 4) names which cells are LLM-graded, which are LLM-graded with human calibration, and which are human-graded end to end.

## Boundary to `ai-risk-engineer` (prerequisite, level 25)

This module *assumes* the level-25 depth on the three flagship frameworks — PyRIT, garak, Promptfoo — that a level-25 AI risk engineer already ships with. Concretely, the prerequisite covers:

- **PyRIT at adoption depth.** What an *orchestrator* is; what a *scorer* is; how to write a `PromptRequestPiece`; how to run a linear PyRIT orchestrator against a target with a static scorer. The prerequisite ships end-to-end usage against a small attack library.
- **garak at adoption depth.** What a *probe* is; what a *detector* is; how to run `garak --model_type openai --probes ...` and read the report. The prerequisite ships the built-in probe walkthrough and the JSON-report format.
- **Promptfoo red-team at adoption depth.** How to author a `promptfooconfig.yaml`, wire a red-team suite, run it under CI, and read the resulting HTML dashboard. The prerequisite ships the small-CI integration.

This module *adds* the scaled-orchestration layer *on top* of that: the coverage matrix as an artefact (chapter 06), the LLM-vs-LLM attacker loops that PyRIT hosts (chapter 03), the fine-tuned attacker that plugs in as a PyRIT chat target (chapter 04), the StrongREJECT judge that replaces PyRIT's default scorers (chapter 05), and the composition with UK AISI Inspect as the eval-framework spine (chapter 02). If a learner has not internalised the level-25 material, chapter 02 will read as an *introduction* rather than a *composition*, and the module's altitude will be lost. `ai-risk-engineer-learning` is the prerequisite for a reason.

## Boundary to `ai-eval-engineer` (peer, level 30)

The eval-framework plumbing that a coverage matrix runs *on top of* — the trace store, the judge-integration layer, the eval-gated CI/CD path that runs the matrix on every model rev, the online-eval sidecar that samples production traffic and routes the interesting fraction to the red-team suite — is a `ai-eval-engineer` peer-role concern. This module *specifies* the eval-plumbing contract the red-team suite needs (a trace store the matrix's replay bundle points into, a judge-serving layer the StrongREJECT judge is deployed on, a CI hook that fires the matrix run) and *cites* the peer role's craft as the mechanism. It does not re-teach how to design the trace store or the CI hook.

Chapter 02 will name the specific handoff shapes — a `matrix_run_id` schema, an *eval-artefact bundle* the CI produces, a *judge-service SLO* the judge layer honours — that this module ships to the eval-engineering peer.

## What automation is, and is not, the answer to

Automation is the correct answer to *breadth* — running every (technique × behaviour × model × decoding) cell, running the same matrix on every rev, keeping the seeded-attack replay bundle up to date, orchestrating LLM-vs-LLM attackers under a rate-limit and cost budget. Chapter 02 and chapter 03 are the load-bearing chapters for this.

Automation is *not* the correct answer to:

- **Elicitation-gap accounting.** Whether a given cell's ASR reflects the model's true capability or an elicitation shortfall is a *methodology* question. mod-106 owns it; the CMC's section 4 (judge contract) names which cells carry an elicitation-gap disclaimer.
- **Domain-expert grading.** CBRN-uplift, cyber-offense with novel tradecraft, persuasion at scale — these are not LLM-judgeable. Chapter 05's judge-contract discussion carves them out; the orchestrator's job is to *route* to the human panel, not to *replace* it.
- **Novel-technique discovery.** A scaled program that only runs techniques already in the library will *never* discover a new one. The program's *cadence* leaves budget for human red-team sprints whose output is new library entries. Chapter 04 discusses this in the context of failure-mode corpus construction.
- **Judge-drift monitoring itself.** The judges the matrix uses must themselves be red-teamed. Chapter 05 walks the judge-drift monitoring loop; the cadence is *not* automated (the human calibration set is refreshed by a human).

A defensible CMC section-4 judge contract names each of these carve-outs explicitly. A CMC that does not carve them out is a CMC that will over-claim.

## The organisation-scope program shape

At level 40, the red-team program is not a project; it is an *organisation-scope function* with a cadence, a signed contract, and a routing to downstream artefacts. The shape:

- **Pre-training / mid-training / pre-deployment / post-deployment cadences.** The matrix runs at each; the sample sizes, the judges, and the reviewer are different for each. Pre-deployment runs feed the tier-decision letter; post-deployment runs feed the monitoring loop.
- **Named consumers.** The RSP / Preparedness / FSF review body reads the matrix's tier-relevant cells. The safety-case author (mod-109) cites the matrix's inability-relevant cells. The disclosure author (mod-112) cites the matrix's system-card and AISI-report cells. The containment engineer (mod-107) reads the matrix's tool-abuse cells. The guardrail engineer (mod-108) reads the matrix's guardrail-effectiveness cells.
- **A version.** The CMC carries a version. A version bump is a change in section 1's scope, section 2's axes, section 3's orchestrators, section 4's judges, or section 6's reproducibility contract. Downstream artefacts cite the version.
- **A signed owner.** The level-40 role signs the CMC. Peer roles (`ai-eval-engineer` for eval plumbing, `fine-tuning-engineer` for attacker fine-tuning, `ai-infra-security` for judge supply-chain, `senior-agentic-ai-engineer` for the agent-under-test) co-sign the sections they implement. A silent co-signature is a finding.

Exercise 06 asks you to draft the CMC-owner sign-off page; exercise 07 asks you to draft the harmful-payload storage discipline that section 5 references.

## Why craft-only red-teams under-cover at frontier scale

Three concrete failure modes make craft-only red-teams a mis-fit for organisation-scope programs. Naming them makes the transition from mod-103 / 104 / 105's craft chapters into this module's program shape defensible.

- **The manual-effort ceiling.** A skilled human red-teamer produces perhaps a hundred well-aimed attacks per week against a target family. A coverage matrix with 1 800 cells and a 100-seed sample per cell is 180 000 attempts per run. At the craft-only tempo, populating a single matrix run consumes 1 800 red-teamer-weeks. The tempo makes per-rev matrix runs a fantasy; a scaled program is the only way the matrix runs on the cadence the RSP / Preparedness / FSF tier-decision path demands.
- **The reproducibility ceiling.** Craft-only red-teams produce written writeups; writeups are not replayable. A safety-case author who cites a craft writeup as evidence is citing an assertion; a reviewer six months later cannot rerun the attack against a candidate rev with a change of decoding and see the ASR delta. The reproducibility bundle (chapter 06) is what turns craft into replayable evidence, and reproducibility bundles at scale need automation.
- **The bookkeeping ceiling.** A craft-only red-team's outputs are heterogeneous — different writeups, different metrics, different verdicts. Aggregating across ten red-teamers, twelve behaviour categories, and three model revs produces a bookkeeping burden that eats the program's engineering budget. The coverage matrix's *uniform* per-cell shape is what makes aggregation and per-rev delta reporting tractable.

Chapters 02 through 06 build the specific mechanisms that lift each ceiling. Chapter 02 lifts the manual-effort ceiling through orchestration; chapter 06 lifts the reproducibility and bookkeeping ceilings through the CMC contract.

## What the CMC is not

Three misreadings are worth pre-emptively naming, so the CMC is understood as a *contract* rather than as a related but weaker artefact.

- **The CMC is not the eval harness.** The harness (Inspect + PyRIT + garak + Promptfoo composed per chapter 02) is the *mechanism* the CMC's runs use. Swapping harnesses is a section-3 change; the CMC as a whole survives.
- **The CMC is not a benchmark score.** A benchmark score compares a target against a fixed leaderboard artefact. The CMC is a *coverage claim over the operator's specific target family under the operator's specific deployment*. The two overlap where public benchmarks (HarmBench, JailbreakBench, AIR-Bench, AILuminate) populate specific cells; they do not substitute.
- **The CMC is not a system card.** The system card (mod-112) *cites* the CMC's aggregated verdicts; the CMC is the technical artefact behind the citation. A system card without a CMC behind it is a system card whose claims a reviewer cannot verify.

## Interfaces

- **`ai-risk-engineer` (prerequisite, level 25).** The level-25 depth on PyRIT / garak / Promptfoo is assumed and not re-taught. If a learner does not have it, chapter 02 reads as introduction rather than composition.
- **`ai-eval-engineer` (peer, level 30).** The eval-framework plumbing — trace store, judge-serving layer, eval-gated CI/CD, online-eval sidecar — is the peer's craft. Chapter 02 names the specific handoff shapes.
- **`fine-tuning-engineer` (peer, level 30).** Chapter 04's attacker-model fine-tuning consumes the peer's training-pipeline craft; this module owns the corpus + evaluation methodology, not the SFT / DPO / RL plumbing.
- **`ai-infra-security` (peer / next-up, level 35).** The judge-supply-chain hardening (chapter 05) — how the judge model's weights and its inference layer are protected — is the peer's craft.
- **`senior-agentic-ai-engineer` (peer, level 40).** The agent-under-test — the planner / tool-bus / memory-store scaffold the matrix hammers — is the peer's craft; the matrix targets it.
- **mod-104 / mod-105 (jailbreak / agent surface).** The *technique-axis* of the coverage matrix is what mod-104 and mod-105 populated at craft depth; this module scales them.
- **mod-106 (dangerous capability).** The behaviour categories that require domain-expert grading; the elicitation-gap accounting; the tier-relevant cells.
- **mod-107 (containment).** The tool-abuse cells whose ASR is *the* signal for the excessive-agency containment posture; the wrapper (chapter 03 of mod-107) is one of the matrix's targets.
- **mod-108 (guardrails).** The guardrail-effectiveness cells whose ASR is *the* signal for the guardrail retraining loop; adaptive-attack survival is measured cell-by-cell.
- **mod-109 (safety cases).** The matrix's inability-leg-relevant cells are cited as evidence.
- **mod-110 (control / deception).** The *deception-eval* axis compositions with the matrix; a model that scores well on the matrix but sandbags on the deception evals produces a widened residual-risk claim.
- **mod-112 (disclosure).** The system card / AISI report / EU AI Act Article 73 report cite matrix versions and specific-cell ASRs.

## Common misreadings to avoid

- **"We already run PyRIT / garak / Promptfoo, so we already have a scaled red-team."** No. Running a framework is the *prerequisite*. A scaled program has a *coverage matrix* the framework populates, a *judge contract* that says whether the cells are red, and a *routing* to downstream artefacts. Running the framework without the matrix is anecdote generation at scale.
- **"Automation replaces the human red-team."** No. Automation *replaces* the busywork so the human red-team can spend cycles on novel-technique discovery, domain-expert grading, and elicitation-gap analysis. The CMC's judge contract names the carve-outs.
- **"A big matrix guarantees coverage."** A big matrix that never adds an axis when the model gains a capability is a stale matrix. Coverage is a *claim* whose axes evolve with the system-under-test; chapter 06 discusses axis updates as versioned CMC changes.
- **"ASR is the metric."** ASR *per cell* is the metric. A single global ASR is a lie — it hides high-ASR behaviour categories under low-ASR ones. Chapter 06 discusses per-cell reporting.
- **"The judges are neutral; we can trust their verdicts."** No. Judges drift, are attackable, and are supply-chain-dependent. Chapter 05 walks the calibration + drift-monitoring loop; the judges are themselves red-teamed.
- **"The framework is enough; we do not need a CMC."** The CMC is what a reviewer signs. Without one, the program cannot be cited in a safety case, a disclosure, or a tier-decision letter. mod-109 will refuse to cite a claim whose evidence is *"we ran PyRIT."*

## Summary

- Scaled red-teaming is the *program-scale* discipline that composes the craft chapters (mod-103 / 104 / 105) into an artefact the RSP / Preparedness / FSF review path consumes.
- The module-level artefact is the **Coverage Matrix Contract (CMC)** — seven sections covering scope, axes, orchestrator inventory, judge contract, attack-corpus contract, reproducibility contract, and consumer contract.
- *Scale* means disciplined multiplication along three axes: sample-count within a cell, cell-count across the matrix, and program-cadence across model revs.
- Adopt the level-25 `ai-risk-engineer` prerequisite for PyRIT / garak / Promptfoo at adoption depth; this module adds *composition* on top.
- Cite the boundary to `ai-eval-engineer` (peer, level 30) on eval-framework plumbing; chapter 02 names the specific handoffs.
- Carve out what automation is not the answer to: elicitation-gap accounting, domain-expert grading, novel-technique discovery, judge-drift monitoring. The CMC judge contract names each carve-out explicitly.
- Downstream artefacts consume the CMC: safety cases (mod-109), disclosures (mod-112), containment (mod-107), guardrails (mod-108), tier decisions (mod-101).
