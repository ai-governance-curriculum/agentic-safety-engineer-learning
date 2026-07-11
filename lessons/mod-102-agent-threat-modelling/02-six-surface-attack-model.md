# 02 — The Six-Surface Agent Attack Model

## Motivation

Every subsequent overlay in this module — OWASP Agentic, OWASP LLM Top 10, MITRE ATLAS, Google SAIF — is a set of *threats*. A threat is only meaningful against a *surface* that carries it. The classical chat-app threat model has essentially one surface (the user prompt) and one channel (the completion). An agent has six.

Enumerating those six surfaces is the load-bearing move in this module. Once the surfaces are named, the overlays land in the right cells and the persona ladder (chapter 03) tells you which persona reaches which surface. Miss a surface and every subsequent artefact — the harm catalogue, the guardrail map, the safety case — is incomplete in exactly the same place.

## The six surfaces

```
                        ┌──────────────────────────────────────────┐
                        │            AGENT RUNTIME                 │
                        │  ┌────────────┐    ┌─────────────────┐   │
                        │  │  Model     │    │  Planner /      │   │
     User prompt ──▶ 1  │  │  (LLM)     │◀──▶│  Orchestrator   │   │
                        │  └────────────┘    └─────────────────┘   │
                        │        ▲                    │            │
     Documents  ──▶ 1   │        │                    ▼            │
     Files                       │            ┌─────────────┐      │
                                 │            │  Tool call  │──▶ 2 Tools (search, email, code, ...)
                                 │            └─────────────┘      │
                        │        │                    │            │
     Vector store ◀▶ 3  │        │                    ▼            │
     Memory              ────────┼────Tool response ──┘            │
                        │        │                                 │
     Observed env  ▶ 4  │        │                                 │
     (browser DOM,       ────────┘                                 │
      screenshots,       │                                         │
      file listings)     │                                         │
                        │        │                                 │
     Human approver ◀▶ 5│                                         │
                        │        │                                 │
     Peer agents   ◀▶ 6 │                                         │
                        └──────────────────────────────────────────┘
```

- **1. Data-input surface** — text and structured data the runtime feeds *into the model's context window*: the user prompt, the system prompt, retrieved documents, uploaded files, tool responses, memory recalls, environment observations, peer-agent messages.
- **2. Tool-invocation surface** — the set of tools the model can call, plus the runtime that executes those calls: argument validators, side-effect scopes, credentials the tools use, rate limits, blast-radius caps.
- **3. Memory / vector-store surface** — anything the agent *writes back* that persists into future turns or sessions: conversation memory, per-user profile, RAG index, embedding store, key/value scratchpad, file writes.
- **4. Environment-observation surface** — the world state the agent observes: browser DOM, screenshots, filesystem listings, terminal output, git repo state, CI logs, external monitoring signals.
- **5. Human-in-the-loop surface** — every place a human is asked to approve, correct, or terminate an agent action: approval buttons, edit-and-approve workflows, kill-switches, escalation channels, feedback loops.
- **6. Cross-agent surface** — every place another agent (a sub-agent, a peer agent over A2A / MCP, an untrusted external agent) exchanges instructions, memories, or tool-call results with this one.

The six are **necessarily distinct** because they map to different owners (chapter 09 — harm-cause attribution), different mitigations (chapters 05–08), and different persona reach (chapter 03). A threat model that fuses "memory" and "data input" loses the persistence angle; a threat model that fuses "tool invocation" and "environment observation" loses the read-vs-write asymmetry.

## Surface 1 — Data-input

**What lands here.** Every string the model reads as part of its context: user prompt, system prompt, retrieved documents, uploaded files (as decoded text), tool responses, memory recalls, environment observations rendered as text, and peer-agent messages. Chat apps have this surface too; the agent-specific expansion is *the number and provenance* of strings.

**Engineering questions to answer for this surface**:

