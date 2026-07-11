# mod-105 — Agent-Specific Attack Surface Engineering

**Track:** Agentic Safety & Red-Team Engineer (`agentic-safety-engineer`, level 40, AI Governance family)
**Estimated effort:** 16 hours (≈3 hours lecture reading + 13 hours exercises)
**Prerequisites:** [mod-101 (Frontier Safety Frameworks)](../mod-101-frontier-safety-frameworks-and-position/README.md), [mod-102 (Threat Modelling for Autonomous and Tool-Using Agents)](../mod-102-agent-threat-modelling/README.md), [mod-103 (Prompt-Injection Engineering)](../mod-103-prompt-injection-engineering/README.md), and [mod-104 (Jailbreak Engineering)](../mod-104-jailbreak-engineering/README.md). The level-25 `ai-risk-engineer` prerequisite for the general LLM-security craft is assumed.

## Why this module exists

mod-103 taught you to engineer prompt-injection *primitives* against one input surface at a time; mod-104 taught you to engineer *jailbreak* payloads that bypass a model's safety policy. Neither of those disciplines, on its own, captures what actually goes wrong in an agent. An agent is a *system* — a planning loop, a tool bus, a memory store, a set of sub-agents, and a set of protocols connecting them. The interesting failures are *chains* across those parts: a webpage delivers an injection that shapes a sub-goal that authorises a tool call that reads a secret that gets exfiltrated to a calendar invite. No single primitive from mod-103 or mod-104 names that chain.

This module builds **agent-specific attack surface engineering** as its own discipline. The engineering artefact you author is an **Agent Attack-Surface Suite (AASS)** for one concrete tool-using agent — a coverage matrix over four agent-only attack families (tool-abuse chains, memory / vector-store poisoning, long-horizon planning subversion, multi-agent adversarial coordination), a benchmark-coverage report against **AgentDojo** (Debenedetti et al., 2024) and **InjecAgent** (Zhan et al., 2024), a defensive-posture axis (tool-response sanitisation, capability gates, human-in-the-loop), and the finding-feed handoffs to `senior-agentic-ai-engineer` (peer, agentic AI family — owns the agent-architecture patterns being attacked) and `ai-infra-security` (peer / next-up, level 35 — owns the tool-runtime hardening this suite hands off).

## Learning objectives

By the end of the module you can:

1. Engineer **tool-abuse chains** — unintended tool invocation via injection, argument smuggling through tool schemas, chained exfiltration through search + email + calendar tools, credential-theft chains through code-interpreter, and blast-radius amplification via file-system tools — with per-chain reproducibility bundles and a taxonomy that maps to OWASP Agentic Tool Misuse and MITRE ATLAS plugin-execution techniques.
2. Engineer **memory and vector-store poisoning** — injection through user-history writes, cross-session persistence, RAG-index poisoning through indexed-content control (PoisonedRAG family), and adversarial embedding-space nearest-neighbour attacks (AgentPoison family) — with per-attack persistence, cross-tenant reach, and retriever-blast-radius measurements.
3. Engineer **long-horizon planning subversion** — goal replacement across steps, sub-goal injection, plan-truncation attacks, and sandbox-escape through plan-branching — with per-attack step budgets, goal-drift traces, and containment-bypass evidence.
4. Engineer **multi-agent adversarial coordination** — cross-agent prompt injection, adversarial-message relay, and principal-confusion attacks in agent-to-agent protocols (**A2A**, **MCP** tool servers) — with per-attack propagation traces and multi-agent-graph blast-radius measurements.
5. Ship coverage against **AgentDojo** and **InjecAgent** — reporting attack-success rate under a set of defensive postures (raw / tool-response sanitisation / capability gates / human-in-the-loop) with an **attack-success-rate × defensive-posture** coverage matrix.
6. Cite the **boundary to `senior-agentic-ai-engineer`** (peer, agentic AI family, level 40) on the agent-architecture patterns being attacked, and the boundary to **`ai-infra-security`** (peer / next-up, level 35) on the tool-runtime hardening this suite hands off. Cite the boundaries to the other modules in this track: mod-103 (single-primitive injection), mod-104 (jailbreak), mod-107 (excessive-agency containment engineering), mod-108 (guardrails and monitors), mod-111 (scaled red-team).

## Chapters

