# mod-103 — Prompt-Injection Engineering

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 14 hours (≈2 hours lecture reading + 12 hours exercises)
**Prerequisites:** [mod-101 (Frontier Safety Frameworks)](../mod-101-frontier-safety-frameworks-and-position/README.md) and [mod-102 (Threat Modelling for Autonomous and Tool-Using Agents)](../mod-102-agent-threat-modelling/README.md). The level-25 `ai-risk-engineer` prerequisite for the general LLM-security craft is assumed.

## Why this module exists

Prompt injection is the single most-cited failure mode in agentic-system security writing, the OWASP LLM Top 10's LLM01, and the MITRE ATLAS `Prompt Injection` (AML.T0051) technique. It is also the most *misused* term — casually conflated with jailbreak, with hallucination, with any refusal failure. This module builds prompt-injection as an *engineering* discipline: a coverage matrix over primitives × channels × obfuscation vectors, an evaluation harness that measures which cells succeed and at what rate, a defence catalogue that composes the layers real deployments ship, and an adaptive-attack methodology that separates brittle defences from robust ones.

The engineering artefact you author from this module is a **Prompt-Injection Evaluation Harness (PIEH)** for one concrete tool-using agent — coverage matrix, replay bundle (payloads held outside this repo), reproducibility bundle, defence catalogue with static + adaptive ASR per cell, plus the finding feed and regression fixtures the `ai-eval-engineer` peer's CI consumes.

## Learning objectives

By the end of the module you can:

1. Engineer the six **direct prompt-injection primitives** — instruction-override, refusal-bypass, role-hijack, task-override, exfiltration-through-completion, output-format hijack — with every payload mapped to **OWASP LLM01** and **MITRE ATLAS AML.T0051**.
2. Engineer **indirect prompt-injection attacks** across the four indirect channel families — retrieved context (webpages, PDFs, RAG snippets), tool responses (search results, code-interpreter output, external API responses), long-term memory / vector stores, and cross-plugin channels (sub-agents, peer agents, MCP servers, plugin marketplaces) — grounded in Greshake et al. (2023) and InjecAgent (Zhan et al., 2024).
3. Engineer **obfuscation vectors** — Unicode confusables / homoglyphs, invisible / Tag characters, base64 / hex / cipher encodings, low-resource-language translation, ASCII-art framing, tool-response HTML injection, steganographic composition, template-delimiter spoofing — and produce a per-vector defence-quality evaluation.
4. Ship a working **Prompt-Injection Evaluation Harness (PIEH)** with a prioritised coverage matrix, an access-controlled replay bundle (payloads stored outside this repo per the mod-111 harmful-payload discipline), a reproducibility bundle, and CI integration.
5. Author a **defence catalogue** covering spotlighting, StruQ, sandwich prompting, self-refuting classifiers, and tool-response sanitisation + boundary controls, and stress-test each layer against adaptive attackers to produce **adaptive-ASR** deltas.
6. Cite the **boundary to the `ai-eval-engineer` peer role (level 30, AI Engineering family)** — the app-side traces, LLM-judge scaffolds, eval-gated CI, and online-eval infrastructure the PIEH consumes; and the injection-finding feed and regression fixtures the PIEH produces for the peer's CI.

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Prompt-Injection as an Engineering Discipline](01-prompt-injection-as-an-engineering-discipline.md) | Define prompt injection (principal confusion mediated by a channel), draw the boundary against jailbreak (mod-104), pin OWASP LLM01 and ATLAS AML.T0051 as the mandatory labels, and introduce the six primitives + the PIEH artefact skeleton. |
| 02 | [Direct Injection Primitives](02-direct-injection-primitives.md) | Instruction-override, refusal-bypass, role-hijack, task-override, exfiltration-through-completion, output-format hijack — with defanged payload shapes, success criteria, and the primitive × channel preview matrix. |
| 03 | [Indirect Injection Through Retrieved Context and Tool Responses](03-indirect-injection-through-retrieval-and-tools.md) | The Greshake-shaped indirect channel: webpages, PDFs, RAG snippets, search snippets, code-interpreter output, external API responses. InjecAgent as the current benchmark. |
| 04 | [Indirect Injection Through Long-Term Memory and Cross-Plugin Channels](04-indirect-injection-through-memory-and-cross-plugin.md) | The persistent / transitive channels: per-user memory, shared corpora, feedback-into-training, sub-agents, peer agents (A2A), MCP servers, plugin marketplaces. Adds latent-activation and cross-principal-reach properties. |
| 05 | [Obfuscation Vectors and Defence-Quality Evaluation](05-obfuscation-vectors.md) | Unicode confusables, invisible / Tag characters, base64 / cipher encodings, low-resource-language, ASCII art, tool-response HTML injection, steganographic composition, template-delimiter spoofing. Per-vector defence-hole measurement. |
| 06 | [Building the Injection Evaluation Harness](06-injection-eval-harness.md) | The three PIEH components — coverage matrix, replay bundle (harmful-payload discipline: payloads outside this repo, manifest inside), reproducibility bundle. Metrics, judges, CI integration. |
| 07 | [The Prompt-Injection Defence Catalogue](07-defence-catalogue.md) | Five layers — spotlighting (Hines et al.), StruQ (Chen et al.), sandwich prompting, self-refuting classifiers, tool-response sanitisation + boundary controls — with per-layer coverage and misses. |
| 08 | [Adaptive Attacks and Defence Stress-Testing](08-adaptive-attacks-and-defence-stress.md) | Static vs. adaptive ASR, per-layer adaptive playbooks, scripted attacker loops, elicitation gap, composed-under-adaptive-attack. |
| 09 | [The Boundary to `ai-eval-engineer`: Traces, Judges, and Plumbing](09-boundary-to-ai-eval-engineer.md) | The peer-role handoff: shared trace schema (with safety-side trust-label + raw/sanitised deltas), shared judge scaffolds, eval-gated CI, online-eval; and the injection-findings feed + regression fixtures the peer's CI consumes. |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [Direct injection attack catalogue](exercises/exercise-01-direct-injection-attack-catalogue.md) | 2 |
| 02 | [Indirect injection through retrieval and tool responses](exercises/exercise-02-indirect-injection-through-retrieval-and-tool-responses.md) | 3 |
| 03 | [Encoding / obfuscation vectors drill](exercises/exercise-03-encoding-obfuscation-vectors-drill.md) | 2 |
| 04 | [Injection evaluation harness authoring](exercises/exercise-04-injection-eval-harness-authoring.md) | 3 |
| 05 | [Injection defence catalogue and adaptive-attack stress](exercises/exercise-05-injection-defence-catalogue-and-adaptive-attack-stress.md) | 2 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys here. Working payloads (as opposed to defanged illustrations) do not live in either repo — see the harmful-payload discipline in chapter 06.

