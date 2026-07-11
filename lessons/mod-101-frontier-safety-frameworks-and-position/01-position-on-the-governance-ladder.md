# 01 — Position on the Governance Ladder

## Motivation

Before you read a single safety framework, you have to know **where you sit on the level ladder** — because the ladder tells you what evidence to produce, what to defer down to a prerequisite role, and what to hand upward to an architect or program lead.

Every requirement in this track lands somewhere on that ladder. If you cannot name the level that owns a task, you cannot draw the artifact contract, and you will either duplicate a peer's work or leave a hole that a next-up role expects you to have filled. This chapter draws that map. Everything after it — RSP, Preparedness, FSF, EU AI Act, AISI methodology — is written against the map in this chapter.

## The role in one sentence

The **Agentic Safety & Red-Team Engineer** (level 40, AI Governance family) owns the *frontier-agent red-team and dangerous-capability evaluation craft end-to-end* — engineering-grade fluency with frontier safety frameworks, agent-specific threat modelling, prompt-injection and jailbreak engineering at frontier depth, agent-attack-surface engineering, dangerous-capability elicitation (CBRN, cyber-offense, autonomy, persuasion, self-exfiltration), excessive-agency containment engineering, frontier-scale guardrail and safety-monitor engineering, safety-case authoring in GSN, AI-control and adversarial-alignment evaluation, automated and scaled red-teaming, and the frontier-safety program plus serious-incident-response plus disclosure engineering slice.

## The ladder around this role

```
Level 70  Chief AI Officer                         ← executive scope
Level 60  Head of AI Governance                    ← program leadership, board reporting, regulator engagement
Level 50  Senior AI Governance Architect           ← control-library architecture, policy taxonomy, cross-jurisdiction reconciliation
─────────────────────────────────────────────────
Level 40  Agentic Safety & Red-Team Engineer       ← YOU ARE HERE (Governance family)
─────────────────────────────────────────────────
Level 35  AI Evaluation Engineer (Governance)      ← release-assurance, audit trail, regulator-facing methodology
Level 35  Security / AI Infra Security             ← platform-scale ML security (peer / next-up)
Level 30  AI Eval Engineer (AI Engineering)        ← application-layer eval plumbing (traces, judges, RAG eval, CI/CD)
Level 30  Model Evaluation Engineer (ML)           ← statistical / benchmark / calibration methodology
Level 30  Fine-Tuning Engineer                     ← post-training / safety-tuning stack
Level 25  AI Risk Engineer                         ← PREREQUISITE — general engineering craft of AI risk
Level 20  ML Engineer                              ← ML fundamentals
Level 15  AI Governance Analyst                    ← operational analyst legwork
Level 10  AI Infra Junior Engineer                 ← engineering-craft prerequisites
```

## Ownership rule

The ownership rule is: **assign a task to the lowest level that genuinely requires it, and link everywhere else**. Duplication is the failure mode.

This role:

- **Owns** the entire frontier-agent red-team, dangerous-capability elicitation, excessive-agency containment, guardrail-engineering, safety-case, AI-control-evaluation, automated-red-team, and safety-program-plus-disclosure slice at frontier depth.
- **Defers down** to `ai-risk-engineer` (level 25) for the general engineering craft of AI risk; to `ai-governance-analyst` (level 15) for operational analyst work; to `ml-engineer` (level 20) and `ai-infra-junior-engineer` (level 10) for engineering fundamentals.
- **Defers sideways** to `ai-eval-engineer` (level 30, AI Engineering family) for application-layer eval plumbing; to `model-evaluation-engineer` (level 30, ML Engineering family) for statistical methodology; to `fine-tuning-engineer` (level 30) for the post-training / safety-tuning stack; to `security` / `ai-infra-security` (level 35) for platform-scale ML security; to `ai-evaluation-engineer` (level 35, Governance family) for release-assurance / audit-trail methodology.
- **Defers up** to `senior-ai-governance-architect` (level 50) for control-library architecture and cross-jurisdiction policy reconciliation; to `head-of-ai-governance` (level 60) and `chief-ai-officer` (level 70) for program leadership, board reporting, and regulator engagement.
- **Out of scope** to legal counsel (this role does not deliver legal opinion), SOC / DFIR (SOC-side incident handling — this role feeds AI-specific signal *to* SecOps), and the frontier-lab safety-research-scientist ladder (novel-alignment research at PhD-publication depth — this role *consumes* that research but does not compete for the same postings).

