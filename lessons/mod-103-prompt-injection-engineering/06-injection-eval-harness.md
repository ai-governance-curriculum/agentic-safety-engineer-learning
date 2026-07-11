# 06 — Building the Injection Evaluation Harness

## Motivation

Chapters 02–05 built the vocabulary — primitives, channels, obfuscation vectors. This chapter builds the *artefact*: the **Prompt-Injection Evaluation Harness (PIEH)** that lets you say, defensibly, "under condition X, direct-injection primitive Y delivered through channel Z under obfuscation vector W succeeds at rate R on the deployed target agent, and the current defence stack catches it at rate D."

Without the harness, every finding is anecdote. Every defence claim in chapter 07 is untestable. Every adaptive attack in chapter 08 has no baseline. This chapter is the load-bearing engineering chapter of the module.

## The three components of the harness

The PIEH has three components, each of which needs its own file layout and its own review process:

1. **Coverage matrix** — the *what*. Which (primitive × channel × obfuscation) cells does the harness exercise, at what depth, with what success criteria?
2. **Replay bundle** — the *how*. The evaluation set (payloads, target scaffolds, expected effects) stored under access control, with a manifest committed to the source repo.
3. **Reproducibility bundle** — the *conditions*. Model IDs, decoding config, chat template, tool manifest, seed ranges — everything that could vary between runs and make one team's number disagree with another's.

Every finding you emit from the harness carries pointers to all three: which coverage-matrix cell it landed in, which replay-bundle payload produced it, and which reproducibility-bundle conditions were in effect.

## Component 1 — The coverage matrix

### Purpose

The coverage matrix is the enumeration you commit to as a red-team engineer: "I claim my harness covers these cells." Every uncovered cell is either an *explicit exclusion* (with a reason) or a *known gap* (with a plan and a date).

### Structure

The rows and columns are drawn from chapters 02–05:

- **Rows** — the six primitives (instruction-override, refusal-bypass, role-hijack, task-override, exfil-through-completion, output-format hijack).
- **First column group** — the direct channel (user prompt).
- **Next column groups** — the four indirect channel families with their sub-channels:
  - Retrieval → webpage, PDF, RAG snippet
  - Tool response → search snippet, code-interpreter, external API
  - Long-term memory → per-user, shared corpus, feedback loop
  - Cross-plugin → sub-agent, peer agent, MCP server, plugin marketplace
- **Layered over each cell** — a chosen subset of obfuscation vectors from chapter 05 (typically 3–5 per cell, selected for defence-relevance to the target agent).

The full cross-product is 6 primitives × ~13 sub-channels × ~5 vectors ≈ 400 cells, which is more than any team ships. The coverage matrix is a *prioritised* subset informed by the ATMD (mod-102):

- **Tier A (always covered)** — every cell whose (surface × persona) prioritisation from the ATMD ranks top-10.
- **Tier B (covered per release)** — every cell for which the target agent has a plausible defence in the current stack (so we can measure regression).
- **Tier C (spot-checked)** — every cell not in A or B, sampled at a lower rate.
- **Explicit exclusions** — cells the target agent's deployment tier does not expose (a customer-support agent with no code interpreter excludes the code-interpreter row).

### Per-cell contract

Every covered cell carries:

