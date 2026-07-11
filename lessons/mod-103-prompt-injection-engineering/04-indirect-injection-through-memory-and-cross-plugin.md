# 04 — Indirect Injection Through Long-Term Memory and Cross-Plugin Channels

## Motivation

Chapter 03 covered the two highest-volume indirect channels (retrieval and tool responses). This chapter covers the two most *persistent* and *transitive* indirect channels — **long-term memory / vector stores** and **cross-plugin** (peer agent, MCP, A2A) — where a payload planted once can activate later, activate for *other* users, or activate inside *other* agents entirely.

These channels change the shape of the threat model. Retrieval and tool-response payloads fire during the session that fetches them. Memory and cross-plugin payloads fire *after* the session that planted them and often against a *different* principal. The mitigation contract is correspondingly different — it is not enough to sanitise on read; the write path itself has to be treated as an attack surface.

## The two channels and their primitives

For this chapter's scope:

- **Long-term memory / vector stores**
  - Per-user conversation memory
  - Per-user profile summary
  - Shared / cross-tenant RAG index
  - Shared embedding store
  - Key-value scratchpad (agent-authored notes across sessions)
  - Feedback signals lifted into fine-tuning corpora (bidirectional with training data)
- **Cross-plugin channels**
  - Sub-agent messages (this agent spawned another agent)
  - Peer-agent messages (an agent-to-agent protocol like A2A)
  - Model Context Protocol (MCP) server tool responses and tool manifests
  - Plugin-marketplace tool responses
  - Dynamically discovered tools whose provenance is not statically pinned

All six direct-injection primitives from chapter 02 (instruction-override, refusal-bypass, role-hijack, task-override, exfil-through-completion, output-format hijack) ride these channels — with two additional threat *properties* that only appear here: **latency** (attack plants now, fires later) and **cross-principal reach** (attack plants under principal A, fires under principal B).

## Long-term memory — per-user conversation and profile

**Delivery mechanics.** Modern agent products persist conversation summaries, key facts, and personalisation vectors across sessions. The next session pre-loads that memory into the model context. If an attacker can write anything to that memory in session 1, the same string appears in the model context in session 2.

**Where the payload lives.**

- **User-authored fact** — the user (or attacker impersonating the user) tells the assistant to "remember" a fact whose content is an instruction. The memory system stores it verbatim.
- **Summary hijack** — the summariser that reduces the conversation into a memory-worthy note is itself a model call; a payload can shape *what* it summarises into the note, planting an instruction inside the note that will be re-read.
- **Profile-vector poisoning** — some products embed the user's messages into a profile vector used at retrieval. A crafted message set can shift the vector so that later retrievals surface a poisoned document.
- **Retention edges** — a memory entry marked for expiry may nonetheless linger in a backup or a stale cache.

**Primitives.**

- **Latent instruction-override** — the payload sits benignly in memory until a benign user turn triggers "let me check what you asked me to remember" — at which point the payload fires.
- **Latent task-override** — the memory instructs the agent to silently perform an action every time a specific benign phrase appears in a future user prompt.
- **Latent exfil-through-completion** — the memory instructs the agent to include a specific side-channel URL in future outputs.

**Sanitisation contract to look for.**

- Memory entries are labelled with provenance (user-authored vs. model-authored vs. admin-authored) and the label is preserved on read.
- Memory writes require an explicit confirmation step (the user is shown the string to be stored).
- Memory reads render the entries with delimiters that cannot be spoofed from within.
- Retention limits are enforced end-to-end (including caches, backups).
- Anomaly detection on the write path (a memory whose content looks like an instruction rather than a fact is flagged).

**Grounding.** Frontier labs' memory features (ChatGPT Memory, Claude's context / memory tooling, Gemini's saved-info features) have all had documented injection issues. Any red-team engagement against an agent with a memory feature must include memory-poisoning cases. <!-- needs-research: cite specific published memory-injection incidents against ChatGPT Memory, Claude / Anthropic memory tools, and Gemini saved-info; check the OWASP Agentic threats section on memory poisoning for the current list of primary sources. -->

## Long-term memory — shared / cross-tenant vector stores

**Delivery mechanics.** A knowledge-base RAG index is often shared across users of a tenant or across tenants of a platform. Any principal with write access — an admin, a document uploader, a support-form user whose ticket is auto-ingested — can plant a document. The index does not filter on principal at read time; it filters on similarity.

**Where the payload lives.**

- **Attacker-uploaded document** in a corpus with permissive write authority.
- **User-generated content ingestion** — reviews, comments, forum posts, wiki edits that are auto-embedded into the index.
- **Embedding-shape attack** — a crafted document whose embedding sits close to a common query cluster, so it is retrieved for queries that have nothing to do with its topic.
- **Metadata-field poisoning** — the `source` or `citation` metadata associated with a chunk carries the payload rather than the chunk text.

**Primitives.** The high-impact primitive here is **cross-tenant task-override**: victim-user-B asks a benign question; the retriever surfaces attacker-user-A's poisoned document; the agent follows the injected instruction on victim-user-B's session with victim-user-B's credentials.