- What is the exhaustive set of channels that can put text into the model's context? (Do not underestimate this. Retrieval-augmented answers, tool-response prefixes, and system-prompt fragments assembled at runtime all count.)
- For each channel, what is the **trust label** (user-supplied, tool-response, retrieved, memory-recall, peer-agent) and how is that label preserved when strings are concatenated? Are the trust labels visible to the model (spotlighting, StruQ) or hidden?
- What is the **sanitisation contract** per channel? Length caps, character-set rules, HTML / markdown neutering, URL rewriting, code-fence quarantine?
- Which channels are **retrieved on the model's own request** (agentic search, agentic browse, tool-triggered RAG)? Attacker-influenceable channels expand as soon as the model can choose what to retrieve.
- What does the model see for each channel in the **worst-case adversarial-content scenario** — 10 KB of hostile instructions embedded in a search-result snippet or PDF metadata?

**Primary threat labels to expect (overlays):**

- OWASP LLM01 Prompt Injection (direct + indirect).
- OWASP Agentic: Cascading Hallucination Attacks (T5).
- MITRE ATLAS AML.T0051 (LLM Prompt Injection).

## Surface 2 — Tool-invocation

**What lands here.** The set of tools the model can call and the runtime that executes those calls. A tool is any function whose invocation produces a *side effect* (send email, execute code, write file, transfer funds, spawn sub-agent) or *retrieves privileged data* (query internal DB, read filesystem). Even "read-only" tools that touch privileged sources belong here.

**Engineering questions to answer for this surface**:

- What is the **exhaustive tool inventory**? Include dynamically discovered tools (MCP servers, plugin registries) and sub-agent-spawn as a tool.
- For each tool: **argument surface** (types, size caps, allowed URL patterns), **side-effect scope** (this tenant only? cross-tenant? cross-region?), **credential** (whose credential is the tool using — the model's, the operator's, the user's, a service principal's?), **reversibility** (idempotent? compensable? irrevocable?), **latency to human detection** (seconds? days? never?), and **blast-radius cap** (per-call limit? per-session budget?).
- Who **decides** a tool call is allowed — the model alone, an argument validator, a policy engine, a human approver?
- What is the **egress network policy** in the tool's runtime? Which URLs / IP ranges is the tool allowed to talk to?
- What is the **audit-log discipline** — is every tool call recorded with immutable arguments, principal, and outcome? Can an attacker whose payload runs *inside* a tool tamper with the log?

**Primary threat labels to expect (overlays):**

- OWASP LLM06 Excessive Agency.
- OWASP Agentic: Tool Misuse (T2), Privilege Compromise (T3), Resource Overload (T4), Unexpected RCE and Code Attacks.
- MITRE ATLAS AML.T0053 (LLM Plugin Compromise) and adjacent execution techniques.

## Surface 3 — Memory / vector-store

**What lands here.** Anything the agent writes that persists — conversation memory, per-user profile summary, RAG document index, embedding store, key/value scratchpad, file writes to shared volumes, git commits to shared branches.

**Engineering questions to answer for this surface**:

- What are the **writeable stores** and who can write to each? Is memory per-user (isolated) or global (shared)? Is the RAG index per-tenant or shared?
- What is the **write authority** — model-authored writes vs. user-authored vs. admin-authored? Is the trust label preserved on read?
- What is the **retention policy** — do memories expire? How? Who initiates removal? Is there a right-to-be-forgotten path?
- What is the **integrity story** for the store — do writes carry provenance metadata? Are embeddings signed or content-addressed? Is there a poisoning-detection pass on read (anomaly detection, canary probes)?
- What is the **isolation model** across tenants and sessions? Can a payload written by attacker-user-A be read into the context of victim-user-B? (This is the memory-poisoning cross-tenant risk — chapter 05.)
- Is the store **read-back into training data** anywhere? (User feedback loops, thumbs-up-fine-tune pipelines, DPO pipelines.)

**Primary threat labels to expect (overlays):**

