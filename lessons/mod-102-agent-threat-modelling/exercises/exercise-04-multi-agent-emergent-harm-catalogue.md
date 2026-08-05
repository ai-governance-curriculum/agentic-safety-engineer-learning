# exercise-04 — Multi-Agent Emergent-Harm Catalogue

**Estimated effort:** 2 hours
**Prerequisite chapters:** 09 (helpful: 02 surface 6, 05, 07).
**Prerequisite exercises:** exercise-01 (surface + persona matrix). Exercise-02 (OWASP overlay) is useful but not required.

## Objective

Author the **multi-agent-emergent-harm catalogue** for your target agent — the fifth cause category from chapter 09 that is the least likely to be named unless the ATMD forces it as an explicit choice. Produce a document that enumerates emergent failure modes across the agent graph (this agent plus sub-agents plus peers plus MCP servers), attributes each to a primary owner, and routes each to an architecture-side fix or a runtime-side containment.

## Problem statement

Multi-agent-emergent harms (chapter 09) do not appear when you threat-model each agent in isolation. They emerge from the *interaction*: cross-agent injection propagation, cascading hallucination across the graph, principal confusion, sub-agent explosion, rogue-agent registration, coordinated goal drift, cross-agent memory poisoning, and repudiation across a message chain. For any deployment that spawns even one sub-agent or accepts even one peer-agent message (chapter 02 surface 6), this catalogue is load-bearing.

Take the target agent from exercise-01 and extend it — if it currently has no cross-agent surface, add a plausible sub-agent (`summariser`) and a plausible peer (`fact-checker` reached over MCP). Then enumerate the emergent failure modes that appear only in the resulting graph.

## Requirements

Produce one Markdown artefact, ~1200–2000 words, named `atmd-<target>-multi-agent-emergent.md`. Structure:

### 1. Agent-graph description

- Sketch (Mermaid or prose) the agents in the graph: this agent, sub-agents it can spawn, peer agents it can call, MCP servers it discovers, any external agents whose messages it accepts.
- For each node: identity model, credential inheritance, memory sharing (isolated / shared), message-protocol (A2A, MCP, direct API).
- Mark edges as read / write / spawn / trust-inherit and note whether the edge is bidirectional.

### 2. Emergent-harm enumeration

For each of the failure modes below, produce a 100–200 word section that describes how it manifests in *this* graph. Cover the following at minimum; add any that are graph-specific:

- **Cross-agent injection propagation.** A payload injected into agent A rides into agent B's context via A's output; B has no independent trust check.
- **Cascading hallucination across the graph** (chapter 05, T5 in a multi-agent instantiation). Agent A summarises with a fabricated citation; agent B treats the summary as authoritative.
- **Principal confusion.** Agent B receives a message that names a principal (user, tenant, admin) A did not intend to authorise; B acts on the false principal claim.
- **Sub-agent explosion (resource overload).** One prompt injection triggers recursive sub-agent creation until a depth or breadth budget aborts — or does not.
- **Rogue-agent registration.** A malicious node registers as a legitimate peer in discovery (MCP registry, agent marketplace) and starts receiving traffic.
- **Coordinated goal drift.** Agents A and B each nudge sub-goals toward a shared misaligned vector across many turns; neither in isolation is out of policy.
- **Cross-agent memory poisoning.** A poisoned memory written by A is read into B's context via a shared RAG index or memory store.
- **Repudiation across the message chain.** A downstream harm cannot be traced back to the specific agent that authored it because principal capture is lost across hops.

For each failure mode:

- Name the surfaces (chapter 02) and the persona tiers (chapter 03) involved.
- Cite the OWASP Agentic threat (chapter 05) and, where applicable, the ATLAS technique (chapter 07) that this failure mode expresses in the multi-agent case.
- Attribute the primary cause (chapter 09) — `multi_agent_emergent` is often but not always the primary; some rows have `integration` or `operator` as primary with `multi_agent_emergent` contributing.
- Name the fix-owner routing: which of `senior-agentic-ai-engineer` (architecture), platform-security peer (integration), mod-105 (cross-agent surface engineering), mod-107 (sub-agent containment), or mod-110 (control-eval in multi-agent settings) picks it up.