**Sanitisation contract.**

- Per-tenant isolation of the retrieval scope.
- Corpus-write authority is restricted to an authenticated principal with a documented review workflow.
- User-generated content is embedded into a *separate* index with a lower trust label, and reads mark the origin.
- Canary probes: on retrieval, a periodic query with known-safe answer is issued; a mismatch flags corpus integrity.
- Anomaly detection: documents that are close to many queries or whose text contains instruction-shaped patterns are flagged for review.

**Grounding.** OWASP Agentic AI Threats and Mitigations (T1 — Memory Poisoning) covers this class explicitly; OWASP LLM04 (Data and Model Poisoning) and LLM08 (Vector and Embedding Weaknesses) frame the model-security angle. <!-- needs-research: confirm the current OWASP Agentic threat number for memory poisoning; the numbering has iterated. -->

## Long-term memory — feedback loops into training

**Delivery mechanics.** Thumbs-up / thumbs-down (or their agent-scale analogues) may be lifted into a preference dataset used for future fine-tuning. If the loop is unattended, an attacker who plants a poisoned message and thumbs-up's it can *train the next model* to prefer the poisoned message shape.

**Primitives.** This is where injection composes with **backdoor / trojan** attacks from adversarial ML. The primitive is not one-shot — it is *slow*, over many samples, and its target is the next training run.

**Sanitisation contract.** Feedback-signal ingestion into training must be gated by human review, anomaly detection, and provenance tracking. If the loop is fully automatic, the surface is open by construction.

**Boundary to mod-105 and mod-108.** Memory poisoning at scale is the shared surface between mod-103 and mod-105 (Agent-Specific Attack Surface Engineering) and between mod-103 and mod-108 (Frontier-Scale Guardrails and Safety-Monitor Engineering). This module owns the injection primitives that plant the payload; those modules own the containment.

## Cross-plugin — sub-agent messages

**Delivery mechanics.** A parent agent spawns a sub-agent to accomplish a subtask ("summarise this document," "browse to find X," "check whether Y is possible"). The sub-agent's completion becomes an input to the parent. If the sub-agent has been compromised — by any of the channels in chapters 03 or 04 — its output is a fresh injection channel into the parent.

**Where the payload lives.**

- **Sub-agent completion text** — a summariser sub-agent produced a summary that includes an instruction the parent then reads.
- **Sub-agent tool-call log** — the parent reads the sub-agent's tool trajectory as a source of authority.
- **Sub-agent's memory writes** — a sub-agent that shares a memory store with the parent can plant a payload the parent will pick up.

**Primitives.** All six. Task-override and instruction-override are the highest-impact because sub-agents' outputs are typically treated as *trusted computed results* — the parent does not re-check them.

**Sanitisation contract.**

- Sub-agent completions are labelled with the sub-agent's identity and trust tier when they enter the parent's context.
- Sub-agents cannot write to the parent's memory or the parent's tool state directly.
- The parent's argument validator re-runs on any tool call whose arguments were shaped by sub-agent output.

## Cross-plugin — peer agents (A2A)

**Delivery mechanics.** An **Agent-to-Agent (A2A) protocol** lets one operator's agent send messages to another operator's agent. The peer's messages carry the peer operator's authority; but a peer that has itself been compromised by any indirect channel is now a fresh delivery vehicle into this agent.

**Where the payload lives.**

- **Peer-agent message body** — the string content the peer sent.
- **Peer-agent tool call proposals** — proposals the peer suggests this agent should make on its behalf.
- **Peer-agent identity claims** — a peer that claims a role ("I am the operator's compliance officer") the receiving agent may accept without verification.

**Primitives.** Role-hijack is particularly potent here because A2A protocols typically carry role / claim metadata; a peer that claims "system" role can trigger role-hijack unless the receiving agent's protocol handler is careful.

**Sanitisation contract.**

- A2A messages carry a signed identity and the identity is *verified*, not accepted at face value.
- Peer messages are labelled distinctly from user or tool inputs.
- The receiving agent's trust in a peer is bounded by an explicit policy that names which peers it trusts for which actions.

**Grounding.** OWASP Agentic AI Threats and Mitigations covers Identity Spoofing & Impersonation, Agent Communication Poisoning, and Rogue Agents in Multi-Agent Systems. <!-- needs-research: confirm the current OWASP Agentic threat numbering for these three; ID drift is common. -->

## Cross-plugin — Model Context Protocol (MCP) servers

**Delivery mechanics.** MCP is a protocol for exposing tools and context to an LLM host at inference time. A client (an agent runtime) connects to one or more MCP servers, which expose:

- **Tools** — callable functions the model can invoke.
- **Resources** — read-only content the model can be given.
- **Prompts** — server-authored prompt fragments the model may be shown.

Every one of these is a channel through which the server can plant attacker-controlled text in the model's context. If the MCP server is compromised, third-party, or dynamically discovered without vetting, the entire agent runtime inherits the server's authority.

<!-- needs-research: pin the currently published MCP spec URL and confirm the resource / prompt / tool trichotomy against the current spec version. The MCP spec has iterated. -->

**Where the payload lives.**