## Structure

```
mod-103-prompt-injection-engineering/
├── 01-…09-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise, categorise, and pin — they do **not** substitute for reading the primary papers and standards. Every chapter cites its primaries; `resources.md` collects them. In particular, before completing exercise 02 you should have read Greshake et al. (2023) end-to-end, and before completing exercise 05 you should have read at least Hines et al. (Spotlighting), Chen et al. (StruQ), and Zhan et al. (InjecAgent). Chapter 01 is the shortest path in.

Numerical IDs (OWASP LLM Top 10 numbering, MITRE ATLAS technique IDs, OWASP Agentic threat numbers) move between versions; every chapter's mapping carries a `<!-- needs-research: ... -->` marker where the ID has churned. Verify against the primary source before citing in an artefact.

## Harmful-payload discipline

Working prompt-injection payloads — the actual strings that elicit misbehaviour against production frontier models — are **not** committed to this repo. Illustrative snippets in the chapters are truncated, defanged, or demonstrably safe. The evaluation set lives in an access-controlled artefact store per the pattern mod-111 codifies for automated / scaled red-teaming, with a manifest committed here. Chapter 06 develops the storage layout and review process. If you catch yourself pasting a working payload into a chapter, exercise, or PR, stop.

## What this module does not cover

- **Jailbreak engineering primitives at frontier depth** (GCG, PAIR, TAP, Crescendo, many-shot, in-the-wild taxonomies) — these are **mod-104**'s scope. This module owns the *injection-delivered* variant of refusal-bypass payloads; mod-104 owns the general jailbreak construction methodology.
- **Agent tool-abuse chains, memory-poisoning at scale, long-horizon planning subversion, multi-agent adversarial coordination** — these live in **mod-105**. This module supplies the injection primitives those chains ride on; mod-105 owns the chain construction.
- **Sandboxing, egress policy, credential-scoping engineering** — these are **mod-107**'s scope. This module names the boundary-control defence layer and measures its coverage; mod-107 engineers it.
- **Classifier-guard training and constitutional-classifier methodology** — these are **mod-108**'s scope. This module measures classifier defences; mod-108 trains them.
- **LLM-vs-LLM attacker loops and StrongREJECT-style judge industrialisation** — these are **mod-111**'s scope. This module builds the minimum-viable attacker loop; mod-111 scales it.