| # | Chapter | Purpose |
|---|---|---|
| 01 | [Agent Attack Surface as an Engineering Discipline](01-agent-attack-surface-as-an-engineering-discipline.md) | Define the *chain* as the unit of study; distinguish this module from single-primitive injection (mod-103) and jailbreak (mod-104); pin the four attack families this module owns; introduce the Agent Attack-Surface Suite (AASS) artefact skeleton and the harmful-payload discipline this module inherits. |
| 02 | [Tool-Abuse Chains](02-tool-abuse-chains.md) | Unintended tool invocation via injection; argument smuggling through JSON-schema tolerances; chained exfiltration through search + email + calendar tools; credential-theft chains through code-interpreter; blast-radius amplification via file-system tools. Per-chain reproducibility bundle. |
| 03 | [Memory and Vector-Store Poisoning](03-memory-and-vector-store-poisoning.md) | Injection through user-history writes; cross-session persistence; RAG-index poisoning through indexed-content control (PoisonedRAG family); adversarial embedding-space nearest-neighbour attacks (AgentPoison family). Cross-tenant reach and retriever-blast-radius measurements. |
| 04 | [Long-Horizon Planning Subversion](04-long-horizon-planning-subversion.md) | Goal replacement across steps; sub-goal injection; plan-truncation attacks; sandbox-escape through plan-branching. Per-attack step budgets and goal-drift traces. |
| 05 | [Multi-Agent Adversarial Coordination](05-multi-agent-adversarial-coordination.md) | Cross-agent prompt injection; adversarial-message relay; principal-confusion attacks in A2A and MCP; worm-shaped propagation across an agent graph. |
| 06 | [AgentDojo and InjecAgent — Shipping Coverage Under a Defensive-Posture Matrix](06-agentdojo-and-injecagent-coverage.md) | AgentDojo (Debenedetti et al., 2024) and InjecAgent (Zhan et al., 2024) as the two public benchmarks this module ships coverage against; defensive postures (tool-response sanitisation, capability gates, human-in-the-loop); attack-success-rate × defensive-posture coverage matrix. |
| 07 | [Boundaries — `senior-agentic-ai-engineer` and `ai-infra-security`](07-boundaries-to-senior-agentic-and-ai-infra-security.md) | The peer-role handoffs: `senior-agentic-ai-engineer` owns the agent-architecture patterns this module attacks; `ai-infra-security` owns the tool-runtime hardening this suite's findings hand off to. Also codifies the boundary to mod-107 (containment engineering) and mod-108 (guardrails / monitors). |

## Exercises

| # | Exercise | Hours |
|---|---|---|
| 01 | [Tool-abuse chain authoring](exercises/exercise-01-tool-abuse-chain-authoring.md) | 3 |
| 02 | [Memory and vector-store poisoning](exercises/exercise-02-memory-and-vector-store-poisoning.md) | 3 |
| 03 | [Long-horizon planning subversion](exercises/exercise-03-long-horizon-planning-subversion.md) | 2 |
| 04 | [Multi-agent adversarial coordination](exercises/exercise-04-multi-agent-adversarial-coordination.md) | 2 |
| 05 | [AgentDojo and InjecAgent coverage run](exercises/exercise-05-agentdojo-and-injecagent-coverage-run.md) | 3 |

Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Do not commit answer keys here. Working chains and poisoned payloads — as opposed to defanged shapes — never live in either repo. See the harmful-payload discipline below and chapter 01.

## Structure

```
mod-105-agent-attack-surface-engineering/
├── 01-…07-… .md      lecture chapters
├── README.md          this index
├── exercises/         hands-on prompts (solutions live in the paired -solutions repo)
├── labs/              long-form hands-on labs (planned)
├── quizzes/           knowledge checks (planned)
└── resources.md       curated primary references
```

## Reading the primary sources

The chapters summarise, categorise, and pin — they do **not** substitute for reading the primary papers and protocol specs. Every chapter cites its primaries; `resources.md` collects them. In particular, before completing exercise 05 you should have read Debenedetti et al. (AgentDojo, 2024) and Zhan et al. (InjecAgent, 2024) end-to-end, and before completing exercise 02 you should have read at least Zou et al. (PoisonedRAG, 2024) and Chen et al. (AgentPoison, 2024). Chapter 01 is the shortest path in.

