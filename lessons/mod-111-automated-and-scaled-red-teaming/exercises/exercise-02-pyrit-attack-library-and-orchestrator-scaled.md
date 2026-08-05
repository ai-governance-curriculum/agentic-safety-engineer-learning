# exercise-02 — PyRIT attack library and orchestrator, scaled

**Estimated effort:** 2 hours
**Prerequisite chapters:** 02, 03, 06 (helpful: 01, 04, 05).

## Objective

Move PyRIT past hand-run adoption into the shape the CMC needs. You will (a) assemble a **per-behaviour-category PyRIT attack library** from a public seed corpus and at least one **converter chain**, (b) run it against a target across at least two **decoding configs** via two canonical PyRIT orchestrators — one single-turn (`PromptSendingOrchestrator`) and one multi-turn — (c) wrap the PyRIT orchestrator as an **Inspect solver** so the runner is Inspect and not PyRIT's own loop, and (d) report **seed-corpus diversity** using chapter 03's metric. The exercise contributes to your CMC's **section 3** (orchestrator inventory — PyRIT's slice) and **section 5** (attack-corpus contract).

## Problem statement

Pick the same target you have been using in this module (a specific model rev with a pinned decoding profile). Pick **3–5 behaviour categories** from a public seed corpus (HarmBench, AIR-Bench, AILuminate, or JailbreakBench — cite the version); the categories must span the taxonomy rather than cluster on one behaviour class (chapter 03's diversity argument is why). From each category, sample a fixed number of seeds into a **per-category PyRIT attack library** whose entries manifest by ID + sha256 + external-store URI — the payloads never enter this repo (chapter 06).

Apply **at least one converter chain** with two or more stages (for example, `Base64Converter` composed with a persona-wrapper `SearchReplaceConverter`, or `TranslationConverter` composed with `UnicodeConfusableConverter`). The chain is one of the CMC section-5 attack-corpus rows; chapter 02 names converters as the mechanism the obfuscation leg of mod-103 rides on at scale.

Configure two canonical PyRIT orchestrators against the target:

1. **`PromptSendingOrchestrator`** — the single-turn shape; the direct-send baseline.
2. **One multi-turn orchestrator** — `RedTeamingOrchestrator`, `CrescendoOrchestrator`, `PAIROrchestrator`, or `TreeOfAttackOrchestrator`. Pick one; chapter 03 walks the differences. <!-- needs-research: pin the current published PyRIT class names; Microsoft has revised the orchestrator taxonomy across releases. -->

Run the library against the target across **at least two decoding configs** (e.g., `T=0.0` and `T=0.7,p=0.95`) so each cell of your matrix carries a decoding axis, not just an attack axis. Produce a **per-cell tally** — ASR with 95% CI, sample count, cost — where a cell is `(behaviour_category × converter_chain × orchestrator × decoding_config)`. Score with a chapter-05 StrongREJECT-shape judge; **do not** use PyRIT's default `SelfAskLikertScorer` for the reported ASR (chapter 02 says why).

Wrap the PyRIT orchestrator invocation as an **Inspect solver**. Inspect is the runner; PyRIT is the attacker library. The Inspect task's log is what the CMC's section-6 reproducibility contract points into — PyRIT's `MemoryInterface` is not (chapter 02).

Report the **diversity of the seed corpus itself** using chapter 03's metric — embedding-cluster count at a pinned threshold, or n-gram diversity, or judge-provided facet coverage. This is the mistake to avoid: reporting diversity only over *successful* attacks lets a repetitive seed corpus look diverse because the same three seeds succeeded under three converter variants. The library's coverage claim rests on the *seed* diversity.

Payload discipline (chapter 06) is not optional: working attacker prompts, converter output text, and judge rationale text never enter this repo.

## Requirements

Produce four artefacts.

### Artefact A — `cmc-<program>-pyrit-library.yaml`

The per-behaviour-category attack library manifest. One entry per seed. Each entry carries:

- `id` — stable `PYR-<category>-<n>` identifier resolved in the payload store.
- `behaviour_category` — the mapped CMC axis cell (HarmBench category, AIR-Bench cell, AILuminate hazard — cite the benchmark and its pinned version).
- `source_corpus` — the public corpus and its version tag; the seed's identifier within that corpus.
- `sha256` — hash of the raw seed text.
- `storage_uri` — external payload-store URI; no inline text.
- `severity` — mod-112 severity annotation for the underlying harm.
- `converter_chain_ids` — list of converter-chain identifiers this seed will be run through.
- `access` — ACL / group / role.

The library file records manifests only. Seed text lives in the payload store.

### Artefact B — `cmc-<program>-pyrit-converters.yaml`

The converter-chain registry. One entry per chain. Each entry carries:

- `chain_id` — stable `CHN-<name>-<n>` identifier.
- `stages` — ordered list of `{class, params}` entries naming PyRIT converter classes and their arguments. <!-- needs-research: confirm the exact PyRIT converter class names and parameter shapes for the pinned release. -->
- `pyrit_version`, `pyrit_git_sha` — version-pinning per chapter 02's discipline.
- `rationale` — one to two sentences on why this chain matters (which mod-103 obfuscation primitive it rides on, which behaviour category it is expected to elicit against).
- `output_payload_id_pattern` — the ID scheme under which the chain's output payloads land in the store (never inlined).
- `expected_matrix_slice` — the CMC section-3 slice this chain feeds.

The library × converter cross-product defines the population of *executed* payloads; the manifest of those executed payloads (with IDs + sha256 + storage URIs) is also carried in the payload store, not this file.

### Artefact C — `cmc-<program>-pyrit-report.yaml`

Per-cell tally, one entry per `(behaviour_category × converter_chain × orchestrator × decoding_config)` cell:

- `cell_id` — deterministic identifier composed from the four axes.
- `orchestrator_class` — `PromptSendingOrchestrator` | the multi-turn class name; the version-pinned class as it appears in the pinned PyRIT release.
- `decoding_config` — temperature, top-p, top-k, seed if set, provider sampling identifier.
- `sample_count` — number of attack attempts in the cell.
- `asr` — attack success rate under the chapter-05 StrongREJECT-shape judge, with 95% CI.
- `cost` — dollars, input tokens, output tokens, wall-clock seconds per cell.
- `judge_id` — judge model + rubric prompt hash + decoding config (chapter 05 shape).
- `inspect_task_id`, `inspect_log_id` — the Inspect run identifiers this cell corresponds to. **These are the load-bearing IDs**; PyRIT `MemoryInterface` IDs are supplementary, not primary (chapter 02).
- `seed_corpus_diversity` — chapter 03's metric applied to the **seed corpus** entering the cell, not the successful subset. Report `unique_clusters@<threshold>` (naming the embedding model and threshold), or `distinct_ngrams@n`, or judge-facet-coverage — pick one and pin it.
- `successful_attack_diversity` — the same metric applied to the successful subset, for comparison. A cell whose successful-attack diversity is much lower than its seed diversity is a *repetition finding* (chapter 03); flag it.
- `matrix_cell` — the CMC section-3 slice this cell populates.

A report that aggregates ASR into one scalar across cells is rejected; the per-cell tally is the finding.

### Artefact D — `cmc-<program>-pyrit-runbook.md`

A short (800–1200 word) runbook covering:

- **Library-assembly rationale.** Why these behaviour categories, why this public seed corpus (and its version), what taxonomy spread looks like across the categories, and how you resisted concentrating on the category you know the target is soft on. Chapter 03's diversity argument is the frame.
- **Converter-chain rationale.** Why this chain and not another. Which mod-103 obfuscation primitive each stage rides on, and which behaviour categories the chain is expected to elicit against. Note explicitly the composition — a base64-of-persona is a different attack from persona-of-base64.
- **Orchestrator choice.** Why the specific multi-turn orchestrator you picked. Chapter 03's PAIR / TAP / Crescendo distinctions set the expectation for what each catches; the runbook says what you expect this one to catch and what you know it will miss (the mistake to avoid: treating PAIR / TAP / Crescendo as interchangeable).
- **Inspect-as-runner wire-up.** How the PyRIT orchestrator is wrapped as an Inspect solver. The load-bearing insight (chapter 02) is that PyRIT is the *attacker library* and Inspect is the *runner*; the runbook documents the adapter's shape (dataset → PyRIT orchestrator invocation → Inspect scorer), what state the adapter reset between invocations, and where the Inspect log ID is captured. The mistake to avoid is calling `Orchestrator.run_attack()` as the top-level loop and layering coverage reporting on PyRIT memories.
- **Decoding-config choice.** Which two (or more) decoding configs and why. Chapter 06's reproducibility contract needs the config hash; the runbook records what was pinnable and what could not be pinned for API-served targets.
- **Seed-corpus diversity discussion.** The chapter-03 metric you chose, the threshold you pinned, and what the number means for your library's coverage claim. Explicit: a library that repeats itself is a library that under-reports risk regardless of ASR.
- **Repetition-finding audit.** For each cell where `successful_attack_diversity` is much lower than `seed_corpus_diversity`, name the mechanism. Was the target repeatedly falling to one specific converter variant? One seed cluster? Chapter 03 argues this is a *repetition finding*, not a coverage finding; the runbook flags it as such.
- **Judge choice.** The chapter-05 StrongREJECT-shape judge (not `SelfAskLikertScorer`); how you plugged it into both the PyRIT scorer slot (for the multi-turn orchestrator's internal loop) and the Inspect scorer slot (for the reported ASR). Note the judge version pin.
- **Version pinning.** PyRIT version + git SHA, orchestrator class name as it appears in that release, converter class names and parameter shapes, Inspect version + git SHA, judge weights hash + prompt hash. Chapter 02 says why: a reviewer replaying the matrix a quarter later runs a *different* suite without these.
- **Threats to validity.** Seed underrun per cell, decoding-config drift on API-served targets, chat-template mismatch, judge staleness, converter-output drift when the converter LLM (for LLM-driven converters) revs, PyRIT class-name renames across releases.
- **CMC handoff.** Which section-3 rows and section-5 rows this exercise populates, which cells remain unpopulated (and which future exercise or peer role picks them up), and which cells route to mod-108 (guardrails), mod-107 (containment), or mod-112 (disclosure).

## Starter guidance

- **PyRIT is the attacker library; Inspect is the runner.** The chapter-02 load-bearing rule. If you find yourself calling `Orchestrator.run_attack()` as the top-level loop, stop; wrap the PyRIT invocation in an Inspect solver and let Inspect own the log.
- **Sample the seed corpus deterministically.** Seeded random sampling from the public corpus, seed recorded in the manifest. A reviewer must be able to reproduce the same library.
- **Diversify the categories.** Concentrating on one HarmBench category will produce a report that says nothing about coverage. Spread across at least three behaviour categories per chapter 03's diversity argument.
- **Version-pin the converter chain, not just PyRIT.** Converter class names and parameter defaults have shifted across PyRIT releases. Pin the class + the release. <!-- needs-research: enumerate the converter classes and any parameter renames for the pinned release. -->
- **Chain converters deliberately, not opportunistically.** A base64 wrapper around a persona wrapper is a different attack surface from a persona wrapper around a base64 wrapper. Name the composition explicitly (chapter 02).
- **Replace `SelfAskLikertScorer` in the reported ASR path.** PyRIT's default scorer under-counts and over-counts in different ways from a StrongREJECT-shape judge. Cross-cell verdicts must be comparable; only a shared chapter-05 judge makes them comparable.
- **Measure diversity on the seed corpus, not just successful attacks.** Chapter 03's argument is that a repetitive library over-reports its coverage. Compute the seed-corpus diversity per cell; compare to successful-attack diversity; flag the delta.
- **Two decoding configs at minimum.** A one-decoding-config matrix is a one-column matrix. Chapter 06's reproducibility contract needs the config hash and the second column exposes decoding-sensitive cells.
- **Store converter outputs by ID, not inline.** Every converter output is a harmful-payload artefact. It lands in the payload store with an ID + sha256 + storage URI; the manifest references it. Nothing inlined in this repo.
- **Capture the Inspect log ID as the primary reference.** PyRIT `MemoryInterface` IDs are supplementary; the Inspect log is what the CMC section 6 replay bundle points into.
- **Budget the cells before running.** Per-cell dollar cap, query cap, wall-clock cap. A cell whose budget is exhausted terminates with a documented `budget-exhausted` leaf, not silently.
- **Cross-tag findings to the guardrail row.** A cell where a converter chain succeeded is a guardrail-configuration input for mod-108; note the tag on the cell.
- **Do not commit any harmful payload.** Not the seed text, not the converter output, not the judge rationale. Every one of those is a payload-store artefact referenced by manifest.
- **Author small first, scale second.** Get one behaviour category × one converter chain × one orchestrator × one decoding config passing end-to-end before fanning out. A broken adapter that runs 200 cells wastes 200 cells of budget.
- **Mark unverified factual claims.** `<!-- needs-research: ... -->` on every PyRIT class name, seed-corpus size, ASR number, or judge calibration figure you cannot verify from primary source.

## Acceptance criteria

- ✅ `cmc-<program>-pyrit-library.yaml` manifests seeds across at least three behaviour categories from a version-pinned public corpus, with `sha256`, `storage_uri`, `severity`, and `access` populated per entry. **No seed text in the file.**
- ✅ `cmc-<program>-pyrit-converters.yaml` defines at least one converter chain with two or more stages, PyRIT version + git SHA pinned, class names and parameters recorded, expected matrix slice named.
- ✅ `cmc-<program>-pyrit-report.yaml` reports **per-cell** tallies over `(behaviour_category × converter_chain × orchestrator × decoding_config)` with `asr` + 95% CI, `sample_count`, `cost`, `judge_id`, `inspect_task_id`, `inspect_log_id`. A report aggregating ASR into one scalar is rejected.
- ✅ At least **two orchestrators** are configured — `PromptSendingOrchestrator` and one multi-turn class — and at least **two decoding configs** are run against each cell.
- ✅ The PyRIT orchestrator is invoked from an **Inspect solver**; `inspect_log_id` is the primary reproducibility reference in the report, not the PyRIT `MemoryInterface` ID. Chapter 02's *PyRIT is the attacker library, Inspect is the runner* rule is respected.
- ✅ The reported ASR is scored under a chapter-05 StrongREJECT-shape judge. PyRIT's default `SelfAskLikertScorer` is not used in the reported-ASR path.
- ✅ **Seed-corpus diversity** (chapter 03's metric, pinned) is reported per cell alongside the successful-attack diversity; every cell whose successful-attack diversity is materially lower than its seed diversity is flagged as a *repetition finding*.
- ✅ **Payload discipline (chapter 06): no working attacker prompts, no converter output text, no judge rationale text, no target completions committed to this repo.** All are referenced by ID + sha256 + external storage URI.
- ✅ PyRIT (package version + git SHA), the orchestrator class name for that release, each converter class and its parameters, the Inspect version + git SHA, and the judge (weights hash + rubric prompt hash + decoding config) are version-pinned in the runbook per chapter 02.
- ✅ `cmc-<program>-pyrit-runbook.md` covers library-assembly rationale, converter-chain rationale, orchestrator choice, Inspect-as-runner wire-up, decoding-config choice, seed-corpus diversity, repetition-finding audit, judge choice, version pinning, threats to validity, and the CMC section-3 / section-5 handoffs.
- ✅ Every unverified factual claim (PyRIT class names or renames, published ASR numbers, seed-corpus sizes, judge calibration numbers) is marked `<!-- needs-research: ... -->`.
- ✅ Handoff notes at the end of the runbook name the mod-108 guardrail row(s) each finding feeds, the mod-112 severity for each finding, and the CMC section-3 / section-5 entries this exercise populates.

## Stretch goals

- **Add a second multi-turn orchestrator.** Run the library against a second class (e.g., `PAIROrchestrator` on top of `CrescendoOrchestrator`). Chapter 03's argument is that PAIR / TAP / Crescendo catch different failure classes; the delta on your target's cells is the evidence.
- **Compose a converter chain with a chapter-04 attacker.** Take one cell's successful attacks and feed them as a few-shot prior into a chapter-04 fine-tuned attacker as its seed corpus for the same behaviour category. Report the ASR lift over the converter-only baseline.
- **Add a garak cross-check for the same behaviour categories.** Run the corresponding garak probes against the same target and decoding configs; report the garak-caught behaviours that your PyRIT library missed and vice versa. Chapter 02's composition rule expects garak to catch known-broken behaviours PyRIT-driven adaptive attacks do not target; a delta here validates the composition.
- **Ship a per-cell repetition-finding monitor.** A standalone judge that, given a cell's successful-attack set, reports the diversity metric and flags repetition. Feeds a mod-108 online-eval sidecar and closes chapter 03's diversity loop.
- **Extend the decoding-config axis to a small grid.** Sweep temperature ∈ {0.0, 0.7, 1.0} × top-p ∈ {0.9, 0.95, 1.0} on one cell; the decoding-sensitivity surface per cell is a CMC section-3 finding when the cell's ASR moves materially across the grid.

## Deliverable location

Personal notes or private repo. **Do not** commit the deliverable — or the seed text, converter outputs, target completions, or judge rationales — into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference library skeleton. Working payloads live in your org's payload store per chapter 06; see the harmful-payload discipline before starting.
