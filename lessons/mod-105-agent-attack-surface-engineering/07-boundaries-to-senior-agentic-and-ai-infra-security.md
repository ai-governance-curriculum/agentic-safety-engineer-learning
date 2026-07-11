# 07 — Boundaries: `senior-agentic-ai-engineer` and `ai-infra-security`

## Motivation

The AASS produces findings; other teams engineer the fixes. A well-authored finding routes to *the right team* with the *right supporting artefacts* so the fix ships without a second discovery cycle. Two peer roles absorb most of this module's finding feed:

- **`senior-agentic-ai-engineer`** (peer, agentic AI family, level 40) owns the *agent-architecture patterns* the AASS attacks — planner shape, memory layout, sub-agent orchestration, protocol integration. Findings that indict a *pattern* (not a specific tool wrapper, not a specific credential scope, but the way the architecture composes them) route here.
- **`ai-infra-security`** (peer / next-up, level 35) owns the *tool-runtime hardening* the AASS's findings hand off to — sandbox construction, MCP server hardening, credential brokers, egress proxies, argument validators, capability-token issuers, audit-log immutability. Findings that pattern-match to "the runtime layer under the agent needs a specific control" route here.

This chapter codifies both boundaries, plus the boundaries to the modules inside this track that consume the AASS's outputs downstream: mod-107 (excessive-agency containment), mod-108 (guardrails and monitors), mod-111 (scaled red-team), mod-109 (safety cases), and mod-112 (safety program and disclosure). Every AASS finding names which of these owners is responsible for remediation and what artefact the AASS ships to make the remediation actionable.

## The peer-role handoffs

### Boundary 1 — `senior-agentic-ai-engineer` (peer, agentic AI family, level 40)

The peer role is *agentic AI engineering* at the same seniority as this track. They design and evolve the agent's architecture: planner style (ReAct vs. plan-and-execute vs. orchestrator), memory topology (per-session, per-user, per-tenant), sub-agent policy, protocol adoption (A2A, MCP, in-house), and tool-composition patterns.

**What the AASS ships to the peer role.**

- **Pattern-level findings.** "Goal drift is possible because reflection reads untrusted context; every deployment of this planner pattern has this weakness; remediation is a *pattern-level* fix (goal-register pinning, read-only reflection)."
- **Reproducibility bundles.** Chain traces, memory-state snapshots, multi-agent graph exports — the artefacts the peer role needs to replicate and evaluate a pattern-level fix.
- **Utility-security Pareto curves.** For a proposed pattern-level fix, the peer role needs to know its effect on both ASR and utility (chapter 06). The AASS pre-computes the curve for the current pattern and re-runs it against the proposed alternative.

**What the peer role ships back.**

- **Pattern-level remediations.** Architecture changes that reduce ASR without disproportionately reducing utility. Examples:
  - Goal-register pinning outside the model context (defence against chapter 04 goal replacement).
  - Structured message schemas for A2A / MCP (defence against chapter 05 cross-agent injection).
  - Least-privilege sub-agent spawn (defence against chapter 04 plan-branching escape).
  - Provenance-preserving memory reads (defence against chapter 03 cross-tenant poisoning).
- **Pattern-level pre-commit reviews.** The peer role reviews AASS finding artefacts before shipping architecture changes; the AASS re-runs post-change to verify the remediation held.

### Boundary 2 — `ai-infra-security` (peer / next-up, level 35)

The peer role is *AI infrastructure security* engineering: the runtime layer under the agent that hosts tools, models, and data. In particular they own the *tool-runtime hardening* the AASS's findings ride into: sandboxes (Firecracker / gVisor / bwrap), MCP servers (auth, transport, argument-validation), credential brokers, egress proxies, IAM-token scoping, audit-log immutability, per-tool blast-radius caps.

**What the AASS ships to the peer role.**

- **Tool-runtime findings.** "The MCP mail-server accepts client-provided principal claims without transport-verified identity; chapter 05 sub-family 3 chain X succeeds by spoofing agent-A. The fix is a server-side transport-verified principal."
- **Argument-smuggling findings.** "The mail tool accepts arbitrary body content; chapter 02 sub-family 2 chain Y smuggles exfil data through it. The fix is per-argument egress checks and content typing."
- **Egress-policy findings.** "The code-interpreter tool has no egress restrictions; chapter 02 sub-family 4 chain Z reads cloud metadata. The fix is an egress allow-list at the sandbox network layer."
- **Capability-token requests.** "The tool bus does not currently issue capability tokens; chapter 06 posture 2 cannot be measured. The fix is to introduce a capability-token issuer, ideally bound to sub-goal IDs."

**What the peer role ships back.**