## What the `ai-risk-engineer` (level 25) prerequisite covers, that this curriculum does not re-teach

`ai-risk-engineer-learning` (level 25) is the **general** engineering craft of AI risk, at enterprise scope and general depth. This curriculum treats that packet as fully assumed. The prerequisite covers:

- **General framework fluency at engineering depth** — NIST AI RMF and the GenAI Profile, ISO/IEC 42001 (AI management system), ISO/IEC 23894 (AI risk management), ISO/IEC 42005 (AI system impact assessment), ISO/IEC 25059 (AI quality model), EU AI Act Articles 9–15 (risk management, data governance, technical documentation, record-keeping, transparency, human oversight, accuracy / robustness / cybersecurity) at general depth, US SR 11-7 model risk management, FDA GMLP, PCCP.
- **Harm modelling** grounded in the AI Incident Database, OECD.AI incidents, and the MIT AI Risk Repository.
- **Risk quantification** — impact × likelihood × exposure, inherent / residual / control-defeated distinctions, portfolio aggregation.
- **General LLM red-teaming** with `garak`, PyRIT, Promptfoo red-team, Inspect at general depth. This curriculum re-uses those suites and adds frontier-depth attack engineering, LLM-vs-LLM attacker loops, and dedicated adversarial-attacker models.
- **General adversarial-ML at working depth** — ART, TextAttack, CleverHans, mapped to NIST AI 100-2, MITRE ATLAS, OWASP ML Top 10.
- **Fairness / bias / explainability** — Fairlearn, AIF360, SHAP, Alibi, LIME.
- **Privacy-risk engineering** — Opacus, TensorFlow Privacy, ML Privacy Meter, Presidio; membership-inference attacks at production quality.
- **General guardrail engineering** — NeMo Guardrails, Guardrails AI, Llama Guard, ShieldGemma, OpenAI Moderation, Constitutional Classifiers at general depth. This curriculum adds *fine-tuning* a classifier guard, adaptive-attack survival curves, and defence-in-depth composition on top.
- **Model risk management shape** — MRM lifecycle, validation independence, model inventory, tiering.
- **Risk register wiring** — monitoring-to-register plumbing, register review cadence.
- **Incident RCA** — AI-specific post-mortem, regression-fixture back-feed, at team scope.
- **Cross-functional AI risk program at team scope** — intake, sign-offs, cadence.

If you cannot ship these end-to-end already, complete `ai-risk-engineer-learning` first. Nothing in this track will make you fluent in them — they are the substrate.

## What this role owns *beyond* the prerequisite (the 12-module map)

| Module | What this role adds beyond the level-25 prerequisite |
|---|---|
| mod-101 (this) | Frontier-lab safety frameworks (RSP / Preparedness / FSF) at engineering depth, EU GPAI CoP + AI Act 55/56/73, US EO 14110, Bletchley + Seoul outcomes, AISI methodology, Frontier Model Forum. |
| mod-102 | Agent-specific threat modelling — OWASP Agentic Threats, MITRE ATLAS applied to agents, agent-specific harm-model authoring. |
| mod-103 | Prompt-injection engineering at frontier depth — direct, indirect (retrieval, tool responses, long-term memory), obfuscation. |
| mod-104 | Jailbreak engineering at frontier depth — GCG, PAIR, TAP, Crescendo, many-shot, in-the-wild DAN taxonomies. |
| mod-105 | Agent-specific attack surface — tool-abuse chains, memory / vector-store poisoning, long-horizon planning subversion, multi-agent adversarial coordination. |
| mod-106 | Dangerous-capability elicitation — CBRN, cyber-offense, autonomy (METR / RE-Bench / SWE-bench Verified), persuasion, self-exfiltration. |
| mod-107 | Excessive-agency containment engineering — capability gates, sandboxed tool execution, monitored-tool wrappers, human-in-the-loop bypass prevention, kill-switches. |
| mod-108 | Frontier-scale guardrails — fine-tuning classifier guards, Constitutional Classifiers methodology, adaptive-attack survival curves, defence-in-depth composition. |
| mod-109 | Safety-case authoring in GSN — Inability / Control / Trustworthiness / Deference argument shapes. |
| mod-110 | AI control + adversarial-alignment evaluation — deception, alignment-faking, sandbagging, sleeper-agent, in-context scheming, sabotage. |
| mod-111 | Automated + scaled red-teaming — LLM-vs-LLM attacker loops, fine-tuned adversarial models, StrongREJECT judge methodology, harmful-payload discipline. |
| mod-112 | Frontier-safety program at organisation scope — RSP / Preparedness / FSF tier contribution, system-card + AISI-report authoring, EU AI Act Article 73 serious-incident response. |

