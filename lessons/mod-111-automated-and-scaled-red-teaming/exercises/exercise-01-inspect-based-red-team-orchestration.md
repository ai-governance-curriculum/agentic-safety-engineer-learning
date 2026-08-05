# exercise-01 — Inspect-based red-team orchestration

**Estimated effort:** 3 hours
**Prerequisite chapters:** 01, 02 (helpful: 05, 06).

## Objective

Stand up a minimal end-to-end **Inspect** orchestration spine for one concrete red-team program: three Inspect tasks run against a chosen target, one task each wrapping a **PyRIT** orchestrator, a **garak** probe, and a **Promptfoo** red-team plugin — all three grading against the **same** chapter-05-shape Inspect scorer, all three producing Inspect logs, and all three registered against a matrix-cell manifest. The exercise produces the first evidence into your Coverage Matrix Contract (CMC) **section 3 (orchestrator inventory)** and **section 6 (reproducibility contract)** — chapter 02's composition rule reified as running artefacts.

## Problem statement

Chapter 02's load-bearing claim is that Inspect is the runner and log store; PyRIT, garak, and Promptfoo are content sources with a single shared scorer. Most teams get this wrong on the first attempt by running each framework end-to-end and hoping the union constitutes a program; the CMC then has three incompatible run artefacts and no reviewable reproducibility surface. This exercise forces the correct decomposition on the smallest possible surface — three tasks, one target, one scorer — so that later exercises can extend the matrix without re-litigating the shape.

Pick **one program scope**: name a concrete target (an open-weight small model — 7B–13B class — served via a local endpoint is fine; API-served works if you own budget for it), name a small **behaviour slice** (two to four HarmBench / AIR-Bench categories, not the whole taxonomy — this is a spine exercise, not a coverage exercise), and name a single **decoding config**. Author three Inspect tasks — one per framework leg — pointed at that target, that slice, that decoding. Wire the three legs to the same Inspect scorer (a StrongREJECT-shape stub is acceptable; calibration is exercise 05). Record every log ID and every framework pin in a matrix-cell manifest.

The exercise emphasises what chapter 02 called the composition rule: Inspect as the runner, PyRIT / garak / Promptfoo as content sources, one shared scorer, and version-pinned everything. The mistake to avoid is writing a fifth runner, or letting each leg carry its native scorer and calling the union "coverage."

## Requirements

Produce four artefacts.

### Artefact A — `cmc-<program>-spine-tasks.py`

The Inspect task-definition file. Three tasks in one module. Each task:

- `name` — stable, matrix-addressable identifier of the shape `spine.<leg>.<harm-slice>.<target>.<decoding>` (e.g. `spine.pyrit_pair.jailbreak.qwen25_7b.tp0_7_p0_95`). Chapter 06 will read these names.
- `dataset` — a small seed dataset (10–30 items) drawn from the chosen behaviour slice. Seed *shapes* only in this file; working weaponised prompts live in the payload store per chapter 06's harmful-payload discipline and are dereferenced by ID at run time.
- `solver` — the wrapper around the leg's content source:
  - **PyRIT leg.** An Inspect solver that adapts a PyRIT orchestrator (`PromptSendingOrchestrator` for the direct-attack spine, or a canonical multi-turn orchestrator such as `RedTeamingOrchestrator` / `CrescendoOrchestrator` — chapter 03 covers the multi-turn class hierarchy). The solver must expose the orchestrator's per-turn output to the Inspect scorer, not swallow it. <!-- needs-research: confirm the current PyRIT orchestrator class names against the pinned release; Microsoft has revised the taxonomy across releases. -->
  - **garak leg.** An Inspect solver that invokes one garak probe (e.g. a `dan.*`, `promptinject.*`, or `latentinjection.*` probe) against the same target and surfaces the per-attempt completion. The garak native detector is **not** consumed; the shared Inspect scorer grades. <!-- needs-research: confirm the garak probe-invocation API surface for programmatic single-probe runs; the CLI-first ergonomics may require a thin subprocess adapter. -->
  - **Promptfoo leg.** An Inspect solver that invokes one Promptfoo red-team plugin family (jailbreak, prompt-injection, PII-exfil — pick one that maps to the behaviour slice) against the same target and yields the per-case completion. The Promptfoo native grader is **not** consumed. <!-- needs-research: confirm the current programmatic invocation path for a single Promptfoo red-team plugin outside a full `promptfooconfig.yaml` run; the CI-first surface may require a thin config-and-invoke adapter. -->
