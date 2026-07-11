# 03 — Adversary Personas from Script-Kiddie to Nation-State

## Motivation

A threat model without adversary personas defaults to *"someone somewhere might do something bad."* That default is useless — it does not tell you what capability, standing, and intent to design against, and it makes every mitigation look equally valuable.

An explicit persona ladder does two things:

1. It **bounds the threat model at the top** — you are not designing your consumer-facing support agent against a nation-state weight-exfiltration campaign; you are designing it against script-kiddie jailbreaks and insider misuse.
2. It **bounds the threat model at the bottom** — you are not spending elicitation budget on trivial persona reach that a static content classifier already catches.

The persona ladder below is *deliberately not novel*. It borrows from the threat-actor taxonomies that already exist in security engineering (see Google TAG / Microsoft Threat Intelligence threat-actor tiers for the non-AI analogue) and from the frontier-safety framework language for the top of the ladder (RSP / Preparedness / FSF explicitly reason about nation-state uplift-seekers). What is agent-specific here is the *cross-surface* profile of each persona — which of the six surfaces (chapter 02) each persona reaches, and by what means.

## The six persona tiers

### Tier 1 — Script-kiddie jailbreaker

**Who they are.** A user with the standing of a normal end-user, an appetite for pushing the model into rules-forbidden output, and access to public jailbreak collections (Reddit, X, jailbreak wikis, the DAN family of prompts characterised by Shen et al., 2024). No proprietary tooling, no persistent campaign, low patience.

**Capability profile.**

- **Prompt-craft** — copy-paste from public collections; light editing.
- **Persistence** — session-scale, not campaign-scale.
- **Tooling** — none proprietary; browser + copy/paste.
- **Cross-tenant reach** — none directly; can plant content on public pages an agent might retrieve.
- **Compute** — none of note.

**Reachable surfaces (chapter 02).**

- ✅ **Data-input (direct)** — the user prompt.
- ✅ **Data-input (indirect, weak)** — planting hostile content on a public page the agent may retrieve; effective only against agents that retrieve widely.
- ✅ **Human-in-the-loop (weak)** — approval-fatigue via repeated benign-looking submissions.
- ❌ **Tool-invocation direct** — no credentials.
- ❌ **Memory / VS direct** — no privileged write authority.
- ❌ **Environment direct** — cannot influence the agent's private environment.
- ❌ **Cross-agent direct** — no standing in the agent graph.

**Intent.** Curiosity, entertainment, mild grievance, community status. Rarely produces per-instance high-severity harm; produces long-tail volume the operator has to withstand.

**Design implication.** Guardrail baseline (input classifier, output classifier, refusal training) should hold this persona at the door. If your agent is repeatedly falling to Tier 1, the guardrail baseline is broken and mod-108 material applies.

### Tier 2 — Curious power-user / bug-bounty red-teamer

**Who they are.** A technically literate user or independent red-teamer. Reads the OWASP LLM Top 10 and the OWASP Agentic threats, follows the jailbreak literature (GCG, PAIR, TAP, Crescendo), and probes systematically. May be a bug-bounty participant with the legal standing to test.

**Capability profile.**

- **Prompt-craft** — bespoke; understands indirect prompt injection, obfuscation, role-play jailbreaks, many-shot.
- **Persistence** — hours to days on a target; may publish findings.
- **Tooling** — familiar with `garak`, PyRIT, Promptfoo red-team, Inspect at the level-25 prerequisite depth.
- **Cross-tenant reach** — can plant content on second- and third-party pages; may fuzz the agent's tool arguments.
- **Compute** — modest.

**Reachable surfaces.**

- ✅ Data-input (direct + indirect).
- ✅ Environment-observation (via crafted URLs, uploaded documents).
- ✅ Human-in-the-loop (via social engineering; understands rate-limit dynamics).
- ✅ Tool-invocation (indirect, via prompt injection that triggers the tool).
- △ Memory / VS (indirect, if the agent writes attacker-supplied content).
- △ Cross-agent (probes if the surface is exposed publicly).

