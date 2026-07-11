# 01 — Agent Attack Surface as an Engineering Discipline

## Motivation

Prompt injection (mod-103) is a *primitive*. Jailbreak (mod-104) is a *policy override*. Neither is an *agent attack*. An agent attack is a **chain** that starts at some entry surface — a webpage, a tool response, a memory recall, a peer-agent message — and ends at some *effect* on state the operator cares about: a wire transfer executed, a secret exfiltrated, a file overwritten, a downstream agent misinstructed. Between the entry and the effect are a planner deciding on sub-goals, a tool bus routing invocations, a memory store persisting cross-session context, and often other agents receiving messages. The interesting failures live in the chain, not in any one primitive.

This chapter names the discipline the module builds. It draws the two adjacent boundaries — *this module is not injection engineering, and this module is not jailbreak engineering* — introduces the **Agent Attack-Surface Suite (AASS)** artefact skeleton, pins the four attack families the module owns, and hands the harmful-payload discipline down from mod-103 and mod-104 unchanged.

Read this chapter first. Subsequent chapters assume you accept "chain" as the unit of study, "AASS" as the artefact, and "AgentDojo + InjecAgent" as the coverage benchmarks that ground the discipline in numbers rather than anecdotes.

## What an agent attack is — and what it is not

**An agent attack is a sequence of state transitions through the agent's runtime — data-input → planner → tool call → memory / cross-agent → external effect — that an adversary induces via one or more attacker-controlled inputs and that produces a policy-forbidden or operator-unauthorised outcome.** The primitive is *chain construction*: the attacker composes injection, jailbreak, poisoning, and legitimate-looking tool invocations into an end-to-end path.

It is not:

