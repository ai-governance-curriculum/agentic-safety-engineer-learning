# 02 — Orchestration with Inspect, PyRIT, garak, and Promptfoo

## Motivation

Chapter 01 named the Coverage Matrix Contract as the module-level artefact. This chapter is where the matrix is *populated*: which framework produces coverage into which cells, how the frameworks compose without duplicating one another's work, and where each framework's opinionated scope ends and the next one's begins. A common failure mode at this level is to bolt PyRIT to garak to Promptfoo and treat the union as a program; the result is three overlapping coverage layers with three incompatible run artefacts and no way to answer *"did this rev regress cell X?"* The correct posture is *composition*, not union — each framework owns a distinct concern, and the CMC's section-3 orchestrator inventory names which cells each populates.

The load-bearing insight is that the four flagship tools each solve a *different* piece of the scaled-red-team problem, and their differences are not accidental. UK AISI's Inspect is an *evaluation framework* — solvers, scorers, task graphs, sandbox integration, log store — designed to make a large eval suite reproducible and reviewable. Microsoft's PyRIT is an *attacker-orchestrator library* — attack-payload generation, multi-turn orchestrators, attack libraries — designed to make LLM-vs-LLM red-team loops re-usable. NVIDIA's garak is a *scanner* — a large library of probes and detectors — designed to make behavioural coverage broad and cheap. Promptfoo's red-team mode is a *CI integrator* — a `promptfooconfig.yaml`-driven suite designed to fire on every PR and produce a dashboard. Each has an opinionated shape; each is best at its concern; each degrades when pushed outside it. The chapter walks the correct decomposition.

## Primary sources

