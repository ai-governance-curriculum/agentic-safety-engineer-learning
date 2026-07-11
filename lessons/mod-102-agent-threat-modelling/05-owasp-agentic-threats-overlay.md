# 05 — OWASP Agentic AI Threats and Mitigations Overlay

## Motivation

The **OWASP GenAI Security Project** publishes the "Agentic AI — Threats and Mitigations" paper as the practitioner-consensus catalogue of agent-specific threats. It is the single most useful checklist for a level-40 threat modeller because it was drafted specifically for agents (not chatbots), it names both threats and mitigations, and every reviewer at this level has read it.

Your job is not to rewrite it. Your job is to lay it as an **overlay** over the six-surface model (chapter 02) so that every OWASP-listed threat lands in the surface it belongs to and every one of your surfaces has been checked against every OWASP threat.

## Primary source

- **[OWASP GenAI — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/)** — read the current published version front-to-back before this chapter.
- **[OWASP GenAI Security Project — hub](https://genai.owasp.org/)** — includes the LLM Top 10, the Agentic threats paper, the GenAI Red Teaming Guide, and adjacent materials.

<!-- needs-research: pin the exact version of "Agentic AI Threats and Mitigations" this chapter is written against. OWASP has iterated the threat list; expect names and IDs to shift and add version-observed dates in citations. -->

## The threat list this module covers

The learning objective for this module explicitly names eight threats:

1. Memory poisoning
2. Tool misuse
3. Privilege compromise
4. Resource overload
5. Cascading hallucination attacks
6. Identity spoofing (and impersonation)
7. Misaligned goals (intent breaking / goal manipulation / misaligned & deceptive behaviours)
8. Human-in-the-loop bypass (overwhelming human in the loop / human manipulation)

The current OWASP paper also names threats that this module treats as *load-bearing but adjacent*: repudiation & untraceability, unexpected RCE and code attacks, agent communication poisoning, rogue agents in multi-agent systems, human attacks on multi-agent systems. All of them show up in the surface × threat matrix below; the eight above are the load-bearing minimum this module's ATMD row list must cover. <!-- needs-research: confirm the OWASP paper's complete current threat list and IDs. -->

## Surface × threat matrix

Every cell says: *given this surface, does this threat land here, and by what mechanism?*

| Threat ↓ / Surface → | Data-input | Tool-invocation | Memory / VS | Environment | HITL | Cross-agent |
|---|---|---|---|---|---|---|
| Memory poisoning | Injection that causes a memory write | Tool call that writes a poisoned document | ✅ primary — direct write to memory / RAG index / embedding store | Rendering the poisoned memory into a later observation | Feedback-signal poisoning (thumbs-up on a bad memory) | Peer-agent writes a poisoned memory this agent inherits |
| Tool misuse | Injection that names a tool | ✅ primary — attacker-controlled arguments | — | Env observation names a tool the model shouldn't call | HITL approver misled about what the tool does | Peer agent invokes a tool on this agent's behalf |
| Privilege compromise | Injection that elicits a privileged tool | Tool called with over-broad credential | Persistent priv-esc via poisoned config in memory | Env content that suggests a false authority | Insider using approval as escalation | Sub-agent inherits over-broad credentials |
| Resource overload | Prompt loops, giant retrievals | ✅ primary — tool-call storms, sub-agent explosion | Vector-store fill attacks | Env pages triggering runaway retrieval | Approval floods | Sub-agent forks that consume compute |
| Cascading hallucination | Injected false context that propagates | Tool call based on hallucination affects a downstream tool | Bad summary written to memory, cited by later runs | Env source that agent treats as authoritative | HITL rubber-stamps a chain of hallucinated citations | Peer agents amplify each other's hallucinations |
| Identity spoofing / impersonation | Injected impersonation ("system:", "admin:") | Tool that returns spoofed authority | Memory with spoofed provenance | Env content with a forged sender | HITL approver spoofed | ✅ primary — peer agent impersonation, rogue registration |
| Misaligned goals / intent breaking | Injection that redirects the goal | Tool sequence that satisfies letter but not intent | Poisoned instructions in long-term memory | Env sub-goal supplant | Approver misled about the *goal* being pursued | Peer agent nudges toward misaligned sub-goal |
| Human-in-the-loop bypass | Injection crafts approval-fatiguing traffic | Tool that shrinks the diff shown to the approver | Memory that pre-legitimises a suspect action | Env noise designed to burn approver attention | ✅ primary — approval bombing, timeout defaults, spoofing | Peer agent auto-approves on behalf of a human proxy |
| Repudiation & untraceability | — | Tool without audit log | Memory writes without provenance | — | HITL approval without immutable log | Cross-agent call without principal capture |
| Unexpected RCE / code attacks | Injection carrying code | ✅ primary — code interpreter, shell tool | Persisted payload triggering RCE on read | Env content that becomes the payload | HITL rubber-stamps the malicious diff | Peer agent runs code on this agent's behalf |
| Agent communication poisoning | — | Tool responses forwarded to peers | Shared memory poisoned across agents | — | — | ✅ primary — peer-message payload |
| Rogue agents in multi-agent systems | — | Rogue agent registers as a tool | Rogue agent's memory pollutes shared index | — | — | ✅ primary — rogue registration in discovery |
| Human attacks on multi-agent systems | Injected attacker instructions to a specific agent | Attacker uses an agent as a tool laundering vector | Poisoning shared memory | — | Social engineering across many approvers | Attacker spoofs peer agent |

Every ATMD row in exercise 02 lives in one or more cells of this matrix. A row that does not fit any cell is either (a) not agent-specific and belongs to the level-25 prerequisite's general harm catalogue, or (b) a new class of threat that should be flagged for OWASP-project contribution.

## Threat-by-threat notes

### T — Memory poisoning

- **Mechanism.** Attacker causes a write to persistent state that biases or hijacks future turns. Writes can be direct (a tool that writes a note) or laundered through a benign-looking write (a "summary of this conversation").
- **Where it hides.** Shared RAG indices across tenants; per-user memories that are read cross-tenant on shared summarisation; feedback-signal-driven fine-tuning pipelines.
- **Mitigation stubs.** Provenance labels on every memory write; per-tenant isolation; canary-probe detection on retrieval; suspicious-write anomaly detection; retention limits; write-approval gates.
- **Owner mapping (chapter 09).** Data + operator.

### T — Tool misuse

- **Mechanism.** Attacker's payload causes a legitimate tool to be called with arguments that produce harm — outside its intended use, at unintended scale, on unintended targets.
- **Where it hides.** Tools accepting free-form URLs, free-form recipients, free-form SQL, free-form arithmetic on money. Any argument the model composes from attacker-controlled text.
- **Mitigation stubs.** Argument validators (types, size, URL allow-lists), side-effect scopes (per-tenant only), rate limits, blast-radius caps, semantic-diff approvals for high-stakes calls.
- **Owner mapping.** Tools + operator.

### T — Privilege compromise

- **Mechanism.** Attacker causes the agent to execute with authority it should not have — sub-agent inherits parent credentials, tool uses operator credentials on behalf of an unauthenticated user, memory-planted config elevates the agent's role.
- **Where it hides.** Credential-inheritance defaults; system-prompt configuration that is not versioned or reviewed; MCP servers that broker credentials across trust boundaries.
- **Mitigation stubs.** Principle-of-least-authority per tool call, per-agent, per-user; credential vending with short lifetimes; explicit dual-approval on privilege changes.
- **Owner mapping.** Operator + integration.

### T — Resource overload

- **Mechanism.** Attacker causes the agent (or sub-agents) to consume runaway compute, tokens, tool budget, or human-approver attention.
- **Where it hides.** Sub-agent-spawn without depth or breadth limits; retrieval loops with attacker-influenced queries; approval channels without rate limits.
- **Mitigation stubs.** Explicit action budgets per session; sub-agent depth/breadth caps; retrieval budget caps; approver rate limits with fair-scheduling.
- **Owner mapping.** Operator + orchestration.

### T — Cascading hallucination attacks

- **Mechanism.** A hallucinated fact propagates through the agent's own tools (writing a summary, citing the summary later, tool-calling on the summary) or through peer agents, and becomes *load-bearing evidence* for a harmful action.
- **Where it hides.** Multi-hop retrieval; long-horizon plans that reuse earlier tool outputs; sub-agent chains that summarise their parent's context.
- **Mitigation stubs.** Provenance labels on every derived fact; deference to first-order sources; hallucination-detection classifiers on tool inputs; independent verification loops.
- **Owner mapping.** Model + integration.

### T — Identity spoofing & impersonation

- **Mechanism.** Attacker impersonates a trusted role — system, admin, tool, peer agent, approver — either through prompt-side impersonation (`SYSTEM:` prefix in retrieved content) or through cross-agent identity claims.
- **Where it hides.** System-prompt boundary rendering; peer-agent messages without cryptographic identity; MCP-server registration without verified identity.
- **Mitigation stubs.** Structured boundaries between roles (StruQ, spotlighting); signed peer-identity claims; explicit disallowed impersonation classifiers; principal-capture in every audit log.
- **Owner mapping.** Model + integration.

### T — Misaligned goals / intent breaking / goal manipulation

- **Mechanism.** Attacker (or attacker-adjacent context) redirects the agent's active goal — replacing it, injecting a sub-goal, or manipulating the objective the agent thinks it is optimising.
- **Where it hides.** Long-horizon plans (goal drift across steps); planner-executor architectures where the planner reads attacker-controlled context; sub-goal decomposition that inherits injected sub-goals.
- **Mitigation stubs.** Goal-anchoring (explicit goal preservation across steps); planner-side goal-drift detectors; frequent human-visible goal reflection; hard sub-goal allow-lists.
- **Owner mapping.** Model + orchestration. Deep coverage lives in mod-110 (deception / alignment-faking / scheming).

### T — Human-in-the-loop bypass / overwhelming HITL / human manipulation

- **Mechanism.** The HITL check is defeated by fatigue (approval bombing), by presentation manipulation (misleading diff), by social engineering (spoofed escalation), or by timeout-default exploitation (approver away → auto-approve).
- **Where it hides.** UX design choices that show summaries instead of arguments; timeout defaults that fail open; single-approver dependencies; unlimited approval queue.
- **Mitigation stubs.** Show canonical arguments not summaries; fail-closed on timeout; dual-approval on high-stakes actions; batched approvals with headroom detection; queue-fatigue budgets.
- **Owner mapping.** Operator + product. Mod-107 engineers containment; mod-108 detection.

## How to use this overlay in the ATMD

For every surface × threat cell (chapter 02 × this chapter), do one of three things:

1. **Populate.** Write a concrete threat description scoped to *this* deployment, cite the OWASP threat ID and version, name the applicable persona tiers (chapter 03), cite evidence (chapter 04), and stub the mitigation with a reference to the module that engineers it (mod-107, mod-108, mod-111).
2. **Justify exclusion.** Some cells are *not applicable* for a specific deployment — e.g., "cross-agent surface × identity spoofing does not apply because we do not expose any cross-agent surface." Write the justification and cite the architecture assumption it relies on.
3. **Flag research.** Some cells will have no known analogue in your deployment but a plausible threat model. Leave the cell with a `<!-- needs-research: ... -->` marker so the reviewer sees the gap.

**Rule:** never leave a cell empty without one of the three above. Empty cells look like completeness; they are actually blind spots.

## Handoff to mitigation modules

The threats named here are the *inputs* to later modules:

- **mod-107 (Excessive-Agency Containment)** — engineers containment against tool misuse, privilege compromise, resource overload, HITL bypass, and unexpected RCE.
- **mod-108 (Frontier-Scale Guardrails)** — engineers detection/prevention for prompt-injected impersonation, memory-poisoning canaries, hallucination-cascade filters.
- **mod-110 (AI Control and Adversarial Alignment Eval)** — evaluates the misaligned-goals / intent-breaking threats at frontier depth.
- **mod-111 (Automated Red-Team)** — exercises every one of these threats through automated fuzzers and LLM-vs-LLM attacker loops.

Do not attempt to engineer mitigations in the ATMD. The ATMD's job is to name the threats, cite the sources, route to the module that owns the mitigation, and record the coverage.

## Common misreadings to avoid

- **"OWASP already lists the threats — we can skip surface mapping."** No. Surface mapping is what turns the OWASP checklist into a *coverage matrix* for your deployment. Without it, you cannot show that every surface has been checked against every threat.
- **"If OWASP doesn't list it, it isn't a real threat."** OWASP is consensus, not exhaustive. When your surface × threat mapping reveals a threat OWASP does not name, keep it and flag it for future OWASP contribution.
- **"Memory poisoning is a training-data problem."** Only partly. Agent-side memory poisoning targets *inference-time* persistent state (RAG index, per-user memory, embedding store), not the base-model training data. The mitigation surface is entirely different from training-data hygiene.
- **"Identity spoofing is out of scope — we don't do multi-agent."** Identity spoofing also applies to the *tool* and *system-prompt* layer within a single agent. Do not skip.

## Summary

- The OWASP GenAI project's "Agentic AI — Threats and Mitigations" is the practitioner-consensus threat list; version-pin it in your citation.
- Cross-map every OWASP threat against every one of the six surfaces (chapter 02) in a surface × threat matrix.
- The eight learning-objective threats — memory poisoning, tool misuse, privilege compromise, resource overload, cascading hallucination, identity spoofing, misaligned goals, HITL bypass — are the minimum load-bearing set.
- Every cell is either populated, justified-as-excluded, or flagged for research. No silent gaps.
- The ATMD names threats; downstream modules engineer mitigations.