- **Tool manifest fields** — the tool's name, description, and argument schema are strings the model reads to decide when to invoke the tool. A malicious server can craft a description that reads as an instruction.
- **Tool response bodies** — as in chapter 03's external-API case.
- **Prompt fragments** — server-supplied prompt text that the client injects into the model's context.
- **Resource content** — server-supplied documents.
- **Dynamic tool discovery** — a server may register new tools mid-session, whose names / descriptions were not part of the client's static allow-list.

**Primitives.** All six. Cross-plugin instruction-override delivered via a tool-manifest description is particularly hard to spot because operators rarely audit tool descriptions the way they audit tool implementations.

**Sanitisation contract.**

- MCP servers are pinned by identity and version; discovery is gated by an allow-list.
- Tool manifests are reviewed at onboarding, with a re-review triggered by any update.
- Tool descriptions are rendered to the model with an unambiguous label ("this is a tool description authored by `<server-id>`, not by the operator") and cannot spoof operator-role delimiters.
- Server-authored prompt fragments and resources are labelled with provenance and are not treated as operator-authored.

**Grounding.** MCP prompt-injection findings have been reported since the protocol's public release — see Anthropic's MCP documentation notes and third-party writeups. <!-- needs-research: cite specific published MCP prompt-injection findings and current published guidance from the MCP maintainers on server-trust posture. -->

## Cross-plugin — plugin marketplaces and dynamic discovery

**Delivery mechanics.** An agent runtime may discover tools at runtime from a marketplace or registry. Each discovered tool ships with a manifest describing its capabilities, arguments, and behaviour. The runtime often accepts the manifest as ground truth.

**Where the payload lives.**

- **Manifest description** — as with MCP tool descriptions.
- **First tool response** from a freshly discovered tool — the tool can inject anything.
- **Registry metadata** — the marketplace's own description of the tool.

**Primitives.** All six, plus a category-level threat that is not injection but supply chain: a plugin that is legitimate today can be updated to a malicious version tomorrow. The injection surface is the *carrier*; the supply-chain risk is the *reason* the carrier is populated.

**Sanitisation contract.**

- Dynamic discovery is off by default. If on, the discovered tool's manifest is subject to human review before invocation.
- Signed manifests with pinned publisher identity.
- The registry itself is treated as an external service with its own trust tier.

## Cross-cutting property — latent activation

Memory and cross-plugin channels introduce **latency**: the payload sits in state until a trigger activates it. Threat modelling has to reason about the trigger, not just the payload:

- **Time triggers** — a payload waits until the next Monday-morning summary.
- **Content triggers** — a payload waits until the user asks a specific question.
- **Principal triggers** — a payload waits until a specific user (e.g., an admin) logs in.
- **Agent triggers** — a payload waits until a specific sub-agent is spawned.

Every latent finding you emit names the trigger. Without the trigger, the finding is a *risk* and not an *attack*.

## Cross-cutting property — cross-principal reach

Memory / cross-tenant and cross-plugin payloads fire *for other principals*. Every finding names:

- The principal who wrote the payload.
- The principal (or class of principals) under whose session the payload fires.
- The credentials whose blast radius the payload accesses at fire time.

The cross-principal property is what turns a nuisance injection into a data-exfiltration incident. Chapter 07's defence catalogue explicitly addresses cross-principal isolation.

## Common engineering mistakes at the memory + cross-plugin boundary

- **Treating memory as read-only in your threat model.** Every retrieval is *also* an ingestion channel because the model can propose new memories mid-session.
- **Assuming sub-agents inherit the parent's threat model.** They don't. Each sub-agent has its own six-surface threat model (mod-102), and a compromised sub-agent is an injection channel into its parent.
- **Trusting MCP tool descriptions.** The model reads tool descriptions as authoritative summaries of what a tool does. A malicious description can drive tool-choice behaviour without any tool response at all.
- **Not testing the latent activation.** A memory-planted payload that never fires in your evaluation is a false negative. Chapter 06 forces trigger-condition testing.
- **Missing cross-principal exercises.** Set up two personas — attacker and victim — and verify explicitly that a payload written by attacker under attacker's authority fires under victim's authority. If your harness only tests attacker-writes-and-attacker-reads, you have not exercised the interesting case.

## Summary

- Long-term memory / vector stores and cross-plugin channels are the *persistent* and *transitive* indirect-injection channels. They add two properties on top of chapter 03's channels: **latent activation** and **cross-principal reach**.
- Memory channels include per-user memory, shared corpora, and feedback loops into training. Cross-plugin channels include sub-agents, peer agents (A2A), MCP servers, and plugin marketplaces.
- Every primitive from chapter 02 rides these channels. The high-impact combinations are cross-tenant task-override, latent instruction-override, and role-hijack via a spoofed peer or MCP role claim.
- Trust labels, per-tenant isolation, signed manifests, and identity-verified peer messaging are the primary sanitisation contracts; their absence is a finding.
- Chapter 05 layers obfuscation across every channel from chapters 02–04; chapter 06 assembles the harness; chapter 07 catalogues defences and chapter 08 stress-tests them.