### 3. Cause-heat-map instantiation

- Instantiate the cause × surface heat map from chapter 09 for the multi-agent-emergent row of the ATMD. Which surfaces cluster on this cause category for this graph?
- Compare to the chapter 09 heat map: which cells shift when you populate for this specific deployment?

### 4. Tripwire set

- Author the tripwires (chapter 10) that catch multi-agent-emergent drift: new peer registered, sub-agent depth exceeds N, cross-agent memory read-count exceeds baseline, principal-mismatch alerts. Name the SIEM / observability signal that instruments each tripwire and the deployment tier at which it fires.

### 5. Architecture-side punch list

- The handoff list to the `senior-agentic-ai-engineer` peer (chapter 09, chapter 10): the architectural changes this catalogue recommends (credential-scope reduction on sub-agent spawn, principal-preserving message envelope, MCP-registry allow-list, memory-namespace isolation across agents, per-hop provenance labels). Each item cites the emergent-harm row that motivates it.

## Starter guidance

- **The graph is the artefact.** If you cannot draw the graph, you cannot enumerate the emergent behaviour. Sketch first, prose second.
- **Do not attribute every row to `multi_agent_emergent`.** Chapter 09 warns against blaming the graph when the operator over-scoped a credential or the model hallucinated. Pick the primary cause honestly; note the emergent contributing factor.
- **The academic corpus is nascent.** Cite the multi-agent adversarial-coordination papers listed in `resources.md` (Ju et al. on manipulated-knowledge propagation, Gu et al. on Agent Smith exponential spread) as forward-looking evidence when the AIID has no direct hit.
- **Bidirectional edges hide the biggest risks.** A one-way parent → sub-agent spawn is bounded; a bidirectional peer relationship where B can also call A is where cross-influence loops live. Mark them and interrogate them first.
- **Do not omit repudiation.** Repudiation is a governance failure even when no individual agent misbehaves. It shows up when the incident-response team asks "who authored the request that triggered the harm?" and the audit-log answer is ambiguous across the graph.

## Acceptance criteria

- ✅ Single Markdown artefact ~1200–2000 words.
- ✅ Agent-graph diagram or prose sketch present with per-node identity / credential / memory model and edge annotations.
- ✅ All eight required emergent-harm failure modes populated for this graph, or explicitly excluded with reasoning.
- ✅ Each populated row cites its surface, persona tiers, OWASP Agentic threat, ATLAS technique (where applicable), primary cause, and fix-owner.
- ✅ Cause-heat-map instantiation compared against the chapter 09 baseline.
- ✅ Tripwire set with SIEM / observability signal per tripwire and tier of first fire.
- ✅ Architecture-side punch list ready to hand to `senior-agentic-ai-engineer` peer.
- ✅ Inline `<!-- needs-research: ... -->` markers where academic or corpus evidence is not yet gathered.

## Stretch goals

- Extend the graph to a *three-hop* topology (this agent → sub-agent → peer of the sub-agent) and re-run the enumeration; identify which failure modes appear only at three hops that were invisible at two.
- Produce a **tabletop-scenario doc**: pick the top emergent failure mode and walk a Tier 4 (cybercrime affiliate) or Tier 5 (targeted attacker) persona through executing it end-to-end across the graph, hop by hop, tool call by tool call.
- Model a **rogue MCP-server registration** attack: propose a discovery-hardening design (signed manifests, pinning, TOFU with alerting) and note which chapter-08 SAIF pillar controls it.
- Cross-map the catalogue against the FSF autonomy CCLs (mod-101) — which of these emergent failure modes would trip a frontier-lab autonomy tripwire if the agent capability envelope moved toward Tier 4 (chapter 10)?

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. Solutions live in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo.