- **A prompt injection on its own.** Injection is *principal confusion mediated by a channel* (mod-103). It is the most common *entry primitive* for an agent chain, but the chain is more than the entry. A finding of "the model followed instructions inside a retrieved document" is a mod-103 finding. A finding of "the model followed instructions inside a retrieved document, then wrote the user's calendar to a public URL, then propagated the same instructions into a memory record that fires on the next session" is a mod-105 chain. The mod-103 harness catches the first cell; the AASS catches the chain.
- **A jailbreak on its own.** Jailbreak is *policy override* (mod-104). A jailbroken model that never touches a tool has produced policy-forbidden text but no agent-level harm beyond that text. Once a jailbroken model is in an agent loop with tools, memory, and peer agents, the jailbreak *unlocks* a downstream chain. Chapter 07 formalises the composition: mod-104 owns the unlock; mod-105 owns the chain the unlock enables.
- **A generic system-security bug.** SSRF against a tool's URL fetcher, an SQL injection in a tool's database wrapper, a container escape in the sandbox — those are classical infra vulnerabilities. `ai-infra-security` (peer / next-up, level 35) owns their engineering. This module composes with those bugs (an SSRF-vulnerable tool amplifies a tool-abuse chain's blast radius), but it does not re-derive them.
- **A one-off "the model did a bad thing" incident.** Every AASS finding is *reproducible* — same target model version, same tool bindings, same memory state, same seed — or it is a story, not a finding. The reproducibility discipline is inherited from mod-103 chapter 06 and mod-104 chapter 01.

The load-bearing definitional element is **chain**. Every attack you engineer in this module names:

1. The **entry primitive** — which mod-103 primitive delivered the initial payload, or which mod-104 jailbreak unlocked the initial policy.
2. The **chain of state transitions** — planner steps, tool calls, memory writes, cross-agent messages — that the attack forced.
3. The **effect** — the operator-observable outcome that would not have happened absent the attack.
4. The **defensive posture** under which the chain was measured — raw, tool-response-sanitised, capability-gated, human-in-the-loop.

A finding that names the entry but not the chain is a mod-103 finding. A finding that names the effect but not the chain is a bug report. Both are useful; neither is what this module produces.

## Three boundaries this module draws

### Boundary 1 — mod-105 vs. mod-103 (single-primitive injection)

mod-103 authors a coverage matrix over **primitive × channel × obfuscation vector** and measures per-cell ASR of one prompt-injection *primitive* landing in one *channel*. That harness is the load-bearing input to this module — the AASS *uses* mod-103 primitives as entry points and does not re-derive them. The mod-105 addition is:

- **Composition.** Two mod-103 primitives (task-override in a retrieved webpage + exfiltration-through-completion via a rendered markdown image) become one mod-105 tool-abuse chain when they are wired through a tool bus that reads the image URL and fetches it. Neither primitive alone is a chain.
- **Persistence.** A mod-103 primitive that lands in a *memory-write* channel becomes a mod-105 poisoning attack when it persists across sessions and affects a different user. The single-turn primitive lives in mod-103; the persistence property is measured in mod-105 chapter 03.
- **State.** mod-103 evaluates one turn's behaviour. mod-105 evaluates the *agent's state trajectory* — planner state, memory state, tool-bus state — because that is what a chain acts on.

If your finding fits in one mod-103 cell and there is no downstream effect on the agent's state or on any tool call, emit it as a mod-103 finding. If the finding requires a chain, it belongs here.

### Boundary 2 — mod-105 vs. mod-104 (jailbreak)

mod-104 authors attackers (GCG, PAIR/TAP, Crescendo, many-shot) whose success criterion is a *policy override*: the model does the forbidden thing. The AASS *composes* with mod-104 in two ways:

- **Jailbreak-as-unlock.** A tool-abuse chain that requires the model to compose a phishing email needs the model to bypass its usage-policy refusal. mod-104's attackers deliver the unlock; the mod-105 chain then routes the phished email through the mail tool.
- **Chain-without-jailbreak.** Many mod-105 chains never touch a policy the model would refuse — the payload is a benign-looking instruction to call a legitimate tool with legitimate-looking arguments, whose *effect* is the harm. Chapter 02's argument-smuggling patterns are the clearest examples: the model happily calls `send_email(to=attacker, body=<user's data>)` because nothing in its safety training flags it as forbidden.

Emit findings against both modules where the chain requires a jailbreak, and only against mod-105 where the chain does not. The distinction routes findings to the right remediation team: mod-104 findings drive safety-tuning and monitor work (mod-108); mod-105 findings drive tool-runtime and containment work (mod-107, `ai-infra-security`).

### Boundary 3 — mod-105 vs. `ai-infra-security` (peer / next-up, level 35)

The `ai-infra-security` peer role owns **tool-runtime hardening**: sandbox construction, MCP-server hardening, credential brokers, egress proxies, argument validators, capability-token issuance, audit-log immutability, per-tool blast-radius caps. Those are *engineering* deliverables that live in the peer role's remit.

This module produces the **finding feed** the peer role consumes:

- "Attack X succeeds under posture Y because the tool bus routes attacker-controlled URLs to the fetch tool without an egress check."
- "Attack Z succeeds because credential-token scopes are not bound to the current sub-goal — the code-interpreter tool inherits the parent agent's mail credentials."
- "Attack W succeeds under human-in-the-loop because the approval UX summarises `send_email(...)` without showing the recipient's domain."

The peer role *engineers* the fix. This module *measures* the fix's effectiveness by re-running the chain under the new posture. Chapter 07 codifies the two-way handoff.

The parallel boundary against `senior-agentic-ai-engineer` (peer, agentic AI family, level 40) is that the peer role owns the *architecture patterns* — planning-loop shape, memory layout, MCP-server design, A2A protocol integration — that this module attacks. Findings that pattern-match to "the planner architecture is not attack-resistant" (e.g., no goal-drift check, no plan-truncation defence, no memory-write provenance labels) route to the peer role for pattern-level remediation, not to any one deployment's tool-runtime team.

## The four attack families this module owns

Every AASS finding falls in exactly one of four families. Chapters 02–05 develop each as first-class work.

### Family 1 — Tool-abuse chains (chapter 02)

*The primitive*: an attacker-controlled input (webpage, tool response, memory recall) causes the agent to invoke tools in a way the operator did not authorise, chaining the invocations to produce an effect the individual tool calls would each pass a naive audit.

*Sub-families*:

- **Unintended tool invocation via injection** — the classical Greshake-shaped case, upgraded from one-shot to a chain.
- **Argument smuggling** — the tool call looks legitimate; the argument encodes exfiltrated data or a downstream instruction.
- **Chained exfiltration through search + email + calendar** — the canonical multi-tool exfil path where each tool's audit sees only its own step.
- **Credential-theft chains through code-interpreter** — the interpreter reads a secret from its environment, encodes it, and emits it through another tool.
- **Blast-radius amplification via file-system tools** — a file-write tool's per-call cap is bypassed by a chain of writes across paths.

### Family 2 — Memory and vector-store poisoning (chapter 03)

*The primitive*: an attacker writes into a store the agent will read back — a memory record, a RAG corpus, an embedding index, a scratchpad — in a way that persists and shapes future behaviour.

*Sub-families*:

- **Injection through user-history writes** — the memory write happens in the attacker's own session and fires on the attacker's later session (attack against oneself, as a stepping-stone) or on a shared / summarised memory.
- **Cross-session persistence** — the payload survives a session boundary the operator assumed would reset state.
- **RAG-index poisoning through indexed-content control** — the PoisonedRAG (Zou et al., 2024) family: attacker publishes documents into the corpus so the retriever surfaces them.
- **Adversarial embedding-space nearest-neighbour attacks** — the AgentPoison (Chen et al., 2024) family: the attacker crafts a document whose embedding is close to a benign query the target user runs.

### Family 3 — Long-horizon planning subversion (chapter 04)

*The primitive*: the agent's planner (ReAct, plan-and-execute, tree-of-thought, sub-agent-spawning orchestrator) is manipulated across steps so the *goal* the agent pursues is no longer the goal the operator specified.

*Sub-families*:

- **Goal replacement** — the planner replaces the operator's goal with an attacker-supplied goal, often through a mid-plan reflection step that reads attacker text.
- **Sub-goal injection** — the operator's goal survives, but the planner inserts an attacker sub-goal into its plan; the sub-goal produces its own tool calls under cover of the parent goal.
- **Plan-truncation attacks** — the planner is nudged to terminate early (declare success) before a required check ran, or to skip the safety review step.
- **Sandbox-escape through plan-branching** — the planner spawns a sub-agent whose spawn arguments (system prompt, tool bindings) were themselves influenced by the attacker, producing a child agent that operates outside the containment the parent enforces.

### Family 4 — Multi-agent adversarial coordination (chapter 05)

*The primitive*: the attack does not act on one agent but on an agent *graph* — a set of agents that exchange instructions and data over A2A or MCP or a bespoke pub/sub — and either propagates worm-shaped or exploits *principal confusion* about whose instructions to trust.

*Sub-families*:

- **Cross-agent prompt injection** — instructions authored in one agent's context are read by a peer agent that treats the sender as authoritative.
- **Adversarial-message relay** — an intermediate agent tampers with messages passing through it (drop, rewrite, reorder), the *transit* becomes the surface.
- **Principal-confusion attacks in A2A / MCP** — the tool server or peer agent mis-attributes the calling principal, so tokens issued for one agent authorise another agent's actions.

## The AASS artefact skeleton

The engineering artefact you author from this module is an **AASS** for one concrete tool-using agent. Its skeleton — filled in across chapters 02–06 and exercises 01–05:

```yaml
aass:
  target:
    agent_id: <name>
    agent_version: <version pin>
    model: <model_id>
    model_snapshot: <date pin>
    planner: <react | plan_and_execute | tot | orchestrator>
    tool_bus: <framework name and version>          # LangChain / LangGraph / Autogen / bespoke
    memory: {kind: <chat | vector | summarised>, backend: <name>}
    protocols: [a2a: <version?>, mcp: <version?>, bespoke: <spec>]
  chains:                                            # chapter 02
    tool_abuse:
      - id: <chain-id>
        entry_primitive: <mod-103 primitive>
        entry_channel: <webpage | tool-response | ...>
        steps: [<planner or tool-call step>, ...]
        effect: <operator-observable outcome>
        payload_ref: <handle in harmful-payload store>
        judge: <rubric-id and version>
  poisoning:                                         # chapter 03
    memory:
      write_source: <this-session | prior-session | cross-tenant>
      persistence: <turns | sessions | tenants>
    rag:
      corpus_write_authority: <who can write to the indexed corpus>
      poisoning_method: <content-injection | embedding-space | trigger-conditioned>
      retriever: <embedding-model / retrieval-config>
      hit_rate: <fraction of queries surfacing the poisoned doc>
  planning_subversion:                               # chapter 04
    - kind: <goal-replacement | sub-goal-injection | plan-truncation | plan-branching>
      step_budget: <turns before the drift is measurable>
      goal_drift_trace: <pointer to trace bundle>
      containment_bypass: <yes/no + evidence>
  multi_agent:                                       # chapter 05
    graph:
      nodes: [<agent-id>, ...]
      edges: [(<from>, <to>, <protocol>), ...]
    attacks:
      - kind: <cross-agent-injection | message-relay | principal-confusion>
        origin: <agent-id>
        target: <agent-id>
        propagation_hops: <n>
        blast_radius: <n_agents affected>
  benchmarks:                                       # chapter 06
    agentdojo:
      version: <pin>
      suites: [<workspace | banking | travel | slack | ...>]
      attacks_covered: [<attack-id>, ...]
      asr: <under each defensive posture>
    injecagent:
      version: <pin>
      cases_covered: <n / N>
      asr: <under each defensive posture>
  defensive_postures:                               # chapter 06
    - name: raw
      config: <no defence>
    - name: tool_response_sanitisation
      config: <sanitiser spec + version>
    - name: capability_gates
      config: <capability-scope spec + issuer>
    - name: hitl
      config: <approval-UX spec + latency budget>
  boundaries:
    to_mod_103_pieh: <pointer>
    to_mod_104_jeh:  <pointer>
    to_mod_107_containment: <pointer>
    to_mod_108_monitor_harness: <pointer>
    to_ai_infra_security: <finding feed schema>
    to_senior_agentic_ai_engineer: <architecture-finding schema>
