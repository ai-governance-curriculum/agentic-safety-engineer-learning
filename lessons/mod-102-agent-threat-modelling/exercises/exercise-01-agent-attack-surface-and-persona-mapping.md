# exercise-01 — Agent Attack-Surface and Persona Mapping

**Estimated effort:** 3 hours
**Prerequisite chapters:** 01, 02, 03 (helpful: 04, 10).

## Objective

Author the **six-surface × persona-tier matrix** for one concrete tool-using agent, and populate the surface-enumeration section of the Agent Threat Model Document (ATMD) skeleton introduced in chapter 01. Produce an artefact you could hand to a colleague from a chatbot-security background and have them read what an agent-specific threat model actually enumerates.

## Problem statement

Pick a concrete tool-using agent as your target. Any of the following work; you may also use your own agent if you have one:

- A **customer support agent** for a hypothetical SaaS company. It reads support tickets, searches an internal knowledge base and public docs, drafts replies for a human agent to approve, and — with human-in-the-loop approval — issues refunds up to $500 and re-schedules subscription renewals.
- A **coding agent** with access to a code interpreter, a shell in a Firecracker sandbox, a checked-out git branch, and a PR-open tool. Runs against a private repo. HITL approval on PR open.
- A **research agent** with browser + search + read-file + write-notes + call-peer-agent tools. Peer agents include a summariser sub-agent and a fact-checker sub-agent. Persistent memory of prior searches per user.

For your chosen target, produce a document that enumerates the six surfaces, the persona ladder, and the reach map that connects them.

## Requirements

Produce one Markdown artefact, ~2000–3500 words, named `atmd-<target>-surface-persona.md`. Structure:

### 1. Target agent description

- One paragraph naming the agent, its purpose, and the deployment tier you propose (chapter 10). Note pre-registration date.
- Link to (or inline sketch) the architecture: tool inventory, memory model, HITL points, environment sources, sub-agent topology. This is the *peer's* artefact in practice; here you sketch enough of it to threat-model against.

### 2. Six-surface enumeration

For each of the six surfaces (chapter 02) — data-input, tool-invocation, memory / vector-store, environment-observation, human-in-the-loop, cross-agent — produce a section that answers the engineering questions from chapter 02 for *this* target agent. Concretely:

- **Channels.** Exhaustively enumerate the channels for this surface. For example, for tool-invocation, list every tool by name, its argument surface, its side-effect scope, its credential, its reversibility, and its blast-radius cap.
- **Trust labels.** For every channel, note the trust label (user, tool, retrieved, memory, peer, environment) and whether it is preserved on read.
- **Sanitisation contract.** Per channel, name the sanitisation (or note its absence).
- **Personas reachable.** Mark which of the six persona tiers can reach this surface and by what mechanism.

### 3. Persona ladder

- Instantiate the six persona tiers (chapter 03) for *this* agent. For each tier: give a concrete capability profile, one to three example attack scenarios, and the surfaces they reach with the mechanism.
- Explicitly mark any tier you are *excluding* from the threat model and cite the justification (usually the deployment tier — chapter 10). Do not exclude Tier 3 (insider) without an unusually strong justification.

### 4. Surface × persona matrix

- The matrix from chapter 03 populated for this specific agent. Rows are persona tiers, columns are surfaces, cells are the reach mechanism (e.g., "injection via retrieved page," "direct write via admin credential," "peer-agent registration").
- Prioritisation column: rank each cell by (impact × likelihood) and mark the top-10 cells that will drive the rest of the ATMD.

### 5. Coverage justification

- One paragraph per surface explaining *what is not enumerated* and why (out of scope, not present in this deployment, deferred to a later tier).
- Do not leave silent gaps — every non-enumerated item is an explicit exclusion with reasoning.

### 6. Handoff notes

- What sections of the ATMD skeleton (chapter 01) this artefact populates.
- What sections it *does not* populate and which exercises fill them (exercise 02 for overlays, exercise 04 for multi-agent-emergent, exercise 05 for incident-corpora grounding).

## Starter guidance

- **Start with the tool inventory.** Every other surface derives from what the agent can do. If the tool inventory has surprises (dynamic MCP discovery, sub-agent spawn as a tool), those propagate everywhere.
- **Do not skip trust labels.** A common shortcut is "everything above the system-prompt line is trusted, everything below is untrusted." Real deployments are messier and the trust label is the load-bearing invariant.
- **Draw the graph first.** Sketch the six surfaces as a diagram with arrows for who writes and reads what. The prose comes easier after the diagram.
- **Prioritise ruthlessly.** The matrix will have 30+ cells. The next exercises focus on 10 of them. Rank early.
- **Consult chapter 04 lightly.** Grounding in incident corpora is exercise 05's job. Reference here but do not front-load.

## Acceptance criteria

- ✅ Single Markdown artefact ~2000–3500 words.
- ✅ Target agent named, purpose stated, proposed deployment tier cited to chapter 10.
- ✅ All six surfaces enumerated with channel list, trust labels, sanitisation contract, and personas-reachable notes.
- ✅ All six persona tiers instantiated for this specific agent with concrete capability profile and example attack scenarios.
- ✅ Surface × persona matrix populated with reach mechanism per cell, and a top-10 prioritisation column.
- ✅ Every non-enumerated item explicitly excluded with reasoning.
- ✅ Handoff notes name which downstream exercises pick up which sections.
- ✅ Inline `<!-- needs-research: ... -->` markers where evidence is not yet gathered.

## Stretch goals

- Produce a machine-readable companion (`atmd-<target>-surface-persona.yaml`) with the surface enumeration and persona matrix as structured data. This starts the ATMD skeleton in a form later exercises can extend.
- Draw a data-flow diagram (Mermaid, drawio) of the six surfaces and the arrows between them; commit the source alongside the Markdown.
- If your target agent has a real architecture spec, sit with the `senior-agentic-ai-engineer` peer (or roleplay the interaction) and produce the reciprocal cross-link between the architecture spec and this artefact.
- Produce a short "insider row" exercise: what changes if the insider persona is a data-platform engineer with RAG-write authority vs. an on-call SRE with kill-switch authority? The two produce different surface-reach maps.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference solution.
