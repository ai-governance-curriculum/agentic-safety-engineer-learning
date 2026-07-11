# 01 — Why Agents Break the General Threat-Model Prerequisite

## Motivation

You already know how to threat-model a system. The `ai-risk-engineer` (level 25) prerequisite trained you on:

- **Classical threat modelling** — STRIDE (Microsoft), LINDDUN (KU Leuven, for privacy), and attack trees.
- **AI-specific harm modelling** — sweep the AI Incident Database, OECD.AI Incidents Monitor, and MIT AI Risk Repository; build a harm catalogue; join it to the NIST AI RMF risk register.
- **Adversarial-ML taxonomy** — NIST AI 100-2, OWASP ML Top 10, MITRE ATLAS at a general depth.
- **General LLM misuse** — the OWASP Top 10 for LLM Applications read as a *chat app* checklist.

That craft is necessary, and this module does **not** re-teach it. It is also insufficient for the systems you now own. The four assumptions below hold for a chat-only LLM behind a form; every one of them fails for a tool-using agent.

## Four prerequisite assumptions that break for agents

### Assumption 1: the system's outputs are text a human reads

For a chat app, a bad output is a bad answer. A user reads it and moves on. The harm is *communicative* (offence, misinformation, defamation) and the mitigation lives in the output classifier.

For an agent, the "output" is a **tool call** the runtime *executes*. `send_email`, `execute_shell`, `create_pr`, `transfer_funds`, `book_appointment`, `spawn_sub_agent`. The runtime does not consult a human before executing; it consults an argument validator and a policy engine, both of which the level-25 prerequisite does not build.

*What changes for the threat model:* Every tool the agent can invoke is a new **egress channel** with its own blast radius, blast direction, reversibility, latency-to-detection, and downstream consumer. The threat model must enumerate them (chapter 02, tool-invocation surface).

### Assumption 2: attacker-controlled data enters through the user prompt

For a chat app, the attacker types into the same box the operator sees. Prompt-injection defence is symmetric: filter the input, watch the output.

For an agent, attacker-controlled data enters through **every retrieved document, every tool response, every persisted memory, every observed environment (screenshot, DOM, file listing), every message from a peer agent**. The attacker never has to type into the operator's chat box. This is the *indirect* prompt-injection surface characterised by Greshake et al. (2023) — for chat apps it's a footnote; for agents it's the main channel.

*What changes for the threat model:* Data provenance becomes load-bearing. Every string the model sees must carry a **trust label** (user-supplied, tool-response, retrieved, memory, peer-agent), and the threat model must ask what an attacker who controls each label can do (chapter 02, data-input and memory surfaces).

### Assumption 3: the "session" is bounded — one turn, one user, one context

For a chat app, each turn is isolated (or near-isolated) and the context is a bounded window.

For an agent, work is **long-horizon**: multi-step plans, persistent memory across sessions, RAG indices shared across users, sub-agents spawned dynamically, checkpointed state on disk. An attacker who plants a bad memory today can wait weeks for a benign trigger to activate it. An attacker who poisons an embedding index affects every subsequent user of that agent, not just their own session.

*What changes for the threat model:* Time and cross-session persistence are attack primitives. The threat model must reason about **latent** compromise (the payload is planted now but activated later) and **cross-tenant** compromise (the payload lives in shared state and affects other users) — see chapters 02 and 05 (memory poisoning; identity spoofing).

### Assumption 4: the security boundary is the process, and network egress is a known set

For a chat app, the system is essentially one process, calling a known set of APIs (its model provider, its logging, maybe a search API). Egress is auditable, and outbound connections that are not on the allow-list are a bug.

For an agent, the system is a **runtime that spawns arbitrary sub-processes and calls arbitrary tools it decided at inference time to call**. Code interpreters mount filesystems. Browser agents make outbound HTTP to any URL the model chose. MCP servers introduce dynamic tool discovery. The "known set of egress endpoints" becomes "whichever set the model reached at that step."

*What changes for the threat model:* Sandboxing (network policy, filesystem policy, syscall policy) is now a threat-modelling input, not an infrastructure detail. Excessive agency (OWASP LLM06) is the label for what happens when the sandbox is too permissive. This module names the surface; mod-107 engineers the containment.

## What the level-25 prerequisite *does* still give you

Do not throw the prerequisite craft away — it is the substrate. Specifically, this module assumes and reuses:

- **STRIDE / LINDDUN discipline** — spoofing, tampering, repudiation, info-disclosure, DoS, elevation-of-privilege as the primitive verbs. Agent surfaces just multiply the *object* of each verb.
- **NIST AI RMF** map / measure / manage cycle, and the **GenAI Profile** as the framing for how a harm catalogue rolls up into a risk register.
- **MITRE ATLAS** at general depth — this module adds the specific agent-side techniques and the ID-churn caveat (chapter 07).
- **Harm-corpora fluency** — you already know how to query the AI Incident Database and OECD.AI. Chapter 04 shows how to slice those corpora for **agent-relevant** incidents and how to note where the corpora undercount because most published incidents predate broad agent deployment.
- **OWASP ML Top 10** — traditional ML security (adversarial examples, model inversion, membership inference, model theft) still applies to the model underneath your agent. The agent adds new surfaces; it does not remove old ones.

The **rule** is: do not re-teach the level-25 craft *inside* a level-40 artefact. Reference it and add the agent-specific delta. If your threat model looks like the prerequisite's harm catalogue with "agent" prepended, you did not do the work.

## The engineering artefact this module produces

At level 40 the artefact you author from this module is an **Agent Threat Model Document (ATMD)** for one concrete agent deployment. Its skeleton — flesh out in later chapters and exercises:

```yaml
atmd:
  target: acme-support-agent-v1.2
  agent_architecture_reference: <link to level-30 senior-agentic-ai-engineer's design doc>
  deployment_tier: T2  # from chapter 10
  surfaces:
    data_input: {...}
    tool_invocation: {...}
    memory_vector_store: {...}
    environment_observation: {...}
    human_in_the_loop: {...}
    cross_agent: {...}
  adversary_personas:
    - id: script-kiddie
      capabilities: [...]
      surfaces_reached: [...]
    - id: insider
      ...
    - id: nation-state-uplift
      ...
  overlays:
    owasp_agentic:   {surface × threat matrix}
    owasp_llm_top10: {surface × threat matrix}
    mitre_atlas:     {technique_id: [surface, personas, evidence]}
    google_saif:     {pillar: applied_controls}
  harm_cause_routing:
    operator: [...]
    model:    [...]
    data:     [...]
    integration: [...]
    multi_agent_emergent: [...]
  incident_grounding:
    aiid: [incident_ids]
    oecd_ai: [incident_ids]
    mit_air:  [risk_ids]
  handoffs:
    ai_risk_engineer_prereq: [...]
    senior_agentic_ai_engineer_peer: [...]
    ai_evaluation_engineer_consumer: [...]
```

Everything in the ATMD comes from one of the ten chapters below or from the level-25 prerequisite. The exercises walk you through populating each section.

## Common misreadings to avoid

- **"We threat-modelled the chatbot last year; we're covered."** No. A chatbot's threat model does not enumerate tool-invocation, memory, environment, or cross-agent surfaces. It also does not carry the persona ladder needed for agent-scale adversaries.
- **"OWASP LLM Top 10 is our threat model."** No. It is a *checklist*, and a valuable one, but a checklist is not a threat model — it does not force you to reason about *your* system's specific surfaces and adversaries. OWASP is an **overlay** on the threat model, not the threat model.
- **"Prompt injection is the main threat."** For a chat app, largely yes. For an agent, prompt injection is the **carrier**; the actual threat is what the injected instructions cause the *tool-calling loop* to do. If your threat model stops at "the model got tricked" it has not named the harm.
- **"We can handle multi-agent later."** No. Multi-agent emergent harms (chapter 09) do not appear in single-agent testing. If the deployment plan includes even one sub-agent spawn or one A2A / MCP hop, cross-agent is in scope.

## Summary

- The level-25 prerequisite gives you general STRIDE / LINDDUN / harm-model / OWASP ML craft. This module assumes it and does not re-teach it.
- Four assumptions of the general craft break for tool-using agents: outputs-are-text, input-is-the-user-prompt, sessions-are-bounded, and process-is-the-boundary.
- The level-40 artefact for this module is an Agent Threat Model Document (ATMD) covering six surfaces, a persona ladder, four overlays, a harm-cause routing table, and explicit handoffs to peer roles.
- The remaining nine chapters build the ATMD piece by piece; the five exercises exercise it against concrete examples.