Benchmark scores, propagation rates, and public leaderboards move with every model and framework release. Every chapter's number carries a `<!-- needs-research: ... -->` marker where the figure would need re-verification before being cited in an artefact.

## Harmful-payload discipline

Working tool-abuse chains, poisoned RAG documents, adversarial memory writes, and multi-agent worm strings against production frontier agents are **not** committed to this repo. Illustrative snippets in the chapters are truncated, defanged, or point at published benchmark cases by reference. The evaluation set lives in an access-controlled artefact store per the pattern mod-103 chapter 06 codifies and mod-111 industrialises, with a manifest committed here. If you catch yourself pasting a working chain, poisoned document, or exfil payload into a chapter, exercise, or PR, stop.

The reasoning is the same as in mod-103 and mod-104: public agent-attack strings age fast, but publishing them accelerates the arms race in the wrong direction, and pinning them to a public repo turns your teaching material into an ongoing disclosure. Store the payloads where mod-112's disclosure workflow can reach them; publish only the *shape* and the *rate*.

## What this module does not cover

- **Single-primitive prompt injection** — direct + indirect primitives, the six-primitive taxonomy, obfuscation vectors, and the PIEH — these are **mod-103**'s scope. This module *uses* mod-103 primitives as the entry points to agent-specific chains; it does not re-derive them.
- **Jailbreak engineering at frontier depth** — GCG, PAIR, TAP, Crescendo, many-shot — these are **mod-104**'s scope. A jailbreak that unlocks a policy-forbidden capability inside an agent then rides a mod-105 chain; this module owns the chain; mod-104 owns the jailbreak.
- **Excessive-agency containment engineering** — capability caps, credential scoping, egress policies, sandbox hardening as *engineering* work — these are **mod-107**'s scope. This module *measures* containment as a defensive posture; mod-107 *engineers* it.
- **Classifier-guard training, constitutional-classifier training, monitor design** — these are **mod-108**'s scope. This module measures monitors as one row in the coverage matrix; mod-108 trains them.
- **Dangerous-capability evaluations** — CBRN, cyber, autonomous-replication, R&D-uplift — these are **mod-106**'s scope. Agent chains that elicit dangerous capability are findings this module produces; the capability-level evaluations themselves live in mod-106.
- **Automated / scaled red-team, LLM-vs-LLM at production scale** — these are **mod-111**'s scope. This module builds engineer-hands-on-keyboard-depth chain construction; mod-111 industrialises it.
- **Safety cases and disclosure workflow** — these are **mod-109** and **mod-112**. The AASS's outputs are inputs to those artefacts; the artefacts themselves are not authored here.
- **Tool-runtime hardening** — sandbox construction, MCP-server hardening, credential brokers, egress proxies — these are the **`ai-infra-security`** peer role's scope. Chapter 07 codifies the boundary.

## What this module produces

An **Agent Attack-Surface Suite (AASS)** for one concrete tool-using agent with:

- A **coverage matrix** over the four attack families (tool-abuse chains, memory / vector-store poisoning, long-horizon planning subversion, multi-agent adversarial coordination) × a **defensive-posture axis** (raw / tool-response sanitisation / capability gates / human-in-the-loop) × the AgentDojo + InjecAgent benchmark cells, with per-cell ASR and a run manifest.
- A **tool-abuse chain library** — reproducible chain scripts (attacker-shape, entry-primitive, target-tools, expected side-effect, judge) referencing payloads in the harmful-payload store.
- A **memory / vector-store poisoning bench** — the poisoned-corpus construction, the embedding-space attack construction (AgentPoison-shaped), the cross-session persistence probe, the cross-tenant reach probe.
- A **planning-subversion probe suite** — goal-drift trace instruments, sub-goal injection payloads, plan-truncation forcing prompts, and plan-branching sandbox-escape probes.
- A **multi-agent coordination bench** — worm-shaped propagation probe, principal-confusion probe on A2A / MCP, and message-relay tampering probe.
- A **defensive-posture toggle harness** — the same suite runnable under each defensive posture, so the finding is *"attack X succeeds under posture Y with rate Z"*, not just *"attack X succeeded once."*
- A **finding feed** to `senior-agentic-ai-engineer` (which architecture patterns fail), `ai-infra-security` (which tool-runtime controls need hardening), mod-107 (which containment layers need engineering), mod-108 (which monitors need training), and mod-111 (which attack families need scaling).