- `scorer` — the **same** StrongREJECT-shape Inspect scorer across all three tasks. A stub whose rubric is placeholder is acceptable at this altitude; the exercise-05 calibration replaces the stub with a calibrated judge. What is not acceptable is three different scorers.
- `config` — the shared decoding config, sampling count, and per-task budget (wall-clock, tokens, cost cap).

Committed file is task **shape** — imports, task definitions, solver signatures, scorer wiring. Working attacker prompts, working PAIR / Crescendo turn text, working probe payloads, and working plugin cases are **not** committed; they live in the payload store per chapter 06 and are referenced by ID.

### Artefact B — `cmc-<program>-spine-manifest.yaml`

The matrix-cell manifest. One YAML document with three cell entries and a program-level header.

Program header:

- `program` — the program identifier (matches `<program>` in the artefact prefixes).
- `scope_statement` — one paragraph, the CMC section-1 pointer.
- `axes` — the coverage-matrix axes this spine populates (chapter-01 CMC section 2): `attack_technique × behaviour_category × model_version × decoding_config`. This spine populates one point on each axis; note it explicitly.
- `orchestrator_inventory` — the CMC section-3 entry for this spine, naming the three legs and citing chapter 02's composition rule.
- `shared_scorer` — the scorer identifier + prompt-template hash + weights hash (stub is fine; still hash it).

Per cell:

- `cell_id` — matches the Inspect task `name`.
- `leg` — `pyrit | garak | promptfoo`.
- `content_source` — the specific class / probe / plugin invoked (e.g. `pyrit.orchestrators.PromptSendingOrchestrator`, `garak.probes.dan.DAN_11_0`, `promptfoo.redteam.plugins.<name>`). <!-- needs-research: pin the exact class / probe / plugin identifiers against the pinned framework versions. -->
- `target` — target model ID + weights hash (open-weight) or provider version tag + build ID (API-served).
- `decoding` — sampling config.
- `dataset_ref` — payload-store dataset ID + sha256 + storage URI. **No seed text in the manifest.**
- `sample_n` — attempts per cell.
- `inspect_log_id` — populated after the run; the CMC section-6 reproducibility bundle points here.
- `pins` — framework pins: Inspect version + git SHA + runner image digest; PyRIT version + git SHA; garak version + git SHA + probe-list SHA; Promptfoo version + git SHA + plugin-catalogue SHA. Chapter 02's version-pinning section is the authority.
- `scorer_output` — pointer to the Inspect log's scorer section; ASR, judge-verdict distribution, and judge-disagreement stub. **No judge rationale text quoting harmful content in the manifest.**
- `cmc_traceability` — which CMC sections this cell feeds (`3`, `6` at minimum; note others where applicable).

### Artefact C — `cmc-<program>-spine-runbook.md`

A ~800–1200 word runbook covering:

- **Scope choice.** Which program, which target, which behaviour slice, which decoding — and why each choice is defensible for a spine exercise (not the full matrix). Chapter 01's scope-statement discipline is the authority; the runbook is where the choice is justified.
- **Decomposition rationale.** The chapter-02 composition rule stated in your own words, then applied cell by cell: why the PyRIT leg is PyRIT and not Inspect-native, why the garak leg is garak and not PyRIT, why the Promptfoo leg is Promptfoo and not a bespoke CI hook. A runbook that cannot justify each choice is a runbook that permitted a union-shaped program.
- **Shared-scorer rationale.** Why one scorer across three legs — chapter 02's "cross-cell verdicts must be comparable; only a shared scorer makes them comparable" is the load-bearing claim. Note explicitly that the stub is a stub and that exercise 05 calibrates it.
- **Inspect-as-runner rationale.** Cite chapter 02's degradation modes: PyRIT as runner (reproducibility gap), garak as runner (adaptivity gap), Promptfoo as runner (matrix-scale gap). The runbook records why each was rejected as the top-level runner for the spine.
- **Version pinning.** Every framework, every image, every probe list, every plugin catalogue, every scorer template — enumerated and hashed. Chapter 02's pinning section is the checklist.
- **Sandbox posture.** If the target is a tool-using agent (unlikely for a spine exercise but call it out), which Inspect sandbox integration is invoked, and the mod-107 handoff for the containment wrapper.
- **Plumbing-boundary handoff.** The trace store, judge-serving layer, CI hook, and online-eval sidecar are the `ai-eval-engineer` peer role (level 30) — chapter 02's plumbing-boundary section. The runbook names which of these are stubbed locally for this exercise and which will route to the peer role for the production CMC.
- **Threats to validity.** Framework-version drift over three hours of running, chat-template mismatch across legs (each framework renders the target's prompt differently by default — this is a load-bearing footgun), scorer-stub false-positive / false-negative behaviour, seed dataset too small for stable ASR (this is expected at spine altitude), non-determinism from the target's sampling.
- **CMC handoff.** Which CMC sections this exercise's artefacts populate (section 3 primarily, section 6 via the Inspect logs). What later exercises will extend (exercise 02 extends section 3's PyRIT depth; exercise 05 replaces the scorer stub; exercise 06 formalises section 6; exercise 07 formalises the payload-store manifest referenced here).