**Intent.** Discovery, publication, bounty, career signal. Rarely per-instance harmful in intent, but publication generalises the finding to every other persona.

**Design implication.** This is the tier that determines whether your guardrails have *depth*, not just *breadth*. Tier 1 falls to a shallow guardrail; Tier 2 requires adaptive-attack survival curves (mod-108) and elicitation-gap-aware evaluation.

### Tier 3 — Insider

**Who they are.** Someone who legitimately holds credentials that touch the agent's runtime — an operator engineer, a customer-support agent operator, a data engineer with write access to the RAG index, a model provider's employee, a third-party integrator with API keys.

**Capability profile.**

- **Prompt-craft** — variable, but not the primary attack vector.
- **Persistence** — indefinite, until credential revocation.
- **Standing** — legitimate credential; whitelisted IPs; on-network.
- **Tooling** — internal tools they were issued.
- **Cross-tenant reach** — depends on their credential's blast radius.
- **Compute** — the operator's compute.

**Reachable surfaces.**

- ✅ **All six directly.** An insider can write to the system prompt, add tools, alter argument validators, write to memory / RAG index, run tests that observe environment, spoof HITL approvers, and register rogue agents. This is the persona whose *reach exceeds* the guardrail model — mitigations here belong to enterprise access control, monitoring, and separation-of-duty, not to prompt-side defences.

**Intent.** Grievance, ideology, financial gain, coercion, negligence, or curiosity. The AI Incident Database catalogues insider-caused harms at meaningful frequency; do not defer this persona.

**Design implication.** Insider mitigation is *organisational* (least-privilege credentials, dual-approval on production changes, audit-log immutability, monitoring for anomalous edits) and *architectural* (system-prompt versioning under change control, RAG-index write staging). Prompt-side defences do not stop this persona; the ATMD's insider row is where the SOC / SecOps handoff (mod-107, mod-112) is codified.

### Tier 4 — Automated / cybercrime affiliate

**Who they are.** Organised, financially motivated, willing to fund infrastructure. Runs cybercrime affiliate programmes; buys or builds attack infrastructure. Uses the agent as a *tool for existing harm* (fraud, phishing at scale, malware distribution) or attacks the agent itself for exfiltration, tenancy abuse, or extortion.

**Capability profile.**

- **Prompt-craft** — commoditised; buys jailbreak-as-a-service.
- **Persistence** — campaign-scale, month-plus.
- **Tooling** — bespoke fuzzers, indirect-injection farms, plausible personas at scale.
- **Cross-tenant reach** — high: can control large swathes of the public web the agent retrieves from; can spin up seemingly-legitimate MCP servers.
- **Compute** — bounded by economics but non-trivial.

**Reachable surfaces.**

- ✅ Data-input direct + indirect at scale (SEO-poisoned pages, poisoned documentation, poisoned datasets published upstream).
- ✅ Environment (compromised sites, malicious PDFs distributed at scale).
- ✅ Tool-invocation via injection *and* via supply-chain compromise of tool endpoints.
- ✅ Memory / VS via poisoning of the upstream corpora feeding embeddings and RAG.
- ✅ Human-in-the-loop via phishing at scale.
- ✅ Cross-agent via rogue-agent registration and MCP-server compromise.

**Intent.** Money. Also credential theft and tenancy abuse of the operator's infrastructure (using the agent's compute or tool calls for downstream crime).

**Design implication.** Elevate supply-chain integrity: pinned MCP servers, signed tools, verified retrieval sources, egress-URL allow-lists. Elevate abuse monitoring: rate-limit anomalies, cross-tenant correlation, financial-instrument scrutiny. This is the persona for which the level-40 role starts to hand off to `security` / `ai-infra-security` (level 35 peer) and `ai-risk-engineer` (prerequisite) on platform hygiene.

