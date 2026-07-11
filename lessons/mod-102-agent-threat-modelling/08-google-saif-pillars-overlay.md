# 08 — Google SAIF Pillars Overlay

## Motivation

**Google Secure AI Framework (SAIF)** is Google's published set of principles for securing AI systems, aimed at operator-side engineers. Where OWASP catalogues *threats* and ATLAS catalogues *techniques*, SAIF catalogues *pillars* — the classes of controls an operator applies to reduce AI-security risk. In the ATMD, SAIF gives you the **control-catalogue** axis that turns "we identified this threat" into "we routed it to this class of control."

If OWASP tells you *what could go wrong*, ATLAS tells you *how it goes wrong*, and SAIF tells you *what control-family fixes it*. All three overlays land on the same six surfaces, and reviewers who come from a Google-partner ecosystem (or from cloud-security backgrounds generally) will read the SAIF layer first.

## Primary source

- **[Google Secure AI Framework (SAIF)](https://safety.google/cybersecurity-advancements/saif/)** — the SAIF hub with the current pillar articulation.
- **[SAIF: A framework for secure AI systems (Google blog, June 2023)](https://blog.google/technology/safety-security/introducing-googles-secure-ai-framework/)** — original announcement and pillar list.
- **[Google Cloud SAIF resources](https://cloud.google.com/security/business/saif)** — enterprise-oriented rendering of SAIF elements as controls.

<!-- needs-research: confirm the current SAIF pillar list, its exact wording, and any Google-published mapping between SAIF pillars and specific product-side controls. Google has iterated the framing; the six pillars below are the current published articulation. -->

## The six SAIF pillars

SAIF is organised around six pillars. As published:

1. **Expand strong security foundations to the AI ecosystem** — reuse enterprise-security foundations (identity, secrets management, data classification, network segmentation, incident response) for AI systems rather than reinventing them.
2. **Extend detection and response to bring AI into an organisation's threat universe** — treat AI-security signal as SIEM / SOC input; wire agent telemetry into detection and response operations.
3. **Automate defences to keep pace with existing and new threats** — attackers automate; defenders automate; specifically, automated red-teaming and automated detection are load-bearing.
4. **Harmonise platform-level controls to ensure consistent security across the organisation** — one control across all AI systems rather than per-app controls; policy-as-code; deployment tiering (chapter 10).
5. **Adapt controls to adjust mitigations and create faster feedback loops for AI deployment** — telemetry → detection → mitigation → deployment update loop; short lifecycle from finding to fix.
6. **Contextualise AI system risks in surrounding business processes** — end-to-end risk, not just the model; include the business process the AI serves, its human operators, its data flows.

<!-- needs-research: verify the current SAIF pillar order, exact wording, and any newer pillar additions or reframings. This chapter uses the published-launch pillar language as its baseline. -->

## Surface × SAIF pillar overlay

Each cell answers: *which SAIF pillar's control-family applies to this surface, and how?*

| Surface ↓ / Pillar → | 1. Foundations | 2. Detection & response | 3. Automate defences | 4. Harmonised platform controls | 5. Adaptive controls | 6. Business-process context |
|---|---|---|---|---|---|---|
| **Data-input** | Identity of the input source; classification of the data on the way in | Prompt-injection signal into SIEM; alerting on injection attempts | Automated input classifiers, spotlighting | One input-filter policy across agents | Fast retrain / re-deploy input classifiers | Which business processes ingest which data |
| **Tool-invocation** | Secret / credential management; egress network policy | Tool-call anomaly detection into SIEM; audit logs immutable | Automated tool-call validators; fuzzing | One tool-policy language across agents | Rapid revoke / re-scope tool credentials | Which tools serve which business processes |
| **Memory / VS** | Data classification of memory writes; per-tenant isolation | Poisoning-detection into SIEM; retrieval anomaly alerts | Automated canary probes; anomaly-detection | One retention / provenance policy for all memories | Fast rotation of embedding namespaces | Which memory serves which business decision |
| **Environment-observation** | Network policy on outbound fetches; sandboxing | Env-observation anomaly signal into SIEM | Automated env-sanitisation; multimodal safety filters | One env-fetch policy across agents | Fast tightening of fetch rules under attack | Which environments correspond to which business processes |
| **Human-in-the-loop** | Identity of approvers; audit trail | Approval-fatigue anomaly detection | Automated escalation, auto-batch de-duplication | One HITL policy across agents | Fast switch of default (fail-open / fail-closed) | Which HITL step maps to which business decision |
| **Cross-agent** | Peer-agent identity; MCP-server registration hygiene | Cross-agent message anomaly detection | Automated peer-agent trust scoring | One cross-agent policy across the fleet | Fast revoke of peer-agent trust | Which peer relationships correspond to business flows |

The matrix is dense on purpose. The SAIF pillars are horizontal; you rarely leave a pillar unused for any surface. What varies across surfaces is the *specific control* the pillar names.

## Pillar-by-pillar notes

### Pillar 1 — Expand strong security foundations to the AI ecosystem

**What it means for agents.** Do not reinvent identity, secrets management, network segmentation, or incident response for AI. Reuse the enterprise-security substrate the level-25 prerequisite and the platform-security peer (level 35) already own.

**In the ATMD.** The row must name the underlying foundation control the enterprise already provides (e.g., "we use Vault for secrets, IAM for identity, VPC for segmentation, SIEM for signal aggregation") and identify the *delta* the agent introduces on top.

**Common failure mode.** Building AI-specific credential stores, AI-specific SIEM lanes, AI-specific identity — introducing parallel infrastructure that never gets the audit rigour of the mainline enterprise security stack.

### Pillar 2 — Extend detection and response to bring AI into an organisation's threat universe

**What it means for agents.** Agent-side signals (prompt-injection attempts, tool-call anomalies, retrieval anomalies, HITL fatigue, peer-agent identity failures) become inputs to the enterprise detection lane, not siloed AI-security dashboards.

**In the ATMD.** For each surface, name the signal that lands in SIEM / SOC and the detection playbook the SOC runs on it. If no signal is emitted, name it as a coverage gap.

**Common failure mode.** Prompt-injection attempts logged only to the agent's own log file, unread by the SOC. The signal exists; it does not reach the responder.

### Pillar 3 — Automate defences to keep pace with existing and new threats

**What it means for agents.** Attackers automate at Tier 2+ (chapter 03). Defenders automate: automated red-teaming (mod-111), automated safety-monitor scoring (mod-108), automated argument-validators and canary-probes.

**In the ATMD.** For each surface, name the *automated* defence and its cadence. Manual-only defences are load-bearing only for insider-tier and above.

**Common failure mode.** A quarterly manual red-team as the only adversarial testing. Between quarters the threat landscape shifts and no automated compensation exists.

### Pillar 4 — Harmonise platform-level controls to ensure consistent security across the organisation

**What it means for agents.** One control across all agents rather than per-agent controls. One prompt-injection policy the fleet inherits. One tool-argument-validation library. One memory-provenance schema. Deployment tiering (chapter 10) is how you harmonise across a heterogeneous fleet without forcing all agents into one tier.

**In the ATMD.** For each surface, name the platform-level control the agent inherits and the per-agent override (if any). Overrides need justification.

**Common failure mode.** Every agent team building their own guardrail. The fleet's guardrail coverage becomes N-shaped rather than platform-shaped, and no team gets the depth a shared library would.

### Pillar 5 — Adapt controls to adjust mitigations and create faster feedback loops for AI deployment

**What it means for agents.** The finding-to-fix loop is short. A jailbreak found on Tuesday lands in the classifier by Friday; a tool-argument-validator gap found by red-team lands in the shared library by end-of-week. The mitigation lifecycle is the axis SAIF names as competitive with attacker automation.

**In the ATMD.** For each surface, name the mitigation lifecycle target — how long from detection to deployed fix.

**Common failure mode.** Six-month release cycles that leave classifier / validator updates lagging attacker capability.

### Pillar 6 — Contextualise AI system risks in surrounding business processes

**What it means for agents.** The risk is end-to-end, not model-only. An agent that refunds up to $500 lives in a business process that includes fraud teams, finance controls, customer-support workflows, and legal review. The ATMD must name the surrounding process for every high-stakes tool.

**In the ATMD.** The `business_process_context` block per surface / per tool: what process this touches, what humans consume the output, what downstream systems accept the tool-call side effects.

**Common failure mode.** Treating the model + tool + prompt as a closed system. Reviewers who own the business process do not see the agent artefact and cannot spot the contextual risks.

## Cross-mapping SAIF pillars to OWASP and ATLAS

| SAIF pillar | OWASP LLM Top 10 correspondents | OWASP Agentic correspondents | ATLAS tactic correspondents |
|---|---|---|---|
| 1. Foundations | LLM02, LLM03, LLM07 | Privilege Compromise | Credential Access, ML Model Access |
| 2. Detection & response | All (as signal source) | Repudiation & Untraceability inverted | Discovery, Impact detection |
| 3. Automate defences | All (as automation target) | All | Defense Evasion counter |
| 4. Harmonised platform controls | LLM06 | All | Persistence prevention, harmonised policies |
| 5. Adaptive controls | LLM01, LLM09 | Cascading Hallucination, Memory Poisoning | ML Attack Staging counter |
| 6. Business-process context | LLM05, LLM06 | All | Impact context |

SAIF is not a threat catalogue — it is a control taxonomy. Its cross-map to OWASP and ATLAS is many-to-many, and the point is not to memorise the correspondence but to make sure every OWASP/ATLAS row is answered by at least one SAIF pillar in the ATMD.

## Handoff to `senior-agentic-ai-engineer` (peer, agentic AI family)

SAIF is the overlay most legible to the `senior-agentic-ai-engineer` peer — the person authoring the agent architecture. The handoff pattern:

- **This role authors** the SAIF × surface matrix with the specific control-family that each cell requires.
- **The senior-agentic-ai-engineer consumes** the matrix into the agent architecture: which foundations the runtime uses, which detection signals it emits, which controls it inherits from the platform layer.
- **This role receives** the architecture spec (data flow, tool inventory, memory model, HITL points, sub-agent topology) that populates the six-surface enumeration in the first place.

The interface artefact is a joint doc: this role's ATMD points at the peer's architecture spec, and the architecture spec references the ATMD's SAIF matrix. Both artefacts version together.

## Where SAIF is *thin*

- **Multi-agent-emergent harms.** SAIF is written for a single-system model. Extending it to agent graphs is an interpretation exercise (chapter 09).
- **Frontier-scale misalignment risks.** SAIF is an operator-side control framework; misalignment risks (mod-110) are model-side and require RSP / Preparedness / FSF-style controls in addition.
- **Business-context specificity.** Pillar 6 is aspirational; SAIF does not provide a template for the business-process-context section. The `ai-risk-engineer` prerequisite's business-context craft fills that gap.

## Common misreadings to avoid

- **"SAIF is a Google-only framework."** It was authored by Google but is publicly published and widely referenced. It maps onto any operator-side AI-security programme.
- **"SAIF is redundant with NIST AI RMF."** They are complementary. NIST AI RMF is a *risk-management framework* (map, measure, manage); SAIF is a *control-family taxonomy*. Use both — the level-25 prerequisite owns NIST AI RMF fluency; this module adds the SAIF overlay.
- **"SAIF pillar 6 is a marketing bullet."** Pillar 6 is the pillar that catches the "end-to-end blast radius" issue that most AI-security programmes miss. Do not skip it.
- **"SAIF doesn't apply because we use another cloud."** SAIF is agnostic. The specific tooling (Google Chronicle, Google Cloud IAM) is what maps to the cloud; the pillars are portable.

## Summary

- Google SAIF is the operator-side control-family taxonomy that complements OWASP (threats) and MITRE ATLAS (techniques).
- Six pillars: strong foundations, detection & response, automated defences, harmonised platform controls, adaptive controls, business-process context.
- Overlay each pillar across the six surfaces (chapter 02); the surface × pillar matrix is what the ATMD populates.
- SAIF is the natural handoff artefact to the `senior-agentic-ai-engineer` peer; both roles version their artefacts together.
- SAIF is thin on multi-agent-emergent risks and on frontier-scale misalignment; augment from chapters 09 and 10.