- OWASP Agentic: Memory Poisoning (T1).
- OWASP LLM04 Data and Model Poisoning; LLM08 Vector and Embedding Weaknesses.
- MITRE ATLAS Publish Poisoned Datasets / Erode Dataset Integrity.

## Surface 4 — Environment-observation

**What lands here.** Any *world state* the agent observes as input beyond canonical text sources: rendered browser DOM, screenshots (multimodal input), filesystem listings, terminal output, git repo state, CI job logs, external monitoring signals, IoT sensor feeds.

**Engineering questions to answer for this surface**:

- What environments does the agent observe? For each: what is the **rendering pipeline** and where can attacker-influenced content ride along it (HTML in a DOM, alt-text in an image, filenames in a listing, ANSI escapes in a terminal output)?
- Are observations subjected to a **sanitisation pass** before reaching the model, or fed raw?
- Are multimodal observations (screenshots, images, PDFs) checked for **embedded adversarial content** — steganographic prompt injection, poisoned OCR-text overlays, unicode-tag payloads?
- Is the environment **trusted or untrusted**? A browser agent operating on the open web is fundamentally untrusted; a coding agent operating on a fresh Firecracker sandbox with a checked-out branch is bounded but still needs care around remote-content fetches.
- Which observations **inform tool-call arguments** (the model reads the DOM and then calls `click(element_id)`)? This is where injection becomes execution.

**Primary threat labels to expect (overlays):**

- OWASP LLM01 Indirect Prompt Injection via environment.
- OWASP Agentic: Cascading Hallucination Attacks (T5), Tool Misuse via injected environment (T2).
- MITRE ATLAS AML.T0051 (LLM Prompt Injection) with the indirect variant.

## Surface 5 — Human-in-the-loop

**What lands here.** Every human-in-the-loop touchpoint: pre-execution approval buttons, edit-and-approve workflows, kill-switches, escalation channels, downstream feedback signals (thumbs-up / thumbs-down) that inform later runs.

**Engineering questions to answer for this surface**:

- For each **high-stakes action**, is there a human approval? What is the human shown, and could a compromised model *shape* what the human sees (misleading summary, truncated diff, benign-looking argument that hides a payload)?
- What is the **approval fatigue budget** — how many approvals per hour does a human tolerate before rubber-stamping? (This is the "human-in-the-loop bypass" threat — approval bombing.)
- What is the **default on timeout / no response**? Fail-open is a threat; fail-closed with retry is a resource-overload vector; the correct default depends on the tier (chapter 10).
- Can the approval channel itself be **social-engineered** (spoofed approver identity, phishing-shaped escalation), and how does the model reason about the approver's authority?
- Is there a **kill-switch** and does it revoke *in flight* work (running tool calls, spawned sub-agents) or only future work?
- Are **feedback signals** (thumbs-up / thumbs-down) trusted as ground truth downstream? If so they become a poisoning surface (write a bad memory today, thumb-up it, watch it get lifted into fine-tuning).

**Primary threat labels to expect (overlays):**

- OWASP Agentic: Overwhelming Human in the Loop, Human Manipulation.
- OWASP LLM06 Excessive Agency (when the HITL is meant to be the check).
- MITRE ATLAS techniques that target monitoring / alerting.

## Surface 6 — Cross-agent

**What lands here.** Any communication with other agents: sub-agents this agent spawns, peer agents over Agent-to-Agent (A2A) protocols, Model Context Protocol (MCP) servers, agent marketplaces, third-party external agents whose instructions enter this agent's context.

**Engineering questions to answer for this surface**:

- What agents can this agent **spawn** or **call**? Is the child agent's identity, model, and system prompt *this* agent's responsibility to verify?
- What agents can **send messages** to this agent (peer, upstream, downstream)? What is the trust model for each?
- Does this agent **discover** peer agents dynamically (registry lookup, MCP discovery)? If so, discovery is an attack surface (identity spoofing, rogue-agent registration).
- What is the **credential model** for cross-agent calls — does the child inherit the parent's credentials, or does it run under its own? (Credential inheritance is a privilege-compromise vector.)
- Do memories written by *this* agent get **read into peer agents' contexts**? (Cross-agent memory poisoning — the "poison one agent, compromise a fleet" risk.)
- Is there a **repudiation** story — can the operator prove which agent said what to which peer? (Untraceability across an agent graph is a governance failure even when no individual agent misbehaves.)

**Primary threat labels to expect (overlays):**

- OWASP Agentic: Identity Spoofing & Impersonation, Agent Communication Poisoning, Rogue Agents in Multi-Agent Systems, Repudiation & Untraceability. <!-- needs-research: confirm current OWASP Agentic threat IDs and names against the latest published edition of "Agentic AI Threats and Mitigations". -->
- MITRE ATLAS techniques around plugin compromise applied at the agent-to-agent boundary.
- Google SAIF pillars 1 (extend security foundations to the AI ecosystem) and 4 (harmonise platform-level controls). <!-- needs-research: confirm SAIF's currently published pillar list — Google has iterated the framing. -->

## The surface × persona matrix (previewing chapter 03)

Chapter 03 introduces the adversary-persona ladder. Every persona × surface cell is either *reachable* (the persona has the capability, standing, and intent to touch that surface) or *not reachable*. A crude preview:

| Surface | Script-kiddie | Curious power-user | Insider | Cybercrime affiliate | Nation-state uplift |
|---|---|---|---|---|---|
| Data-input | ✅ direct prompt | ✅ + retrieved | ✅ + retrieved | ✅ + retrieved | ✅ + retrieved + supply chain |
| Tool-invocation | Indirect via injection | Indirect + tool-abuse chains | ✅ direct (credentials) | Indirect + supply chain | Indirect + supply chain + tool authoring |
| Memory / VS | Indirect via injection | Cross-session probes | ✅ direct (admin) | Poisoned corpora | Poisoned corpora + weight-tuning |
| Environment | Own docs / URLs | Web-adjacent | ✅ direct (internal env) | Compromised sites at scale | Own infra + supply chain |
| Human-in-the-loop | Approval-bombing UX | Social engineering | ✅ direct authority | Phishing at scale | Long-horizon deception |
| Cross-agent | Occasional | Rare | ✅ direct control | Rogue-agent registration | Full A2A/MCP compromise |

Chapter 03 defines each persona precisely and exercises 01 and 04 populate the matrix for a specific agent.

## Cross-cutting concerns that live between surfaces

Not every threat lives on one surface. Three cross-cutting concerns worth naming now:

- **Long-horizon persistence.** A payload planted in memory (surface 3) fires when a benign environment observation (surface 4) triggers it and is executed via a tool call (surface 2). The threat is the *chain*, not any one link.
- **Elicitation gap in adversarial settings.** The general elicitation-gap concept from RSP / Preparedness / FSF (mod-101) applies here: an attacker will elicit worse behaviour than your internal red team by definition, so plan the threat model against elicitation-worst-case, not against default sampling.
- **Emergent multi-agent behaviour.** With N agents, the number of message paths and cross-influence loops grows super-linearly. Multi-agent emergent harms (chapter 09) are their own category and cannot be enumerated by threat-modelling each agent in isolation.

## Summary

- Agent threat modelling requires enumerating **six surfaces**: data-input, tool-invocation, memory / vector-store, environment-observation, human-in-the-loop, cross-agent.
- Each surface has a distinct set of engineering questions (channels, trust labels, sanitisation, blast radius, reversibility, provenance) and a distinct set of overlay-threat labels.
- The persona × surface matrix (chapter 03) determines which threats each persona reaches, and the harm-cause taxonomy (chapter 09) determines who owns each mitigation.
- Miss a surface and every downstream artefact is incomplete in exactly that place.