### Artefact D — `cmc-<program>-spine-pins.yaml`

A dedicated version-pin file. Chapter 02's pinning discussion is the schema:

- `inspect` — package version, git SHA, runner image digest, Python interpreter version.
- `pyrit` — package version, git SHA, orchestrator class name(s) pinned to the release, memory backend.
- `garak` — package version, git SHA, probe-list SHA, detector-list SHA (even though the shared scorer is used, the detector version affects the probe's completion trajectory in some probes). <!-- needs-research: confirm which garak probes have detector-dependent generation paths in the pinned version. -->
- `promptfoo` — npm package version, git SHA, plugin-catalogue SHA, `promptfooconfig.yaml` SHA for the minimal config the leg invokes.
- `target` — model ID, weights hash or provider version tag + build ID, tokenizer SHA, chat-template SHA.
- `scorer_stub` — weights hash (stub can be a rule; hash the rule), prompt-template SHA, decoding config.
- `hardware` — the physical / cloud host descriptor sufficient to reproduce the run (chapter 06 will formalise; the spine records what it can).

This file is the CMC section-6 reproducibility bundle's version-pin subset; exercise 06 will fold it in.

## Starter guidance

- **Read chapter 02 with a highlighter on the composition rule.** The one-concern-per-tool rule is what the whole exercise enforces. A three-legged spine that lets the PyRIT leg use PyRIT's memory as the log store, or the garak leg use garak's report as the verdict, has silently defeated the exercise.
- **Do not write a fifth runner.** Chapter 02's misreading list names this explicitly. The temptation appears when the garak or Promptfoo programmatic-invocation surface feels awkward. Write a thin adapter, not a runner.
- **Pick the smallest target that will actually run.** An open-weight 7B–13B model served via `vllm` or a local Ollama endpoint is fine. The spine exercise is not measuring your target's ASR; it is measuring whether the spine composes.
- **The scorer stub is a stub.** A rule as simple as "response contains a refusal phrase → refuse; else → comply" is acceptable at this altitude, provided the *interface* — the Inspect scorer signature, the output schema, the log-store placement — is StrongREJECT-shaped. Exercise 05 replaces the internals with a calibrated judge; if the interface is wrong now, exercise 05 has to redo the wiring.
- **Adapter thickness discipline.** Each of the three solver adapters should be a small file — dozens of lines, not hundreds. If an adapter is growing a config-parser or a retry loop or a caching layer, that logic belongs in the framework being adapted, not in the adapter. Chapter 02's degradation-modes section is the diagnostic.
- **Chat-template rendering must match across legs.** The single largest silent failure at spine altitude is one leg rendering the target's chat template correctly and another leg dumping raw text into the prompt. Verify with a single-seed probe against a known-refusal prompt across all three legs before the full run.
- **The manifest is not optional.** Chapter 06's reproducibility contract is what the manifest becomes. A run whose log ID is not recorded is a run that did not happen from the CMC's point of view.
- **Payload discipline (chapter 06) is not optional.** Seed prompts, PyRIT-orchestrator working turns, garak probe payloads, Promptfoo plugin cases, judge rationale text — none of these are committed to your notes repo or to this course. The manifest references payloads by ID + sha256 + storage URI. Illustrative shapes only in committed files.
- **Pin versions before the first run, not after.** A version-pin file authored after the run is fiction; author it against the environment you built and re-verify after the run completes.
- **The runbook is the interface to the reviewer.** The four sections a reviewer reads first are scope, decomposition, shared-scorer, and pinning. The rest can be terse; those four cannot.
- **The `ai-risk-engineer` (level 25) prerequisite is where garak / PyRIT / Promptfoo at general depth was taught.** This module does not re-teach the frameworks; it composes them. If a learner cannot invoke a single garak probe programmatically, the correct route is back to level 25, not into this spine.
- **The `ai-eval-engineer` (level 30) peer role owns the plumbing.** The trace store, judge-serving layer, CI hook, and online-eval sidecar are not built here. The runbook names the handoff; the manifest records the plumbing contract's expected properties without building them.
- **Do not skip the axes header.** The CMC section-2 axes are what makes the spine legible as a coverage artefact rather than three unrelated runs. Even a spine that populates one point per axis is legible; a spine with no axes header is not.
- **A cell that cannot be described in chapter 02's worked-example shape is a cell whose manifest entry is incomplete.** Use the chapter's worked example (runner, solver, seeds, scorer, sandbox, sample size, log store, judge output) as the manifest-entry checklist.

## Acceptance criteria

- ✅ `cmc-<program>-spine-tasks.py` defines exactly three Inspect tasks — one PyRIT-solver, one garak-solver, one Promptfoo-solver — all pointing at the same target, the same shared scorer, and the same decoding config. Task names follow the `spine.<leg>.<harm-slice>.<target>.<decoding>` shape.
- ✅ The shared scorer is a single Inspect scorer used across all three tasks; three different scorers is a hard fail. The scorer is StrongREJECT-shape at the interface level (rubric internals can be a stub; exercise 05 calibrates).
- ✅ Each solver is a **thin adapter** that wraps its framework's content source and yields the completion to the shared scorer. Native framework scorers / detectors / graders are not consumed. No fifth runner exists.
- ✅ `cmc-<program>-spine-manifest.yaml` lists all three cells with `cell_id`, `leg`, `content_source`, `target`, `decoding`, `dataset_ref` (ID + sha256 + storage URI — **no seed text**), `sample_n`, `inspect_log_id`, `pins`, and `scorer_output`. A `cmc_traceability` field cites CMC section 3 and section 6 at minimum.
- ✅ `cmc-<program>-spine-runbook.md` (800–1200 words) covers scope choice, decomposition rationale per leg, shared-scorer rationale, Inspect-as-runner rationale citing chapter 02's degradation modes, version pinning, sandbox posture, plumbing-boundary handoff to `ai-eval-engineer`, threats to validity, and the CMC handoff to later exercises.
- ✅ `cmc-<program>-spine-pins.yaml` pins Inspect + PyRIT + garak + Promptfoo + target + scorer stub + hardware per chapter 02's pinning schema. Every entry has a version, a git SHA (or provider build ID), and a content hash where applicable.
- ✅ All three legs produced an Inspect log; every log ID is captured in the manifest and dereferences in the local Inspect log store. Chapter 02's "Inspect owns the runner and log store" rule is verified in artefact, not just in prose.
- ✅ **Payload discipline (chapter 06) satisfied.** No working attacker prompts, no PyRIT orchestrator turn text, no garak probe payloads, no Promptfoo plugin cases, and no judge rationale text quoting harmful content appear in any committed file. Manifest references are by ID + sha256 + storage URI only.
- ✅ Every unverified factual claim about a specific PyRIT class name, garak probe API, Promptfoo plugin taxonomy, or Inspect API surface is marked `<!-- needs-research: ... -->`. Chapter 02's own primary-sources block uses the same discipline; the exercise inherits it.
- ✅ Runbook handoff section names the `ai-eval-engineer` (level 30) peer role for the trace-store / judge-serving / CI-hook / online-eval-sidecar plumbing, cites the mod-107 sandbox handoff if the target is agentic, and names the mod-108 / mod-109 / mod-112 consumer routes (CMC section 7) the spine will feed once populated.

## Stretch goals

- **Wire the Inspect log store to a shared trace-store stub.** Chapter 02's plumbing-boundary section describes the trace-store contract the `ai-eval-engineer` peer role owns. Author a minimal stub that reads Inspect logs from a local directory and exposes the CMC section-6 replay-bundle contract. This is a stub, not the production trace store; the point is to make the plumbing contract concrete enough that exercise 06's replay bundle has a real interface to point at.
- **Add a fourth cell that fails the composition rule intentionally.** Author (in a separate branch of the manifest, clearly marked `anti_pattern: true`) a cell that runs PyRIT end-to-end with PyRIT's native scorer, and record in the runbook the specific reproducibility properties chapter 02 says it loses. This is teaching-by-inversion; the anti-pattern makes the rule visible.
- **Cross-target the same three legs against a second target.** Run the same three tasks against a second model (a differently sized open-weight model is fine) to verify the manifest's per-target hashing surfaces the change cleanly. This previews the `model_version` axis of the coverage matrix that exercise 06 formalises.
- **Author the online-eval-sidecar handoff spec.** Chapter 02's plumbing-boundary names the sidecar as `ai-eval-engineer` craft; author a one-page interface spec (not an implementation) describing what fraction of production traffic the sidecar routes to the shared scorer and how it writes back into the trace store. Route the spec to the peer role.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable — or the seed datasets, or the working attacker prompts, or the PyRIT / garak / Promptfoo working payloads, or the judge rationales, or the Inspect logs quoting harmful completions — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference spine. Working payloads live in your org's payload store per chapter 06; see the harmful-payload discipline before starting.
