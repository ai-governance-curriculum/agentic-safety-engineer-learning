# 10 — Enterprise Agent-Deployment Tiering and Role Coordination

## Motivation

Mod-101 read the frontier-lab safety frameworks — Anthropic RSP, OpenAI Preparedness, Google DeepMind FSF — as *frameworks that tier the model*. The engineering shape they share (capability → threshold → elicitation → tripwire → mitigation → rollback → review → evidence → disclosure) is *portable*. The same shape, applied downstream, gives you an enterprise-agent tiering framework: not "how dangerous is the model?" but "how dangerous is the *deployment*?"

An internal read-only Q&A agent and a customer-facing agent that transacts on behalf of end-users live in profoundly different tiers. If both are subject to the same threat-model rigor, one is under-served and one is over-served — and neither is defensible. Enterprise-agent tiering fixes that. It is the last artefact this module produces; every prior chapter feeds into it.

The chapter closes with the two role-coordination handoffs the learning objectives name: **`ai-risk-engineer`** (prerequisite, level 25) and **`senior-agentic-ai-engineer`** (peer, agentic AI family).

## Reusing the RSP / Preparedness / FSF shape

From mod-101 chapter 05, the common shape:

```
capability → threshold → elicitation → tripwire → mitigation
        → rollback → review → evidence → disclosure
```

Applied to an enterprise agent deployment, the analogue is:

```
deployment_capability → tier_threshold → threat-model coverage
        → tripwire → deployment mitigation → rollback contract
        → review body → ATMD + eval evidence → internal disclosure
```

- **Deployment capability** is what the agent can do: which tools, which credentials, which environments, which peer agents.
- **Tier threshold** names the level of concern the deployment is at.
- **Threat-model coverage** (this module) is the elicitation-analogue: how deeply the ATMD was authored.
- **Tripwire** is the operator-side signal that a deployment is drifting out of tier.
- **Deployment mitigation** is the containment / guardrail / control contract (mod-107, mod-108).
- **Rollback contract** is what "roll back" concretely means: revert to previous tier, revoke tools, revert HITL policy, take offline.
- **Review body** is the internal review authority (deployment safety board, security review, product review).
- **Evidence** is the ATMD plus supporting evaluations.
- **Disclosure** is internal (rarely external, unless the deployment is regulated by an external framework — see mod-112).

## A four-tier deployment-risk scheme

The number of tiers you use is a house choice. A minimum-viable four-tier scheme:

### Tier 0 — Non-agent baseline

- **What it is.** A chat app or classical LLM deployment with no tool-invocation, no persistent memory beyond the session, no environment observation beyond text input, no HITL beyond human-in-loop editing, no cross-agent.
- **What the level-25 prerequisite already covers.** All of it. This role does not add a new ATMD for Tier 0; it inherits the prerequisite's harm catalogue.
- **Threat-model coverage required.** The prerequisite's general harm catalogue plus the OWASP LLM Top 10.

### Tier 1 — Internal read-only assistant

- **What it is.** Read-only tools inside an internal boundary — knowledge-base search, internal wiki lookup, ticket read, code search. No writes, no external egress, no cross-tenant reach. Session-scoped memory only.
- **Personas in scope.** Tier 1 (script-kiddie via prompt), Tier 2 (curious power-user), Tier 3 (insider by proximity).
- **Threat-model coverage required.** ATMD across data-input, tool-invocation (read-only tools only), and HITL surfaces. Memory / VS empty or scoped to per-user with retention limits. Environment scoped to internal sources. Cross-agent excluded with citation.
- **Deployment mitigation minimum.** Baseline guardrails; per-tool argument validators for query-strings; system-prompt versioning; audit log immutable.
- **Rollback contract.** Take offline; revert to previous system prompt version; revoke read-only tool credential.
- **Review body.** Product review + platform-security peer.

### Tier 2 — Internal write-authorised or customer-facing read-only agent

- **What it is.** Either (a) internal agent with write authority to bounded systems (files, tickets, PRs, calendar) or (b) customer-facing read-only agent (product Q&A, documentation search). Some persistent memory. Some environment observation.
- **Personas in scope.** Tier 1–4, with Tier 4 attention limited by exposure.
- **Threat-model coverage required.** Full ATMD across all six surfaces. All four overlays populated: OWASP Agentic, OWASP LLM Top 10, MITRE ATLAS, Google SAIF. Harm-cause routing for every row.
- **Deployment mitigation minimum.** Argument validators on every write tool; HITL on any high-stakes write; memory provenance; environment-fetch allow-lists; automated red-team baseline (mod-111).
- **Rollback contract.** Take offline; revert or roll back memory / RAG index writes; revoke write credentials; re-tier as Tier 1.
- **Review body.** Product review + platform-security peer + deployment-safety board (or equivalent internal function).

### Tier 3 — Customer-facing transactional agent

