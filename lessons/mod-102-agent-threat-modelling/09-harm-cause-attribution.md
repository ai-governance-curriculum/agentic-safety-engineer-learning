# 09 — Harm-Cause Attribution and the Routing Contract

## Motivation

A threat model that names threats but not their *cause* leaves every mitigation homeless. If the ATMD row says *"the agent leaks PII through a tool call,"* the row is complete only when it also says *whether* the leak is caused by

- an **operator** choice (the tool was allow-listed with too-broad credentials),
- the **model** (the model chose to include the PII in the tool arguments),
- the **data** (the retrieval corpus surfaced PII into the context in the first place),
- an **integration** (the tool's downstream API sends PII to a third party by default), or
- **multi-agent-emergent** behaviour (agent A's summary of PII was accepted as sanitised by agent B).

Each cause routes to a different owner: product engineering, model provider, retrieval / data platform, tools team, orchestration. Attribution is the mechanism by which the mitigation contract lands on the right desk. Without it the ATMD row is a bug report addressed to nobody.

This chapter names the five cause categories, the routing contract for each, and the common attribution mistakes that misroute mitigations.

## The five cause categories

### Operator-caused

**Definition.** Harm caused by a choice the operator (the team deploying the agent) made — tool selection, credential scope, HITL policy, prompt design, guardrail configuration, tier assignment, deployment surface.

**Diagnostic.** *"If we had configured this differently, the harm would not have occurred, and the difference is a knob we own."*

**Owner routing.** Product engineering, platform-security peer, agent-orchestration owner. The `senior-agentic-ai-engineer` peer often owns the architectural knob; this role owns the threat-model finding that surfaces it.

**Examples.**

- Tool allow-list includes a broad-scope tool the deployment does not need.
- HITL default is fail-open on timeout.
- System prompt embedded a secret.
- Deployment tier (chapter 10) was set below the risk envelope.
- Guardrail policy was inherited without adaptation to the deployment.

**Fix modules.** Mod-107 (excessive-agency containment), mod-108 (guardrails), mod-112 (program).

### Model-caused

**Definition.** Harm caused by a behaviour of the model itself — hallucination, refusal-erosion, sandbagging, deception, misaligned goal pursuit, generalisation from training data — that no operator-side configuration could have prevented at inference time.

**Diagnostic.** *"Even with the correct operator configuration, the model itself would have caused this."*

**Owner routing.** Model provider (frontier lab), safety-tuning team (peer `fine-tuning-engineer`, level 30), model-eval / release-assurance (peer `ai-evaluation-engineer`, level 35). This role owns the finding and produces the elicitation evidence; the fix belongs to the model-side stack.

**Examples.**

- The model generates a false citation the agent then uses as an argument.
- The model refuses under a benign framing but complies under a role-play framing.
- The model exhibits alignment-faking behaviour under monitored vs. unmonitored conditions.
- The model pursues an inferred sub-goal that the user did not authorise (misaligned goal — chapter 05, T7).
- The model consistently under-samples a safety consideration in tool arguments.

**Fix modules.** Mod-108 (guard against at inference), mod-110 (control / alignment evaluation), and cross-org handoff to the model provider or safety-tuning peer.

### Data-caused

**Definition.** Harm caused by the state of a data source the agent depends on — retrieval corpus, embedding store, memory, environment content — where the data itself is corrupted, biased, poisoned, stale, or out of policy.

**Diagnostic.** *"Even with a well-configured operator and a well-behaved model, if the data source were clean, this harm would not have occurred."*

**Owner routing.** Retrieval / data platform team, data-governance function. The `ai-risk-engineer` prerequisite owns much of the general data-governance craft.

**Examples.**

- RAG index contains poisoned documents (chapter 05, T1).
- Embedding store has near-duplicate leakage across tenants (OWASP LLM08).
- Environment observation (browser DOM) contains attacker-planted content.
- Feedback signal used for fine-tuning was gamed by attacker users.
- Corpus has stale policy that contradicts current legal requirements.

**Fix modules.** Data-platform side controls; provenance labelling; per-tenant isolation; canary probes.

### Integration-caused

**Definition.** Harm caused by the interface between the agent and an external system — a tool's downstream API, a third-party service, an authentication broker, an MCP server, a browser sandbox, an approval channel.

**Diagnostic.** *"The agent behaved as designed, the model behaved as expected, the data was clean, but the tool / integration surface introduced the harm."*

**Owner routing.** Tools team; API-integration owner; third-party vendor. `security` / `ai-infra-security` peer (level 35) often owns platform-scale integration hygiene.

**Examples.**

- Tool's downstream API sends a copy of the request to a partner service by default.
- MCP server aggregates requests from multiple agents and cross-mixes them.
- Approval channel does not preserve principal identity across escalation.
- Code interpreter's sandbox permits an unexpected egress route.
- Third-party OAuth grant has broader scope than the agent needs.

**Fix modules.** Mod-107 (containment at the tool boundary), mod-108 (validators at the integration boundary), platform-security peer handoff.

### Multi-agent-emergent

**Definition.** Harm that appears only in the interaction between agents, that no individual agent alone would have caused. Failure modes include cross-agent injection propagation, cascading hallucination across the agent graph, principal confusion, deadlock / livelock, rogue-agent registration, and coordinated goal drift.

**Diagnostic.** *"Every agent in isolation was correct. The graph as a whole caused the harm."*

**Owner routing.** Orchestration / agent-platform team, jointly owned by this role (threat-model side) and the `senior-agentic-ai-engineer` peer (architecture side). Multi-agent-emergent harms are often the last-diagnosed and the least owned; deliberately name them.

**Examples.**

- Agent A produces a summary containing a hallucinated fact; agent B treats the summary as authoritative and acts on it.
- Agent A's memory is inherited by sub-agent B, which then acts on injected content that agent A already filtered out.
- Peer-agent identity spoofing: agent A trusts a message claiming to be from agent B, which was actually from agent C.
- Sub-agent explosion: one prompt injection triggers recursive sub-agent creation until resource limits abort.
- Coordinated goal drift: agents A and B each nudge sub-goals toward a shared misalignment vector.

**Fix modules.** Mod-105 (agent-specific attack surface engineering — multi-agent coordination), mod-107 (containment on sub-agent spawn), mod-110 (control-eval on multi-agent settings), architecture-side redesign with the peer.

## Attribution is often mixed — the primary-cause / contributing-cause pattern

Real harms often have multiple causes. The pattern used in incident reporting (adapted from safety-engineering practice more generally):

- **Primary cause.** The cause whose removal would most-likely have prevented the harm.
- **Contributing causes.** Other causes that made the harm more likely, more severe, or less detectable.

Route the mitigation to the primary-cause owner and document the contributing causes with cross-references to their owners. Do not attempt to route to *all* contributing owners; the routing contract loses its point if every mitigation is owned by five people.

An ATMD row template:

```yaml
harm: pii-leak-through-email-tool
primary_cause:
  category: operator
  owner: product-eng@acme
  justification: |
    The email tool was allow-listed with a credential that could send
    to any recipient. Restricting the credential to intra-tenant
    recipients would have prevented the leak.
contributing_causes:
  - category: model
    owner: model-provider
    justification: |
      The model chose to include the PII in the email body despite
      the system prompt's instruction not to.
  - category: data
    owner: data-platform@acme
    justification: |
      The retrieval corpus surfaced the PII in a document that was
      supposed to be redacted.
mitigation_refs:
  - mod-107: tighten email-tool credential scope
  - mod-108: add PII classifier at tool-input
  - data-platform: audit redaction pipeline
```

## Cause × surface heat map

Which cause categories cluster on which surfaces? Approximate:

| Surface | Operator | Model | Data | Integration | Multi-agent |
|---|---|---|---|---|---|
| Data-input | Medium | High | High | Low | Medium |
| Tool-invocation | High | Medium | Low | High | Medium |
| Memory / VS | Medium | Low | High | Low | High |
| Environment | Medium | Low | High | Medium | Low |
| HITL | High | Low | Low | Medium | Low |
| Cross-agent | Medium | Low | Low | High | High |

Use the heat map as a *sanity check* — if your ATMD attributes every memory-poisoning row to operator-cause, something is wrong (memory poisoning is dominantly data-caused). If your ATMD attributes every cross-agent row to operator-cause, something is wrong (cross-agent harms are dominantly integration- or multi-agent-caused).

## The routing contract as an ATMD field

Every ATMD row carries a `harm_cause_routing` field. The routing contract as a schema:

```yaml
harm_cause_routing:
  primary_cause: operator | model | data | integration | multi_agent_emergent
  primary_owner: <team | role | vendor>
  contributing_causes:
    - {category, owner, justification}
  handoff_artefact: <link to the interface artefact this row hands to the owner>
  escalation_path: <who to escalate to if the owner disputes attribution>
```

The `handoff_artefact` is the concrete document the primary owner receives — a data-platform ticket for a data-cause row, a model-provider incident report for a model-cause row, a product-eng story for an operator-cause row. Without a handoff artefact the routing is aspirational.

## Common attribution mistakes

- **Blaming the model when the operator is the cause.** A tool that has too-broad credentials produces harm the moment the model uses the credential as designed. The model did not misbehave; the operator over-scoped.
- **Blaming the operator when the data is the cause.** A well-configured RAG-backed agent produces harm when the corpus contains poisoned documents. The operator did what they should have; the data pipeline let the poison through.
- **Blaming everyone.** "All five categories contributed" is the anti-pattern that dilutes ownership. Pick a primary; document contributors.
- **Failing to name multi-agent-emergent when it applies.** Multi-agent-emergent is often the primary cause and is the category least likely to be named unless the ATMD forces it as an explicit choice.
- **Attributing to model-cause without elicitation evidence.** If you claim the model would have caused this harm regardless of operator configuration, you need elicitation-gap-accounted evidence (mod-101, mod-106) to back the claim.

## Handoff to `ai-risk-engineer` (prerequisite)

The level-25 prerequisite owns the **general** harm-attribution craft (STRIDE / LINDDUN framings, model-risk lifecycle attribution). This chapter adds the **five agent-specific categories** and the routing contract. When you author an ATMD row's routing contract, cite the prerequisite for the underlying attribution discipline and add the agent-specific delta.

## Handoff to `senior-agentic-ai-engineer` (peer)

The peer often owns *most operator-caused* and *most multi-agent-emergent* mitigations, because those cluster on the architecture side (tool inventory, credential scoping, sub-agent topology). The routing contract is your way of handing the peer a punch list of ATMD rows whose primary cause is architecture. The interface artefact is the ATMD's `handoff_artefact` link; the peer's architecture spec is the reciprocal artefact.

## Common misreadings to avoid

- **"The cause is whoever the SOC would blame."** Attribution is technical, not political. Blame is an incident-response artefact; cause is a design artefact. Do not mix them.
- **"Model-cause rows should all be sent to the model provider."** Some model-cause rows have operator-side mitigations (guardrail, classifier, argument-validator). Route to the fix-owner, not the blame-owner.
- **"Multi-agent-emergent is a small niche."** For any deployment that spawns sub-agents or accepts peer-agent messages, multi-agent-emergent is one of the load-bearing cause categories. Do not deprioritise it because it is harder to write.
- **"Data-caused harms are always the data-platform team's problem."** Data-caused harms also have model-side mitigations (retrieval-provenance-required, canary probes). Route to the fix-owner, not just the source-owner.

## Summary

- Five cause categories: **operator, model, data, integration, multi-agent-emergent**. Each routes to a different owner.
- Every ATMD row carries a `harm_cause_routing` field naming the primary cause, the primary owner, contributing causes, the handoff artefact, and the escalation path.
- Attribution is often mixed; pick a primary cause using the "which removal would most-likely have prevented the harm" test.
- The routing contract is what makes the ATMD *actionable* — without it the threat model is a bug report to nobody.
- Multi-agent-emergent is under-attributed by default; force it as an explicit choice.