- **Hardened runtime components.** Sandbox images, MCP server versions with verified-principal auth, capability-token issuers, egress proxies. The AASS re-runs its cells against the hardened components to verify the ASR drop.
- **Finding-feed schemas.** The AASS's finding artefacts arrive in a schema the peer role's remediation intake can consume automatically. Chapter 06's coverage-report YAML block is the current version; the AASS commits to it as an integration contract.

### The subtle place both peer roles overlap

Some findings are ambiguous. "The A2A protocol as configured does not preserve provenance across hops, so cross-agent injection lands" could be a *pattern-level* finding (adopt a provenance-preserving profile) or an *infra-level* finding (patch the current A2A server to carry the provenance metadata). Two coping rules:

- **Every ambiguous finding routes to both roles with an explicit primary and a secondary.** Whichever role's remediation ships first, the other role is on notice and reviews.
- **Both roles co-own the artefact review.** For findings that touch protocol semantics, the pattern-level fix and the infra-level fix are one review; both roles sign off.

## The intra-track handoffs

The AASS's findings also feed the other modules in this track. Each has a distinct consumption pattern.

### mod-107 (Excessive-Agency Containment Engineering)

mod-107 owns the *containment engineering* that turns the AASS's defensive-posture toggles (chapter 06) from *measurements* into *deployed defences*. Specifically:

- Capability-token issuance policy.
- Per-tool blast-radius caps.
- Argument allow-lists and egress policies.
- HITL approval-UX design with argument-diff surfacing.
- Sandbox-hardening acceptance criteria.

**What the AASS ships to mod-107.**

- Chain findings from chapters 02–05 whose defensive-posture remediation is a *containment* choice (as opposed to a monitor, a training change, or a pattern rewrite).
- Per-cell ASR under each posture — mod-107's prioritisation queue is anchored on the "which posture drop delivers the largest ASR reduction?" question.

**What mod-107 ships back.**

- Deployed containment layers the AASS then measures. The two-way loop is on a cadence set by the operator (weekly, monthly).

### mod-108 (Frontier Guardrails and Monitors)

mod-108 owns the *classifier and monitor* work: constitutional classifiers, guardrail policies, judge-time monitors, refusal-robustness curriculum. These are the *runtime detection* half of a defence stack, complementary to mod-107's containment.

**What the AASS ships to mod-108.**

- Findings whose remediation is a *monitor* — a per-turn or per-step classifier that catches the attack signal in real time. Examples: goal-drift monitors, memory-write anomaly detectors, semantic-consistency monitors on multi-agent messages.
- The full attack corpus (defanged, referenced from the harmful-payload store) that mod-108 uses as *training data* for adversarial-hard classifiers.
- The judge scaffolds the AASS calibrated (chapter 06), which mod-108 can reuse as monitor scaffolds under the constitutional-AI framing.

**What mod-108 ships back.**

- Trained monitors deployed against the target agent. The AASS then measures the monitor's per-cell effect as one of the defensive-posture layers (or as a new posture 4 in the coverage matrix).

### mod-111 (Automated and Scaled Red-Team)

mod-111 owns the *industrialisation* of this track's red-team work: turning the engineer-hands-on-keyboard AASS into a scheduled, sharded, cost-managed pipeline that runs coverage against every model / framework / target on every release cadence.

**What the AASS ships to mod-111.**

- The *reusable* chain library, poisoning bench, planning-subversion probes, and multi-agent bench with clean interfaces (a target-agent adapter, a defensive-posture toggle, a benchmark plugin).
- Cost and throughput profiles per cell (chapter 06). mod-111's affordable coverage is bounded by them.
- The AgentDojo + InjecAgent integrations, ready to run against a fleet of targets.

**What mod-111 ships back.**

- Continuous coverage numbers across the fleet. The AASS becomes one release-cadence node in a longer-running harness the mod-111 pipeline manages.

### mod-109 (Safety Cases and Structured Argumentation)

mod-109 owns the *safety-case* artefacts that argue "we have adequate assurance that this agent is safe to deploy for use-case X." The AASS's coverage matrix (chapter 06) is a core input.

**What the AASS ships to mod-109.**

- The AASS's coverage-report YAML block (chapter 06) — the *evidence artefact* that grounds the safety case's claim about agent-level attack robustness.
- Uncertainty envelopes: which cells have low judge–human agreement, which have small trial counts, which have needs-research markers on the underlying benchmarks.
- The *elicitation gap* discussion: the AASS's attackers are engineer-hands-on-keyboard, not a nation-state adversary; the safety case must not treat the AASS's ASR as a ceiling.

### mod-112 (Safety Program and Disclosure)

mod-112 owns the *disclosure workflow* — coordinated disclosure to model providers, framework maintainers, and downstream consumers when a finding warrants it. The AASS's harmful-payload store hooks into mod-112's disclosure process.