- **Success criterion** — what "the payload worked" means for this cell. For task-override cells, this is "the agent invoked the attacker tool and did not mention it in the user summary." For exfil-through-completion, this is "the completion contained the target string encoded per the attacker's requested encoding."
- **Judge** — the function (a rubric-scored classifier, an LLM judge with its own eval, a heuristic parser) that scores the success criterion. Every judge has its own agreement number with human labels; a judge without a calibration figure is not shippable. Cross-reference the mod-111 automated-red-teaming judge methodology and the `ai-eval-engineer` peer's judge-plumbing conventions.
- **Sample size** — how many trials of this cell per run. Statistical power depends on your target effect size and false-positive tolerance (see the `model-evaluation-engineer` peer's calibration material for the underlying methodology).
- **Baseline** — the current pass rate on this cell against the target agent's current version, so future runs measure movement.
- **Owner** — the team who fixes the finding when the pass rate degrades.

### Coverage-matrix example (partial, illustrative)

```yaml
matrix:
  - cell: {primitive: instruction_override, channel: direct.user_prompt, obfuscation: none}
    tier: A
    success_criterion: attacker_task_executed
    judge: rubric_task_shift_v1
    n_trials: 100
    baseline: 0.02
    owner: model-provider
  - cell: {primitive: task_override, channel: indirect.retrieval.webpage, obfuscation: none}
    tier: A
    success_criterion: silent_side_effect_and_benign_summary
    judge: rubric_silent_side_effect_v1
    n_trials: 100
    baseline: 0.11
    owner: retrieval-team
  - cell: {primitive: exfil_through_completion, channel: indirect.tool_response.external_api, obfuscation: base64}
    tier: B
    success_criterion: target_string_in_url_with_encoding
    judge: heuristic_url_pattern_v1
    n_trials: 50
    baseline: 0.04
    owner: agent-runtime-team
  - cell: {primitive: role_hijack, channel: indirect.cross_plugin.mcp_server, obfuscation: template_delimiter_spoof}
    tier: A
    success_criterion: model_adopts_spoofed_identity
    judge: rubric_identity_shift_v1
    n_trials: 100
    baseline: 0.06
    owner: mcp-onboarding-team
```

## Component 2 — The replay bundle

### Purpose

The replay bundle is the actual **evaluation set**: the payload strings, target agent scaffolds (system prompts, retrieved documents, tool responses), and expected effects. It is what a colleague loads to reproduce your finding.

### Harmful-payload discipline

Weaponised payloads (base64 exfil templates, working refusal-bypasses against a specific model, role-hijack payloads that succeed against a specific chat template) are **not** stored in this source repo, this module's chapters, or any exercise deliverable committed here. They live in a **separate access-controlled artefact store**, following the same pattern that mod-111 codifies for automated / scaled red-teaming:

- **External storage** — private S3 bucket, dedicated internal repo, or signed tarball on an evaluation server. Access-logged. Scoped credentials for reviewers.
- **Manifest committed here** — a text file that lists payload IDs, checksums (SHA-256), authors, the coverage-matrix cell each payload belongs to, and the storage location. The manifest does *not* contain the payload text.
- **Signing** — the manifest is signed (Sigstore, gpg, whatever the org uses) so reviewers can verify integrity.
- **Retrieval workflow** — a scripted, audited fetch: the CI runner has a role that can pull the payloads to a scratch directory, run the harness, and destroy the directory. Developers pulling for local runs use a scoped short-lived credential.
- **Review process** — new payloads are added by opening a review PR *in the external store*, not this repo. The reviewer checklist covers scope (does this belong to a real coverage-matrix cell?), safety (is the payload as defanged as possible while remaining functional?), and attribution (who authored, when).

### Manifest example (committed here; safe to publish)

```yaml
manifest_version: 1
signed_by: <keyfingerprint>
storage_location: s3://acme-red-team/pieh-payloads/v3/
payloads:
  - id: PIEH-DIR-INST-001
    matrix_cell: {primitive: instruction_override, channel: direct.user_prompt, obfuscation: none}
    sha256: <hex>
    author: <handle>
    added: 2026-04-12
    notes: baseline naive instruction-override; expected fail rate high against modern safety tuning
  - id: PIEH-IND-WEB-TASK-011
    matrix_cell: {primitive: task_override, channel: indirect.retrieval.webpage, obfuscation: none}
    sha256: <hex>
    author: <handle>
    added: 2026-04-14
    notes: derived from Greshake et al. §4 pattern; adapted to target agent's browsing tool
```

### What lives inside the payload — schema, not string

Each payload in the store is a structured record:

```yaml
payload_id: PIEH-IND-WEB-TASK-011
matrix_cell: {...}
delivery:
  channel: retrieval.webpage
  scaffold:
    - the exact URL to serve
    - the exact HTML body
    - the exact meta tags
target_action:
  - which tool the agent should invoke
  - which arguments should carry attacker data
  - what should appear in the user-facing summary
judges:
  - id: rubric_silent_side_effect_v1
    version: 3
    threshold: 0.7
reproducibility:
  min_model_version: <model-id>
  max_model_version: <model-id>
  chat_template: <template-id>
  decoding: {temperature: 0.0, top_p: 1.0}
```

The store's records are structured so the harness can iterate them; the review process treats each record as a self-contained unit.

## Component 3 — The reproducibility bundle

### Purpose

Any injection finding is decoder-, template-, and version-sensitive. The reproducibility bundle is what makes a finding *reproducible* — a colleague running the same harness a week later against the same model / template / decoding config gets numbers within the harness's stated confidence interval.

### Contents

- **Model IDs** — the exact model IDs and version pins. For frontier lab APIs, include the `model` string and the model's snapshot / date pin. For self-hosted, include the checkpoint hash.
- **Chat template** — the exact chat-template renderer, its version, and any custom operator wrapping (system prompt, tool-response formatting).
- **Decoding config** — temperature, top-p, max-tokens, seed strategy. Injection findings are decoder-sensitive; report across at least two decoder configs (deterministic and the production sampling config).
- **Tool manifest** — the exact set of tools the agent has, their schemas, and their runtime versions.
- **Retrieval and memory state** — the exact vector store contents (index snapshot ID), the retrieval hyperparameters (k, similarity metric), any per-user memory state to preload.
- **Seed ranges** — for stochastic runs, the seeds the harness cycles through. Reporting one lucky seed is not evidence.
- **Environment metadata** — timezone, locale, and any other environment that might affect tool responses.

### Storage

The reproducibility bundle is metadata; it can live in the source repo alongside the manifest. It should not contain any weaponised payload. A common pattern is a `reproducibility.yaml` alongside each harness invocation, plus a `reproducibility.snapshot.tar` in the external store for the heavier artefacts (vector-store index snapshot, memory state).

## Judge design (cross-reference to mod-111 and to `ai-eval-engineer`)

Every cell needs a judge. Judge design is not this chapter's primary material — it is developed in mod-111 (Automated and Scaled Red-Teaming) and by the `ai-eval-engineer` peer role. The bindings this chapter mandates:

- **Every judge has a version.** Judge versions are pinned per finding.
- **Every judge has a human-agreement number.** A judge that has not been calibrated is not shippable.
- **Judges are ideally rubric-scored** — a rubric that a human can also apply to the same trial for spot-checks.
- **LLM-judge cost is a first-class metric** — the harness reports judge tokens per run so cost-effective sampling can be tuned.
- **Judge bias is monitored.** A judge whose false-positive rate has crept up across releases is a bug in itself.

The `ai-eval-engineer` peer role (level 30, AI Engineering family) owns the *general* application-side eval-plumbing craft — trace formats, judge scaffolds, eval-gated CI. This module *consumes* that plumbing. Chapter 09 develops the boundary contract; here the important thing is that the PIEH judge harness is not built from scratch when the peer role's harness exists.

## Metrics the harness emits

For each cell and each run:

- **Attack Success Rate (ASR)** — fraction of trials where the success criterion was met.
- **Defence Coverage** — fraction of trials the defence stack caught (before or after the model's own refusal).
- **Model Refusal Rate** — fraction of trials the model refused unaided.
- **Elicitation Gap** — ASR under best-effort adversarial elicitation vs. baseline elicitation. Cross-references mod-101 elicitation gap.
- **Judge–human agreement** — for a random-sampled subset.
- **False positives** — trials where the judge scored success but a human labeller disagreed. Reported for every judge every run.

Chapter 08 defines the *adaptive-attack* metrics (delta of ASR against a defence under attack iteration) as separate outputs.

## CI integration

The harness runs in CI on every release candidate. Its outputs feed:

- **A release gate** — a chosen subset of cells (typically Tier A + a rotating subset of Tier B) must remain at or below the baseline ASR to release. Tier and threshold are set by the mod-102 deployment-tier decision.
- **A trend dashboard** — ASR per cell over time. Regressions and improvements are both first-class signals.
- **A regression bundle** — findings that once passed and now fail become named test cases in the harness's regression suite. This bundle is what the `ai-eval-engineer` peer consumes to gate application releases.
- **The mod-112 disclosure feed** — findings above a severity threshold trigger the safety-program disclosure workflow.

## Common engineering mistakes when building the harness

- **Building the harness after the fix.** By then there is no baseline. Build the harness on the current version *before* touching the defence stack.
- **Reporting a single-seed number.** Injection findings vary across seeds. Report across the reproducibility bundle's seed range with a CI.
- **Skipping the baseline row.** Tier A cells must have a baseline. Without it, "we improved" is unverifiable.
- **Committing payloads.** The harmful-payload discipline is not optional. Check every PR for accidental payload strings.
- **Judge drift.** Rerun judge calibration on a schedule (monthly, or every model update). A judge that has drifted quietly biases every finding it scored.
- **Not distinguishing model refusal from defence catch.** The `Defence Coverage` metric must separate "the model refused unaided" from "an external classifier or sanitiser caught the input." Otherwise you cannot tell which layer is the load-bearing defence.
- **Coupling the harness to one model.** The harness must run across at least two of {your primary model, a comparison model, a safety-tuned variant} to reveal model-specific over-reliance.

## Handoffs to peer roles

- **`ai-eval-engineer` (peer, level 30, AI Engineering family)** — provides the app-side trace format, the judge scaffold, the eval-gated CI plumbing. This module's PIEH consumes those. Chapter 09 codifies the contract.
- **`model-evaluation-engineer` (peer, level 30, ML Engineering family)** — provides statistical calibration methodology (best-of-N CI, judge-vs-human calibration). Cited from the harness's metrics section.
- **mod-111 (Automated and Scaled Red-Teaming)** — extends the harness with LLM-vs-LLM attacker loops. This chapter builds the base harness; mod-111 scales it.
- **mod-112 (Safety Program and Disclosure)** — consumes findings above a severity threshold and routes them through the disclosure workflow.

## Summary

- The PIEH has three components: a **coverage matrix** (what is exercised), a **replay bundle** (payloads under access control, manifest in the repo), and a **reproducibility bundle** (model / template / decoder / tool state).
- The evaluation set is stored outside this source repo. The manifest and reproducibility metadata live here; the payloads do not. This is the harmful-payload discipline shared with mod-111.
- Every cell is tiered (A / B / C / excluded), every finding cites its cell, its judge, and its reproducibility bundle. Every judge has a version, a calibration figure, and a cost metric.
- The harness emits ASR, defence coverage, model refusal rate, elicitation gap, judge–human agreement, and false positives per run.
- CI integration produces a release gate, a trend dashboard, a regression bundle, and a disclosure feed. The `ai-eval-engineer` peer provides the app-side plumbing; chapter 09 codifies that boundary.
- Chapter 07 catalogues defences; chapter 08 stress-tests them adaptively against this harness.
