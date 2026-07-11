# mod-107-excessive-agency-containment: Excessive-Agency Containment Engineering

> Scaffolded by `aicg org execute-plan`. Lecture chapters and exercise content are authored on subsequent autonomous cycles.

**Estimated effort:** 14 hours

## Learning objectives

- Engineer capability gates — allow-list tool policies, argument validators, side-effect scopes, principle-of-least-authority for tool credentials, per-tool rate limits, action budgets, blast-radius caps — grounded in OWASP LLM06 (Excessive Agency) and OWASP Agentic Threats
- Engineer sandboxed tool execution — gVisor / Firecracker / OS-level sandboxes for code-interpreter, network-egress allow-lists, filesystem overlay, ephemeral compute, side-effect quarantine + human confirmation
- Engineer monitored-tool wrappers — argument diffing, outcome verification, policy-enforcement in the wrapper (not in the model), audit-log emission, tamper-evident action logs
- Engineer human-in-the-loop escalation — decision thresholds, escalation UI patterns, timeout defaults, revocable-consent workflow, and bypass-prevention (against the LLM socially engineering the human, against fatigued-approval, against approval-bombing)
- Engineer kill-switches / stop-buttons — false-positive cost accounting, escalation-fan-in, org-wide stop-button orchestration, and the incident-response contract for after a stop-button fires
- Cite the boundary to `senior-agentic-ai-engineer` on the agent-architecture patterns being contained and to `ai-infra-security` (peer / next-up, level 35) on the runtime-hardening / secret-scoping platform this contract plugs into

## Structure

- `01-…md` … `0N-…md`: lecture chapters.
- `exercises/`: per-exercise prompts.
- `labs/`: long-form hands-on labs.
- `quizzes/`: knowledge checks.
- `resources.md`: external references.