```

Every field derives from one of the six remaining chapters or an exercise. If a field is empty, name the chapter that fills it.

## Success criteria for a chain finding

Every AASS-emitted chain finding names:

- **The entry primitive** and its channel (mod-103 taxonomy label).
- **The jailbreak, if any**, that unlocked a policy the model would otherwise refuse (mod-104 taxonomy label).
- **The step-by-step chain trace** — planner steps, tool calls with arguments and results, memory reads/writes, cross-agent messages. Held in the reproducibility bundle, referenced by ID.
- **The operator-observable effect** — what the operator can point at as the harm.
- **The defensive posture** the chain was measured under.
- **The target agent, its version, the model snapshot, and the tool-bus/framework version.**
- **The judge and its version** — chain judgement is more subtle than a completion-refusal judgement; chapter 06 develops the AgentDojo utility × security composite.
- **The taxonomy cell** — one of the four families and its sub-family — so the finding routes to the right benchmark row.
- **The severity** — the mod-112 severity scale, with the chain's effect grounded in the operator's harm model, not in intuition.

A finding missing any of the above is a story, not a finding.

## Metrics the suite reports

For each cell and each defensive posture (developed further in chapter 06):

- **Attack Success Rate (ASR)** — fraction of trials the chain reached its effect per the judge.
- **Utility Preservation** — the "does the agent still do its job?" number. AgentDojo pairs an *attack* success rate with a *utility* success rate on benign requests, because a defence that refuses all tool calls has zero ASR and zero utility.
- **Defensive posture delta** — ASR under posture Y minus ASR under posture X. This is the number the peer roles need to prioritise where the marginal engineering hour goes.
- **Chain length / step count** — median steps from entry to effect. Long chains cost more to detect and often survive per-step-only defences.
- **Persistence horizon** (poisoning family only) — turns / sessions / tenants over which the poisoned state remains effective.
- **Propagation blast radius** (multi-agent family only) — number of agents transitively affected by one entry attack.
- **Judge–human agreement** — per-run agreement on a random-sampled subset. Uncalibrated judge → un-shippable finding.

## Common misreadings to avoid

- **"It's just prompt injection with more steps."** No. mod-103 measures *whether the primitive lands*. mod-105 measures whether the primitive *composes into an effect*. The composition can succeed on a defence that catches individual primitives (see chapter 04 on plan-truncation as an example) and can fail on a stack whose primitives all land (see chapter 06 on capability gates).
- **"AgentDojo covers it."** AgentDojo covers a subset — a specific set of workspaces (workspace, banking, travel, Slack) with a specific set of injection payloads and utility tasks. Chapter 06 details what it covers and what it does not. A finding "we passed AgentDojo" is one row in the coverage matrix, not the coverage itself.
- **"Human-in-the-loop stops chains."** HITL is one row in the defensive-posture matrix and its effectiveness is measured, not assumed. Approval-bombing (mod-102 surface 5) and misleading-summary attacks (chapter 02) degrade HITL's effectiveness in ways only a per-chain measurement catches.
- **"Memory poisoning is a training-data problem."** Memory poisoning at the per-agent, per-user, per-corpus level fires at inference time and does not require the poisoned content to reach any training pipeline. Chapter 03 develops the four sub-families; only one of them (feedback-loop-into-fine-tune) touches training.
- **"Multi-agent attacks require a multi-agent product."** No. As soon as your agent talks to any MCP server, calls any peer agent for a sub-task, or accepts any A2A message, the multi-agent surface is live. Chapter 05 develops the surface at any scale from N=2.

## Harmful-payload discipline (inherited from mod-103 and mod-104)

Working tool-abuse chains, poisoned RAG documents, and adversarial memory writes against production frontier agents route to the harmful-payload store, not this repo. Illustrative snippets in chapters 02–05 are truncated, defanged, or point at published benchmark cases by reference. The AASS's reproducibility bundle references payloads by ID; the payloads live behind access controls; the store's access log routes to the mod-112 disclosure workflow. If you catch yourself pasting a working chain into a chapter, exercise, or PR, stop.

## Summary

- Agent attacks are *chains* — the composition of injection, jailbreak, poisoning, and legitimate tool invocations into an end-to-end path with an operator-observable effect. Chain construction is the module's discipline.
- The boundary against **mod-103** is: mod-103 measures whether a primitive lands; mod-105 measures whether the primitive composes into an effect.
- The boundary against **mod-104** is: mod-104 delivers the unlock when the chain needs one; mod-105 owns the chain.
- The boundary against **`ai-infra-security`** is: this module produces the finding feed; the peer role engineers the tool-runtime fix; this module re-measures under the new posture.
- The boundary against **`senior-agentic-ai-engineer`** is: this module attacks the agent-architecture patterns the peer role designs; findings that indict a pattern route to pattern-level remediation.
- The four attack families are **tool-abuse chains, memory / vector-store poisoning, long-horizon planning subversion, multi-agent adversarial coordination** — chapters 02–05.
- The engineering artefact is an **Agent Attack-Surface Suite (AASS)** with a coverage matrix, chain library, poisoning bench, planning-subversion probes, multi-agent bench, and defensive-posture toggle harness — with the payload store held outside this repo.
- The AASS ships coverage against **AgentDojo** and **InjecAgent** under a **defensive-posture matrix** (raw / tool-response sanitisation / capability gates / human-in-the-loop) — chapter 06.