- **What it is.** External-facing agent with authority over financial, contractual, or safety-critical outcomes — refunds, orders, appointments, tickets, health advice, legal-adjacent summaries. Persistent memory. Environment observation. Cross-agent surface may be exposed.
- **Personas in scope.** All six tiers, with Tier 4 (cybercrime affiliate) as a first-class threat and Tier 5 (targeted) as a real concern.
- **Threat-model coverage required.** Everything in Tier 2 plus:
  - Explicit fraud model with the fraud function's sign-off.
  - Cross-tenant memory / VS isolation with red-team probes.
  - HITL bypass evaluation (approval-bomb, fatigue, spoof).
  - Multi-agent-emergent harms catalogue (chapter 09) even for single-agent deployments if the deployment plans peer-agent surface.
  - Elicitation-gap-accounted evaluation for tool misuse and privilege compromise.
- **Deployment mitigation minimum.** Everything in Tier 2 plus:
  - Blast-radius caps on every irreversible tool.
  - Dual-approval on high-value transactions.
  - Real-time SIEM integration for tool-call anomalies.
  - Kill-switch with in-flight revocation.
  - Adaptive-attack survival curves for guardrails (mod-108).
- **Rollback contract.** Tier 2's plus: unwind or compensate the last-N transactions; publicly disclosed pause; regulator notification if under EU AI Act Article 73 scope (mod-112).
- **Review body.** Product + security + safety board + risk + legal.

### Tier 4 — Frontier-adjacent or dangerous-capability-adjacent agent

- **What it is.** An agent whose capability envelope touches the RSP / Preparedness / FSF concern space — meaningful autonomy on ML R&D tasks, cyber-offense capability, CBRN uplift adjacency, high-persuasion capability. Rare in an enterprise setting; common in a frontier-lab setting.
- **Personas in scope.** All six, including Tier 6 (nation-state uplift) as a first-class threat.
- **Threat-model coverage required.** Everything in Tier 3 plus:
  - Explicit RSP / Preparedness / FSF-shape evaluation coverage (mod-106).
  - Deception / alignment-faking / sandbagging / sleeper-agent evaluations (mod-110).
  - Safety-case authoring (mod-109).
- **Deployment mitigation minimum.** Everything in Tier 3 plus the safeguard set the relevant frontier framework requires at the analogous tier.
- **Rollback contract.** Tier 3's plus: escalate to Responsible-Scaling Officer / Preparedness team / FSF review; consider public disclosure to AISI or regulator.
- **Review body.** Enterprise safety board plus frontier-safety program (mod-112) plus, if applicable, external AISI window.

### Tier assignment as a pre-registration

Choose the tier before the ATMD is authored. Moving a tier down after the ATMD reveals uncomfortable findings is the failure mode — the same anti-pattern as post-hoc threshold moving in the RSP / Preparedness / FSF frameworks. Pre-registration means:

- The tier is chosen in writing before the ATMD begins.
- Moving down requires a documented justification and a review-body sign-off.
- Moving up requires re-work of the ATMD but no sign-off (up is always defensible).

## Tripwires for tier drift

Tier drift is what happens when a deployment slowly acquires capability it did not have at review time — a new tool is added, a memory becomes cross-tenant, a peer-agent surface is exposed. Every tier carries a tripwire set:

- **New tool added?** Re-tier or restrict.
- **Memory scope broadens (per-session → per-user → shared)?** Re-tier.
- **Environment-observation source expands (internal-only → open web)?** Re-tier.
- **HITL rate exceeds fatigue threshold?** Escalate to review body.
- **Peer-agent surface added?** Re-tier (single-agent → multi-agent is a tier boundary).

Tripwires belong in the ATMD and are wired into the deployment's monitoring (mod-108).

## Coordination with `ai-risk-engineer` (prerequisite, level 25)

The prerequisite role owns the *general* engineering craft of AI risk at team scope. This role owns the *frontier-agent depth* at organisation scope. The coordination artefacts:

- **What this role receives from the prerequisite.**
  - The general harm catalogue authored against AI Incident Database, OECD.AI, MIT AI Risk Repository.
  - The NIST AI RMF risk register and the map / measure / manage cycle.
  - The OWASP ML Top 10 coverage at general depth.
  - Fairness, privacy, and explainability craft that applies to the model underneath the agent.
  - The team-scope cross-functional program that owns the risk register cadence.
- **What this role hands back to the prerequisite.**
  - The ATMD as a specific, agent-scoped extension of the general harm catalogue. The prerequisite consumes the ATMD into the register.
  - The harm-cause routing (chapter 09) for rows whose primary cause is data or general operator hygiene — those often route to the prerequisite for platform-level fixes.
  - Deployment-tier assignment as an input to the register's tiering discipline.
- **The interface artefact.** A single link from the prerequisite's harm catalogue to this role's ATMD, and a reciprocal link back. The two artefacts version together.

### What not to do

- Do not re-teach the prerequisite's craft inside the ATMD. Cite it.
- Do not re-author the general harm catalogue. Reference it.
- Do not remove rows from the general harm catalogue that this role's ATMD extends. Leave both; they serve different audiences.

## Coordination with `senior-agentic-ai-engineer` (peer, agentic AI family)