### Tier 5 — Sophisticated targeted attacker

**Who they are.** APT-shape actor: patient, well-resourced, targeting a specific enterprise or specific data / capability. Objective is not commodity crime — it is exfiltration of proprietary data, sabotage, or persistent access.

**Capability profile.**

- **Prompt-craft** — bespoke, adaptive, may include model-inversion, membership-inference, and prompt-extraction targeting the operator's system prompt (OWASP LLM07 System Prompt Leakage).
- **Persistence** — months, with staging and adaptation.
- **Tooling** — bespoke, closed-source; possibly custom LLMs for red-team.
- **Cross-tenant reach** — targeted; may compromise a supplier to reach the enterprise.
- **Compute** — significant.

**Reachable surfaces.**

- ✅ All six, with **targeted** rather than opportunistic reach. Chains long-horizon persistence: plant a payload in a shared RAG document, wait for a benign trigger, exfiltrate through a tool call routed through a compromised MCP server.

**Intent.** Espionage, sabotage, persistent access.

**Design implication.** This is where safety-monitor discipline (mod-108) and safety-case argumentation (mod-109) become load-bearing. Prompt-side defences are necessary but insufficient; the enterprise-side controls (SIEM, SOC, DLP, network segmentation) reach in.

### Tier 6 — Nation-state uplift-seeker

**Who they are.** The persona the frontier-safety framework literature (RSP / Preparedness / FSF) explicitly reasons about. The threat model is *catastrophic-capability uplift* — a state actor extracting model output, exfiltrating weights, or coordinating a supply-chain campaign to get uplift on CBRN, cyber-offense, autonomy, or persuasion domains.

**Capability profile.**

- **Prompt-craft** — full frontier depth (GCG, TAP, best-of-N, fine-tune-elicitation of open-weight equivalents, multi-model attacker loops).
- **Persistence** — indefinite; strategic.
- **Standing** — may compromise supply chain, personnel, and infrastructure of the operator or a critical dependency.
- **Tooling** — bespoke; may include state-level cyber-offense capability.
- **Cross-tenant reach** — arbitrary through compromise; may include physical-world channels.
- **Compute** — effectively unbounded.

**Reachable surfaces.**

- ✅ All six; also the **frontier-lab surface**: weight exfiltration, training-data poisoning, safety-tuning-pipeline compromise (out of scope for enterprise-agent mitigation; addressed at the frontier-lab level by RSP / Preparedness / FSF safeguards).

**Intent.** Strategic capability uplift, sabotage of adversary infrastructure, denial of a competitor's capability.

**Design implication.** For most enterprise agents, this persona is *out of your role's scope* except as an input to tiering: an agent whose harm envelope reaches this persona's objectives (e.g., an agent capable of meaningful autonomy on ML R&D tasks) is a frontier-lab-tier deployment and the RSP / Preparedness / FSF safeguards apply. Chapter 10 makes the tiering rule explicit.

## Using the persona ladder

### Rule 1 — Only design against personas your deployment actually attracts

An internal read-only assistant used by 200 engineers does not attract Tier 4. An external customer-facing agent transacting on behalf of end-users attracts Tier 1, Tier 2, and Tier 4 by default and Tier 3 by proximity. An open-weight frontier-capable model attracts Tier 6.

Design against the personas your deployment tier attracts; document explicitly which personas you are *not* designing against, and cite the tier justification (chapter 10).

### Rule 2 — Insider (Tier 3) is almost always in scope

Even for internal-only deployments. The AI Incident Database contains numerous insider-caused harms in adjacent enterprise-AI contexts, and OWASP Agentic explicitly lists insider-shape threats (privilege compromise, identity spoofing, repudiation). Do not omit this row.

### Rule 3 — Match persona to surface, not the other way around