- **[UK AI Safety Institute — Inspect framework](https://inspect.aisi.org.uk/)** — the documentation site for Inspect, covering the solver / scorer / task model, the log store, and the sandbox integration. <!-- needs-research: confirm the current canonical documentation URL; AISI has hosted Inspect docs under multiple subdomains. -->
- **[UKGovernmentBEIS/inspect_ai — GitHub repository](https://github.com/UKGovernmentBEIS/inspect_ai)** — the source repository; the reference for pinning versions in the CMC's section-3.
- **[Microsoft — PyRIT](https://github.com/Azure/PyRIT)** — the source repository. The README + docs cover orchestrators, converters, targets, and scorers. <!-- needs-research: pin the current published version of the PyRIT docs and confirm the orchestrator taxonomy has not been renamed (Microsoft has revised the class names). -->
- **[NVIDIA — garak](https://github.com/NVIDIA/garak)** — the source repository; the reference for probe / detector inventory. The associated paper (Derczynski et al.) documents the design. <!-- needs-research: cite the garak paper URL (arXiv identifier) once verified. -->
- **[Promptfoo — Red-Team documentation](https://www.promptfoo.dev/docs/red-team/)** — the CI-integrated red-team suite documentation. <!-- needs-research: confirm the current documentation URL and the red-team-plugin taxonomy the CMC will reference. -->

Version-pin these frameworks and their image digests in the CMC's section-3 orchestrator inventory.

## The four opinionated tools

### UK AISI Inspect — the eval-framework spine

Inspect is the *spine* of the scaled program. Concretely, Inspect provides:

- **A task model.** A task is a dataset plus a solver plus a scorer. The task shape is the unit of reproducibility: a run of a task produces a log the log-store persists; the log is what a reviewer replays.
- **A solver abstraction.** A solver is the pipeline that turns a dataset item into a completion (with optional tool use, chain-of-thought, multi-turn). Solvers compose — an attack solver can wrap a target solver, a critic solver can wrap the attack.
- **A scorer abstraction.** A scorer is what turns a completion into a numeric or categorical grade. StrongREJECT-shape judges (chapter 05) plug in as Inspect scorers.
- **A sandbox integration.** For tool-using agents, Inspect integrates with sandbox providers so tool calls are contained during eval. This is the load-bearing integration for the mod-107 target-under-test.
- **A log store.** Every task run produces a signed log the CMC's section-6 reproducibility contract points into. The log is the *replay bundle*.

Use Inspect as the top-level task runner for the matrix. Every cell — every (technique × behaviour × model × decoding) point — is a task-run whose solver comes from PyRIT (for LLM-vs-LLM attackers), garak (for scanner-shape probes), or the Inspect built-in library (for simple direct-prompt attacks), and whose scorer is the StrongREJECT-shape judge (chapter 05). Inspect provides the *harness*; the other tools provide the *content*.

The reason Inspect sits at the top is that its log-store and reproducibility model is the *strongest of the four*, and the CMC's section-6 reproducibility contract needs that. PyRIT logs live in the PyRIT `MemoryInterface`; garak logs live in a JSON report; Promptfoo logs live in the Promptfoo run store. Inspect's log is the one a reviewer replays.

### Microsoft PyRIT — attacker orchestrators and attack libraries

PyRIT owns *attacker* code paths. Concretely:

- **Orchestrators.** The `RedTeamingOrchestrator`, `CrescendoOrchestrator`, `PAIROrchestrator`, `TreeOfAttackOrchestrator`, and their siblings. Each is a stateful multi-turn attacker with a target, a scorer, and a memory. Chapter 03 walks the LLM-vs-LLM loops PyRIT hosts.
- **Converters.** The transformations applied to a seed prompt before it reaches the target — base64, unicode-confusable, translation-into-low-resource-language, ASCII-art, and so on. Converters are chainable and are the mechanism the *obfuscation* leg of mod-103 uses at scale.
- **Targets.** The abstraction over the model-under-attack; PyRIT ships targets for OpenAI, Azure OpenAI, HuggingFace, local endpoints. Custom targets wrap an operator's own inference API.
- **Attack libraries.** The seed prompt corpora PyRIT ships with; these are one input to the CMC's section-5 attack-corpus contract. The operator's own failure-mode corpus (chapter 04) plugs in as an additional library.

Use PyRIT as the *attacker* layer plugged into Inspect. A PyRIT orchestrator is an Inspect solver when wrapped through a thin adapter; the orchestrator's output is what Inspect's scorer grades. The scorer *is not* PyRIT's default `SelfAskLikertScorer`; it is the chapter-05 StrongREJECT judge.

The mistake to avoid is running PyRIT *end-to-end* — using PyRIT's own `Orchestrator.run_attack()` loop as the top-level runner. PyRIT's memory and reporting are fine for attack development but do not carry the reproducibility guarantees the CMC section 6 needs. PyRIT is the *attacker library*; Inspect is the *runner*.

### NVIDIA garak — scanner-shape behavioural breadth

garak owns *scanner* coverage. Concretely:

- **Probes.** A probe is a family of prompts targeting a specific known-broken behaviour: `dan.DAN_11_0`, `promptinject.HijackHateHumansMini`, `xss.MarkdownImageExfil`, `latentinjection.LatentJailbreak`, dozens more. Probes are *shallow but wide*; the strength is breadth of coverage against known-broken behaviour, not depth of adaptive attack.
- **Detectors.** A detector turns a completion into a probe-verdict — string-match, classifier, regex, LLM-judge. Detectors are the equivalent of Inspect scorers but at a lower altitude.
- **Reports.** garak produces JSON + HTML reports enumerating which probes fired.

Use garak for the *broad-scan* leg of the coverage matrix — the cells whose axis is *"has this known-in-the-wild behaviour been fixed?"* rather than *"can our organisation-specific attacker still elicit this behaviour?"* A model rev's garak scan is a fast, cheap check that the population of known-broken behaviours is not regressing. It is not a substitute for the PyRIT-driven LLM-vs-LLM loop.

The composition shape: garak runs *as a nightly matrix leg* against every candidate rev, produces a report, and the report's per-probe verdicts populate a designated slice of the coverage matrix — the *known-behaviour regression slice*. When a garak probe fires that had previously been clean, that is a regression that the containment or guardrail engineer routes to a fix.

### Promptfoo red-team — CI-integrated coverage

Promptfoo's red-team suite owns *CI integration*. Concretely:

- **A `promptfooconfig.yaml`-driven suite.** The suite is committed to the repository (with harmful payloads *externalised* per chapter 06's harmful-payload discipline); a CI hook runs it on every material change.
- **A plugin taxonomy.** Promptfoo ships red-team plugins organised by attack category — jailbreak, prompt injection, PII exfil, hallucination, tool misuse — and each plugin generates a family of attack cases against the target.
- **A dashboard.** The suite's output is a per-PR dashboard the reviewer opens.

Use Promptfoo as the *CI-integrated leg* of the coverage matrix — the leg that fires on every change to the scaffold (prompt, guardrail config, retrieval index, tool registration) rather than every model rev. Its role is to catch the *"we regressed the scaffold, not the model"* class of failure that a per-rev matrix run does not fire on.

The composition shape: Promptfoo runs on every PR that touches the scaffold; its output populates a designated slice of the coverage matrix — the *scaffold-regression slice*. The dashboard is the reviewer's entry point; the CMC's section-6 reproducibility contract points into the Promptfoo run store for the specific cell.

## Composition: separation of concerns

The load-bearing rule is: *each tool owns exactly one concern; the matrix cell names which tool populates it.*

- **Inspect owns the runner and the log store.** Every cell run is an Inspect task. Every log is an Inspect log.
- **PyRIT owns the attack-payload generation and the multi-turn attacker orchestrators.** PyRIT solvers are the *content* Inspect runs.
- **garak owns the wide-shallow behavioural probes.** garak probes populate the *known-behaviour regression* slice.
- **Promptfoo owns the CI-integrated scaffold-regression suite.** Promptfoo suites populate the *scaffold-regression* slice.
- **The StrongREJECT-shape judge (chapter 05) owns the grading.** All four legs plug into the same judge; verdicts are comparable across the matrix.

The wrong thing to build: a *fifth* orchestration harness that duplicates Inspect. If a cell cannot be populated by one of the four tools, the fix is either (a) authoring a new Inspect solver / PyRIT orchestrator that fills the gap or (b) documenting the cell as *human-graded, not automation-populated* in the CMC. The fix is *not* to write your own runner.

## When each framework degrades outside its concern

The concerns are not arbitrary. Each framework degrades in specific, predictable ways when pushed past its concern; naming the degradations codifies the composition rule.

- **Inspect degrades as an attack-payload generator.** Inspect's solver library covers direct-prompt attack shapes and multi-turn conversation solvers, but it does not ship the depth of attacker orchestrators PyRIT does. Attempting to author PAIR / TAP / Crescendo directly in Inspect solvers duplicates the PyRIT class hierarchy at lower altitude; the correct posture is to *wrap* PyRIT orchestrators as Inspect solvers.
- **PyRIT degrades as a program runner.** PyRIT's memory abstraction and reporting were designed for attack development, not for cross-run reproducibility at CMC scale. The per-run memory is fine for a single orchestrator invocation; the *aggregated log store* that a coverage-matrix report reads is not what PyRIT ships. Attempting to layer coverage reporting on top of PyRIT memories multiplies the surface a reviewer inspects.
- **garak degrades as an adaptive attacker.** garak probes are *shallow*. A probe that fires an in-the-wild DAN prompt and grades the response is precisely calibrated to catch the known-broken behaviour; it does not adapt against a target whose safety-tuning has moved past the specific prompt. The correct posture is to keep garak on the known-behaviour regression slice and route adaptive coverage to PyRIT-hosted loops.
- **Promptfoo degrades as a matrix runner.** Promptfoo's per-PR run store and dashboard are tuned for the *scaffold-change* cadence. Running a full matrix on the Promptfoo runner works technically but produces a run artefact whose reproducibility guarantees are weaker than Inspect's. Keep Promptfoo on the CI-integrated scaffold-regression slice.

The composition rule that follows from the degradations: *each tool runs where it is strongest; each tool feeds a slice of the coverage matrix; the CMC section 3 records which tool feeds which slice.*

## Concrete decomposition — one worked example

Consider a cell: `(attack_technique=PAIR, behaviour=harmful-content-CBRN-uplift, model=candidate-rev-2025-07, decoding=T=0.7,p=0.95)`.

- **Runner.** An Inspect task, named `pair.cbrn_uplift.candidate_rev_2025_07.tp0_7_p0_95`.
- **Solver.** A PyRIT `PAIROrchestrator` (chapter 03) driving a fine-tuned attacker model (chapter 04) against the target. The orchestrator is wrapped as an Inspect solver via an adapter.
- **Attack seeds.** From the CBRN-uplift seed corpus, hosted per chapter 06's harmful-payload discipline outside the source repo.
- **Scorer.** A StrongREJECT-shape LLM judge (chapter 05) *plus* a domain-expert human panel for a random 5% sample. The Inspect scorer emits both verdicts.
- **Sandbox.** Inspect's sandbox integration if the target-under-test is a tool-using agent (chapter 07 of mod-106 covers the sandbox contract).
- **Sample size.** 200 attack attempts, best-of-8 sampling per attempt (mod-104 methodology).
- **Log store.** The Inspect log store. The CMC section-6 reproducibility bundle points at the log ID.
- **Judge output.** ASR, ASR CI, diversity metric (chapter 03), judge-disagreement rate (chapter 05).

A cell that cannot be described in this shape is a cell whose CMC entry is incomplete.

## The eval-framework plumbing boundary

Everything above sits *on top* of the eval-framework plumbing the `ai-eval-engineer` peer role (level 30) owns. Concretely, the peer role provides:

- **A trace store.** Where Inspect writes its logs and where the seeded-replay bundle points. Retention, indexing, and access control are the peer's craft.
- **A judge-serving layer.** The StrongREJECT judge is deployed here; the SLOs (latency, throughput, cost) are the peer's craft.
- **An eval-gated CI hook.** The mechanism by which a candidate model rev cannot proceed past a build stage until its matrix run completes.
- **An online-eval sidecar.** The mechanism by which a fraction of production traffic is routed to the red-team judge for post-deployment monitoring; feeds the CMC's post-deployment cadence.

This module *specifies* the plumbing contract — the matrix expects a trace store with property X, a judge-service SLO of Y, a CI hook that fires on Z. It does not build the plumbing. Findings that require plumbing changes route to the peer role. The pattern is the mod-107 chapter-06 boundary pattern applied to the eval-plumbing layer.

## Version pinning and image digests

Every framework the CMC's section-3 references must be version-pinned. Concretely:

- **Inspect.** Python package version + git SHA + Docker image digest of the runner image.
- **PyRIT.** Python package version + git SHA. Orchestrator class names have changed across releases; the CMC pins the class + the release.
- **garak.** Python package version + git SHA + the *probe list* (garak versions add / remove probes, so the CMC pins which probes were run).
- **Promptfoo.** npm package version + git SHA of the `promptfooconfig.yaml` + git SHA of the *plugin catalogue* the suite consumed.
- **StrongREJECT judge.** Weights hash, prompt-template hash, decoding config.
- **Target model.** Model ID + weights hash (if the operator holds the weights) or model-provider version tag + provider-reported build ID (if API-served).

The pinning goes in the CMC's section-6 reproducibility bundle. Without it, a reviewer replaying the matrix a quarter later runs a *different* suite and will not reproduce the ASR — an entirely predictable failure that undermines the CMC's usefulness as evidence.

## Interfaces

- **Chapter 01 (CMC).** Section 3 (orchestrator inventory) is populated from this chapter; section 6 (reproducibility contract) leans on the version-pinning discussion above.
- **Chapter 03 (LLM-vs-LLM).** The PAIR / TAP / Crescendo attacker loops are hosted in PyRIT; this chapter names the composition, chapter 03 walks the mechanics.
- **Chapter 04 (fine-tuned attacker).** The fine-tuned attacker plugs into PyRIT as a chat target the orchestrator drives; the training pipeline is the peer's craft.
- **Chapter 05 (judge).** The StrongREJECT judge deploys as an Inspect scorer and as a PyRIT scorer; the judge is the *shared* grading layer across all four legs.
- **Chapter 06 (coverage matrix).** The four legs' outputs are reconciled into the coverage matrix here.
- **`ai-eval-engineer` (peer, level 30).** The eval-plumbing beneath the four frameworks — trace store, judge-serving, CI hook, online-eval sidecar — is the peer's craft. The CMC names the handoffs.
- **mod-107 (containment).** When the target-under-test is a tool-using agent, Inspect's sandbox integration is the mechanism; the mod-107 wrapper is *the thing being tested*.
- **mod-108 (guardrails).** The guardrail-effectiveness leg of the coverage matrix runs against the target-with-guardrails-on and target-with-guardrails-off; the delta is the guardrail's contribution.

## Common misreadings to avoid

- **"Union PyRIT + garak + Promptfoo and call it a program."** Union without decomposition produces triple-count coverage and no reproducibility. Each tool owns one concern; the CMC names which.
- **"PyRIT can be the runner."** PyRIT is an excellent attacker library and a poor runner. Its memory and reporting do not carry the reproducibility Inspect's log-store does. Use Inspect as the runner.
- **"garak covers everything."** garak covers *breadth of known-broken behaviour*. It does not adapt; a fresh in-the-wild jailbreak that garak has not yet added a probe for is invisible to garak. PyRIT + a fine-tuned attacker covers depth of adaptation.
- **"Promptfoo is a full red-team platform."** Promptfoo's red-team mode is a *CI integrator*. It is not the whole matrix; it is the scaffold-regression slice.
- **"We'll write our own runner."** Unless there is a specific, load-bearing gap Inspect cannot fill, do not. The wrong end of the trade is inheriting all of Inspect's reproducibility and reviewability guarantees so you can substitute your own runner that does not yet have them.
- **"The frameworks' default scorers are fine."** No. Chapter 05 replaces them with a StrongREJECT-shape judge because the default scorers under-count and over-count in different, incompatible ways. Cross-cell verdicts must be comparable; only a *shared* scorer makes them comparable.

## Summary

- The four flagship tools each own a distinct concern: Inspect is the runner and log store, PyRIT is the attacker-orchestrator library, garak is the scanner-shape breadth layer, Promptfoo is the CI-integrated scaffold-regression layer.
- The CMC's section-3 orchestrator inventory names which tool populates which slice of the matrix; the composition rule is *one concern per tool*, not union.
- Inspect is the top-level runner because its reproducibility guarantees are the strongest of the four. PyRIT plugs in as solvers; the StrongREJECT judge (chapter 05) plugs in as the shared scorer.
- Every framework and every artefact is version-pinned in the CMC's section-6 reproducibility bundle.
- The eval-framework plumbing beneath the four tools — trace store, judge-serving layer, CI hook, online-eval sidecar — is the `ai-eval-engineer` peer role's craft (level 30). This module specifies the plumbing contract; it does not build the plumbing.
- The wrong thing to build is a fifth orchestration harness. The right thing is to author a solver / orchestrator that fills the gap inside one of the four, or to route the gap to a peer role.