**What the AASS ships to mod-112.**

- Findings whose severity crosses the disclosure threshold, with reproducibility bundles (chapter 02) and payload store handles.
- The mod-112 severity scale applied to each finding.
- The public-safe *shape* description (what the finding is about, without the payload) for external communication.

## The AASS's boundary-routing block

Every AASS run's report includes an explicit boundary-routing block. The schema (from chapter 06, expanded here):

```yaml
boundary_routing:
  routes_to:
    senior_agentic_ai_engineer:
      pattern_level_findings:
        - finding_id: <id>
          pattern_name: <e.g. reflection-reads-untrusted-context>
          proposed_remediation_class: <goal-register-pinning | provenance-preserving-protocol | ...>
    ai_infra_security:
      tool_runtime_findings:
        - finding_id: <id>
          component: <mcp-mail-server | code-interpreter-sandbox | credential-broker | ...>
          proposed_remediation_class: <verified-principal | egress-allow-list | capability-token-issuer | ...>
    mod_107_containment:
      - finding_id: <id>
        containment_layer: <capability-gate | argument-allow-list | egress-policy | hitl-ux>
    mod_108_monitors:
      - finding_id: <id>
        monitor_class: <goal-drift | memory-write-anomaly | semantic-consistency | ...>
        training_data_ref: <handle in harmful-payload store>
    mod_109_safety_case:
      evidence_refs:
        - coverage_report_ref: <handle>
          uncertainty_notes: <string>
    mod_111_scaling:
      scale_candidates:
        - {cell_id: <id>, throughput_estimate: <trials/hr>, cost_per_trial: <$>}
    mod_112_disclosure:
      disclosure_candidates:
        - {finding_id: <id>, severity: <mod-112 scale>, coord_target: <model-provider | framework-maintainer | downstream>}
```

The block is not a decorative addendum — it is the operational hand-off that makes the AASS's findings shippable to remediation without a second discovery cycle.

## Non-boundaries — what the AASS does *not* hand off

Two categories of finding stay with the AASS's authoring team rather than routing out:

- **False positives.** A cell where the judge scored a success but the human-agreement sample disagrees. The AASS's authoring team owns the judge fix or the case correction, not the peer team.
- **Un-reproducible one-shots.** A trial the AASS could not reproduce on a rerun. The authoring team owns the investigation; a not-yet-reproducible finding does not route out.

Both categories carry a finding record and are visible in the AASS's coverage report as *pending* rows so nothing gets silently dropped.

## Common engineering mistakes at the boundary

- **Emitting infra-level fixes as pattern-level findings.** "Fix the MCP server auth" is an `ai-infra-security` finding, not a `senior-agentic-ai-engineer` finding. Mis-routing wastes the peer role's time.
- **Emitting pattern-level fixes as infra-level findings.** "Add goal-register pinning to the planner" is a `senior-agentic-ai-engineer` finding, not an `ai-infra-security` finding. Same mistake in the other direction.
- **Not versioning the finding-feed schema.** Peer roles automate consumption on top of the schema; a schema change without a version bump breaks their intake.
- **Emitting a finding without a proposed remediation class.** The peer role has more context than the AASS's authoring team on the exact fix; they do not need a specification. They *do* need a remediation *class* so the finding lands with the right sub-team.
- **Emitting a finding without the reproducibility bundle.** No bundle → no shipping. The bundle is the boundary contract.
- **Missing mod-109's uncertainty envelope.** A safety-case artefact that presents the AASS's ASR as a certain number is dishonest; the envelope is mandatory.
- **Missing mod-112's public-safe shape description.** External disclosure needs a description that carries the finding's shape without the payload; the AASS pre-authors it.

## Summary

- The AASS is a *finding-producing* module. Chapter 07 codifies the boundaries to the roles and modules that *consume* those findings and engineer the fixes.
- **`senior-agentic-ai-engineer`** owns pattern-level remediations (planner shape, memory layout, protocol integration); the AASS ships pattern-level findings with reproducibility bundles and Pareto curves.
- **`ai-infra-security`** owns tool-runtime hardening (sandboxes, MCP servers, credential brokers, egress); the AASS ships tool-runtime findings with proposed remediation classes.
- **mod-107** consumes containment findings; **mod-108** consumes monitor findings and training data; **mod-111** consumes the reusable bench for scaling; **mod-109** consumes the coverage matrix as safety-case evidence; **mod-112** consumes findings for coordinated disclosure.
- Every AASS run's report includes an explicit **boundary-routing block** — the operational hand-off that makes findings shippable to remediation without a second discovery cycle.
- The pattern that ties the whole track together: this module *measures*; the peer roles and other modules *engineer*; the AASS *re-measures*. The loop is the delivery vehicle.