For each persona, mark the surfaces they reach and the *channel* by which they reach each. "Tier 4 reaches memory via poisoning the upstream Common Crawl-derived embedding corpus" is a useful ATMD entry; "Tier 4 → memory" is not.

### Rule 4 — Persona escalation is a real risk

A finding published by a Tier 2 red-teamer generalises to Tier 1 the next week. A Tier 4 compromise sold to a Tier 5 actor is a documented pattern. Model your persona ladder as a *diffusion graph*: what a lower tier discovers travels upward and outward.

### Rule 5 — Persona reach is dynamic

Capability profiles shift. When public jailbreak methods advance (e.g., automated jailbreaks like PAIR / TAP mature further), Tier 1's effective reach expands. Re-read the persona list on the same cadence you re-read the OWASP Top 10 for LLM Applications.

## A worked persona table for a hypothetical support agent

Assume an agent deployed at Acme Corp. It reads customer support tickets, searches an internal knowledge base, drafts replies, and — with a human approver — issues refunds up to $500.

| Persona | In scope? | Primary reach | Primary mitigations | Handoff |
|---|---|---|---|---|
| Tier 1 script-kiddie | Yes | Data-input (ticket text). | Guardrails baseline, refusal training, output classifier. | Owner: this role + mod-108. |
| Tier 2 power-user | Yes | Data-input direct + indirect via URLs in ticket; approval-fatigue on refund approver. | Adaptive-attack survival curves; approval-rate anomaly detection; URL fetch policy. | Owner: this role. |
| Tier 3 insider | Yes | All six — support agent operator has RAG-index write; on-call has kill-switch. | Access controls; separation-of-duties on RAG writes; approval logging. | Owner: `ai-risk-engineer` prereq + platform security peer. |
| Tier 4 cybercrime affiliate | Yes | Data-input (fake tickets at scale); memory poisoning via seeded feedback; refund-tool abuse; social engineering on approver. | Rate limiting; fraud checks on refund tool; provenance labels on memory writes; feedback-signal poisoning defence. | Owner: this role + fraud team + `ai-risk-engineer` prereq. |
| Tier 5 targeted attacker | Yes, low-frequency | Targeted RAG-poisoning of a specific product line; long-horizon prompt-extraction of system prompt. | System-prompt versioning; anomaly detection on retrieval patterns; DLP on refund arguments. | Owner: this role + SOC / DFIR peer. |
| Tier 6 nation-state uplift | **No** — agent capability envelope not in scope. | — | Documented tier justification for exclusion. | Handoff to frontier-safety program (mod-112) if the capability envelope changes. |

That table is one row in the ATMD skeleton (chapter 01). Exercise 01 asks you to author it for a concrete agent of your choice.

## Common misreadings to avoid

- **"We only need to worry about Tier 6."** For most enterprise deployments Tier 6 is out of scope — but Tier 1–5 are the ones you are actually shipping mitigations for. Do not skip the base of the ladder.
- **"Insider is HR's problem."** Insider misuse is a technical control problem *and* an HR / policy problem. This role owns the technical-control side (separation-of-duties, audit logging, config-change review).
- **"If we defend against Tier 5, Tier 1 is free."** No — Tier 5 mitigations often assume the low tiers are already handled. The ladder is cumulative; every tier's mitigations must be in place.
- **"Persona is a person, not a group."** Persona is a capability profile, not a headcount. Tier 4 is one persona; the number of Tier 4 *actors* is a separate metric (attack-attempt frequency).

## Summary

- Six adversary-persona tiers: script-kiddie, curious power-user, insider, cybercrime affiliate, sophisticated targeted attacker, nation-state uplift-seeker.
- Each persona has a distinct capability profile and a distinct surface-reach map (chapter 02).
- The tiering rule (chapter 10) determines which personas your deployment attracts and which you can defensibly exclude with citation.
- Persona reach diffuses over time; re-read the ladder on the same cadence you re-read overlay checklists.
