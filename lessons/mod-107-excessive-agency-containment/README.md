# mod-107-excessive-agency-containment: Excessive-Agency Containment Engineering

**Estimated effort:** 14 hours

## What this module is

Excessive-Agency Containment is the discipline of engineering the runtime to be **narrower than the model**. Every real agent-caused harm is a runtime failure to gate a capability the model should never have exercised in that state. This module builds the load-bearing controls — capability gates, sandboxes, monitored wrappers, human-in-the-loop escalations, and kill switches — into a single deliverable this module names the **Excessive-Agency Containment Contract (EACC)** for a specific deployment.

Ground truth is **OWASP LLM06 (Excessive Agency)** and the **OWASP GenAI Agentic AI Threats and Mitigations** entries for Tool Misuse, Privilege Compromise, Resource Overload, Overwhelming Human-in-the-loop, and Repudiation & Untraceability. The chapters cite the specific standards and primary sources for each layer.

## Learning objectives

- Engineer capability gates — allow-list tool policies, argument validators, side-effect scopes, principle-of-least-authority credentials, per-tool rate limits, action budgets, blast-radius caps.
- Engineer sandboxed tool execution — gVisor / Firecracker / OS-level sandboxes, network-egress allow-lists, filesystem overlay, ephemeral compute, side-effect quarantine + human confirmation.
- Engineer monitored-tool wrappers — argument diffing, outcome verification, wrapper-side policy enforcement, audit-log emission, tamper-evident action logs.
- Engineer human-in-the-loop escalation — decision thresholds, escalation UI, timeout defaults, revocable consent, bypass-prevention against social engineering, fatigue, and approval-bombing.
- Engineer kill-switches / stop-buttons — false-positive cost accounting, escalation fan-in, org-wide orchestration, and the incident-response contract after a fire.
- Cite the boundary to `senior-agentic-ai-engineer` (agent-architecture patterns being contained) and to `ai-infra-security` (runtime-hardening platform this contract plugs into).

## Chapters

1. [`01-capability-gates-and-policy-design.md`](01-capability-gates-and-policy-design.md) — the seven capability-gate primitives and the EACC.
2. [`02-sandboxed-tool-execution.md`](02-sandboxed-tool-execution.md) — compute / network / filesystem / persistence isolation, and the side-effect proposal pattern.
3. [`03-monitored-tool-wrappers.md`](03-monitored-tool-wrappers.md) — canonicalisation, outcome verification, wrapper-side policy, tamper-evident audit logs.
4. [`04-human-in-the-loop-escalation.md`](04-human-in-the-loop-escalation.md) — escalation contract, UI patterns, timeouts, revocable consent, and six bypass classes.
5. [`05-kill-switches-and-stop-buttons.md`](05-kill-switches-and-stop-buttons.md) — fire modes, false-positive cost accounting, org-wide orchestration, incident-response contract.
6. [`06-boundaries-to-senior-agentic-and-ai-infra-security.md`](06-boundaries-to-senior-agentic-and-ai-infra-security.md) — what mod-107 owns vs. what peer roles own; the handoff shapes.

## Exercises

Each exercise builds one section of the EACC (or its testing / drill artefact) for a concrete deployment of your choice.

1. [`exercises/exercise-01-capability-gate-and-policy-design.md`](exercises/exercise-01-capability-gate-and-policy-design.md) — Author EACC sections 1–5 for one agent deployment.
2. [`exercises/exercise-02-sandboxed-tool-execution-hands-on.md`](exercises/exercise-02-sandboxed-tool-execution-hands-on.md) — Build a sandbox-contract test suite and run it against a chosen isolation platform.
3. [`exercises/exercise-03-monitored-tool-wrapper-authoring.md`](exercises/exercise-03-monitored-tool-wrapper-authoring.md) — Implement a monitored wrapper for one tool, with canonicalisation, verification, and tamper-evident log.
4. [`exercises/exercise-04-human-in-the-loop-escalation-and-bypass-prevention.md`](exercises/exercise-04-human-in-the-loop-escalation-and-bypass-prevention.md) — Author EACC section 6 and red-team it against the six bypass classes.
5. [`exercises/exercise-05-kill-switch-and-stop-button-orchestration.md`](exercises/exercise-05-kill-switch-and-stop-button-orchestration.md) — Design the kill-switch fan-in, register the modes, and run a table-top drill.

## Structure

- `01-…md` … `06-…md`: lecture chapters.
- `exercises/`: per-exercise prompts (this repo). Solutions live in the paired `agentic-safety-engineer-solutions` repo.
- `labs/`: long-form hands-on labs (placeholder).
- `quizzes/`: knowledge checks (placeholder).
- `resources.md`: external references.