The peer owns the *agent architecture* being threat-modelled: tool inventory, memory model, HITL topology, sub-agent design, orchestration policy, integration points, prompt architecture. This role owns the *threat model* over that architecture. The two roles must move in lockstep; a threat model authored without the architecture spec is guesswork, and an architecture spec authored without a threat model is a bug factory.

- **What this role receives from the peer.**
  - The agent architecture spec (data flow, tool inventory, memory model, HITL points, sub-agent topology).
  - The deployment plan (tier assumption, personas expected, business process context).
  - The change-log of architectural updates that require re-threat-modelling.
- **What this role hands back to the peer.**
  - The ATMD, structured to point at the architecture spec's sections.
  - A punch list of operator-caused and multi-agent-emergent findings the peer's architecture-side team owns (chapter 09).
  - Tier drift tripwires the peer must instrument.
  - The `handoff_artefact` links in every ATMD row that names an architectural fix.
- **The interface artefact.** A jointly versioned pair: architecture spec + ATMD, with reciprocal cross-links. The pair versions together; a change to either triggers a review of the other.

### Boundary rule

The peer decides *what the agent does*; this role decides *what could go wrong*. Both roles have influence on both, but the ownership is clear: architectural choices route to the peer, threat-model findings route to this role.

## Coordination with the broader ladder

Beyond the two learning-objective handoffs, the tiering framework informs the rest of the ladder:

- **`ai-evaluation-engineer` (peer / next-up, level 35, Governance).** Consumes the tier assignment plus the ATMD as inputs to release-assurance packaging. If a deployment enters Tier 3 or above, expect a release-assurance file with cross-linked artefacts.
- **`senior-ai-governance-architect` (level 50).** Reconciles the tiering scheme across the enterprise's control library and cross-jurisdiction regulatory shape (EU AI Act, etc.).
- **`head-of-ai-governance` (level 60).** Owns the tier-review body's cadence and board-facing rollup.
- **`security` / `ai-infra-security` (level 35).** Consumes the ATMD's ATLAS + SAIF blocks for platform-scale controls.

Mod-112 (Frontier-Safety Program, Serious-Incident Response, and Disclosure) is where the tiering rolls up into the organisation-scope program.

## A deployment-tier ATMD field

The tiering fact belongs in the ATMD skeleton (chapter 01) as its own field:

```yaml
deployment_tier:
  tier: T2
  pre_registered_on: 2026-06-15   # example
  pre_registered_by: <safety board minutes ref>
  justification: |
    Tier 2 selected because the agent has write authority to internal
    ticket system (bounded) but no external transactional authority.
    Memory is per-user with 30-day retention. Environment observation
    limited to internal sources. Cross-agent surface not exposed.
  tripwires:
    - "New tool added → re-tier review"
    - "Memory scope broadens → re-tier review"
    - "Cross-agent surface added → re-tier review to T3"
  rollback_contract:
    revert_to: T1 (read-only)
    revoke_credentials: [ticket-write-cred]
    unwind_writes: false   # writes are audit-only, no unwind path
```

Exercise 01 asks you to author this field for a specific agent; exercises 02 and 04 exercise the tripwires and the multi-agent-emergent coverage that tier-3-and-up demand.

## Common misreadings to avoid

- **"Tiering is a governance concern; engineers don't tier."** The engineering choices *are* the tier: which tools, which credentials, which HITL points, which peer agents. Tiering is fundamentally an engineering artefact reviewed by a governance body.
- **"Tier 4 is only for frontier labs."** Some enterprise deployments (dual-use agents, high-persuasion agents in politically sensitive settings, autonomous coding agents at scale) approach Tier 4 in specific dimensions. Do not assume the enterprise is safe from the top of the ladder.
- **"We can start at Tier 1 and defer Tier 3 to next quarter."** Deferring the ATMD past deployment is the failure mode. The ATMD is a pre-deployment artefact; if the deployment ships at Tier 3, the Tier 3 ATMD must be in place before it ships.
- **"The peer's architecture spec is sufficient; we don't need our own artefact."** The architecture spec and the ATMD are complementary — the peer's spec describes the design; this role's ATMD describes what could go wrong with it. Both are load-bearing.

## Summary

- Reuse the RSP / Preparedness / FSF shape (mod-101) as the enterprise-agent tiering template: capability → tier threshold → threat-model coverage → tripwire → deployment mitigation → rollback → review → evidence → disclosure.
- Four minimum-viable tiers: T0 (non-agent baseline), T1 (internal read-only), T2 (internal write / customer-facing read-only), T3 (customer-facing transactional), T4 (frontier-adjacent).
- Pre-register the tier before the ATMD; moving down requires review, moving up is always defensible.
- Wire tripwires for tier drift; escalate to re-tier review when the deployment's capability envelope changes.
- Coordinate with the `ai-risk-engineer` prerequisite (general harm catalogue, register cadence) and the `senior-agentic-ai-engineer` peer (architecture spec, tripwire instrumentation); their artefacts version with yours.