## What the next-up roles inherit or extend

- **`ai-evaluation-engineer` (peer / next-up, level 35, Governance family)** — inherits this role's frontier-safety evidence and extends into release-assurance discipline: audit-trail authoring, evidence packaging for regulators, EU AI Act post-market surveillance (Article 72), lifecycle traceability. If your artifact leaves the frontier-safety program and enters the release-assurance file, this is who receives it.
- **`senior-ai-governance-architect` (level 50)** — inherits and extends into **cross-jurisdiction control-library architecture**: one control catalog reconciled against EU AI Act, EO 14110 successor policy, UK regulator guidance, NYC Local Law 144, Colorado SB 205, etc. Also owns policy taxonomy and the master framework crosswalk.
- **`head-of-ai-governance` (level 60)** — inherits and extends into **program leadership**: cross-functional operating rhythm, board reporting, budget and headcount, regulator engagement, industry-body representation (FMF, Partnership on AI).
- **`chief-ai-officer` (level 70)** — executive scope.

## What is explicitly out of scope

- **Legal opinion.** You author engineering artifacts (safety cases, system cards, AISI reports, Article 73 incident reports). Legal counsel authors the legal opinion, drafts contracts, and signs on regulatory posture. You feed them.
- **SOC / DFIR handling.** When a frontier-safety incident is also an infosec incident (credential compromise, exfiltration, availability), SecOps runs the incident-handling playbook. You provide AI-specific signal, adversarial-context, capability-context. You do not run the SOC.
- **Novel-alignment research at PhD-publication depth.** Interpretability, mech-interp, RLHF theory, scalable-oversight theory. You *consume* this research (Redwood control protocols, Apollo deception evaluations, Anthropic sleeper-agents / alignment-faking / sabotage-evals, Meinke et al. in-context scheming) and productionise evaluations from it. You do not compete on the frontier-lab safety-research-scientist ladder.

## The deferral contract as a template

Every artifact you author has an implicit *deferral contract* — a header that names the level it belongs to, the level that consumes it, and the level that would take over if this were a level-lower or level-higher task. A simple form:

```yaml
artifact: dangerous-capability-eval-report-v1
level_of_owner: 40   # this role
deferred_down_to:
  - level: 25
    role: ai-risk-engineer
    scope: "general adversarial-ML evaluation methodology"
deferred_sideways_to:
  - level: 30
    role: model-evaluation-engineer
    scope: "best-of-N confidence intervals, judge-vs-human calibration"
  - level: 30
    role: fine-tuning-engineer
    scope: "post-training / safety-tuning stack the eval measures"
consumed_by:
  - level: 35
    role: ai-evaluation-engineer
    scope: "release-assurance packaging"
  - level: 50
    role: senior-ai-governance-architect
    scope: "control-library update"
  - level: 60
    role: head-of-ai-governance
    scope: "board reporting"
```

This is not decoration — it is the mechanism that stops you from re-teaching the level-25 material inside a level-40 artifact, and stops the level-50 architect from re-doing the level-40 elicitation methodology. Exercise 05 asks you to author this contract for a concrete artifact.

## Summary

- This role is level 40, in the AI Governance family, and it owns the frontier-agent red-team + dangerous-capability + safety-case + control-eval + guardrail-engineering + safety-program slice.
- `ai-risk-engineer-learning` (level 25) is the required immediate prerequisite. Its general framework fluency, harm modelling, general adversarial-ML, MRM shape, risk registers, incident RCA, and team-scope cross-functional program are **not** re-taught here.
- Peers at level 30 (`ai-eval-engineer`, `model-evaluation-engineer`, `fine-tuning-engineer`) and level 35 (`security` / `ai-infra-security`, `ai-evaluation-engineer` Governance) take specific slices sideways; this curriculum documents the interface, not their depth.
- Next-up roles at level 50 (`senior-ai-governance-architect`), 60 (`head-of-ai-governance`), and 70 (`chief-ai-officer`) inherit this packet upward.
- Every artifact you author carries a deferral contract that names the level it belongs to.
