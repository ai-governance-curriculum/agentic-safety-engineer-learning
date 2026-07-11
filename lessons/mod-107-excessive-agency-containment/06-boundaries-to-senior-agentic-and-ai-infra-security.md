# 06 — Boundaries: `senior-agentic-ai-engineer` and `ai-infra-security`

## Motivation

Excessive-Agency Containment is a *contract* on top of two neighbouring roles' craft. Above it is the **agent architecture**: the planner, the tool bus, the memory store, the sub-agent decomposition — the things being contained. Beside it (peer, next-up) is the **runtime-hardening platform**: the sandbox fleet, the credential broker, the WORM store, the workload-identity fabric — the things containment runs on. The safety-engineering role in this module cannot ship a defensible containment posture without both peer-roles' artefacts under it.

Naming the boundaries in advance is not bureaucracy; it is what separates a Containment Contract that a reviewer will sign from one that is quietly bundled with unowned platform work and unowned agent-architecture changes. When a mod-107 finding recommends "swap the sandbox fleet to Firecracker snapshots for cross-session persistence," the recommendation is a peer-role handoff, not a mod-107 build. When a mod-107 finding recommends "decompose the planner so that the sub-agent's budget is bounded by the parent's remaining budget," that is a peer-role handoff too. The Containment Contract *names* those handoffs and points at the peer role's artefact; the peer role delivers.

This chapter draws the boundary in detail and codifies the handoff shape.

## The two peer roles

### `senior-agentic-ai-engineer` (peer, agentic-AI family, level 40)

Owns the **agent architecture patterns** being contained. Includes:

- The planner / executor / critic loop shape.
- The tool bus interface the wrappers plug into.
- The memory store's read / write API.
- The sub-agent decomposition (fork / join, principal inheritance, budget inheritance).
- The A2A / MCP-shaped multi-agent protocols the deployment participates in.
- The retrieval-augmentation loop and its prompt-injection surface.

The safety-engineering role in mod-107 consumes this role's design as *the thing being contained*. When the EACC needs to reference "the sub-agent's budget is a fraction of the parent's" the peer role owns the mechanism; mod-107 owns the *contract* that the mechanism must satisfy.

### `ai-infra-security` (peer / next-up, level 35, AI Infra family)

Owns the **runtime-hardening platform** the containment contract plugs into. Includes:

- The sandbox fleet (gVisor / Firecracker / hardened-container platform).
- The egress-proxy platform and its allow-list authoring.
- The credential broker, KMS, and workload-identity fabric (SPIFFE / SPIRE-shaped, cloud IAM).
- The WORM audit-log store and the signing service.
- The base-image build pipeline and its CVE-patch cadence.
- The runtime observability / telemetry pipeline.

The safety-engineering role consumes these platform components as *the layer the containment runs on*. The EACC's section-4 credential contract *names* the broker; the broker's platform lives here. The EACC's section-7 sandbox contract *names* the sandbox class; the sandbox platform lives here. The chapter-03 tamper-evident log *names* the WORM store; the WORM store lives here.

## Where the boundary lies

The boundary is not a syntactic property; it is an ownership property. Two questions determine which role owns a piece of work:

**Question 1 — *what* is the artefact?**

- If the artefact is *the Excessive-Agency Containment Contract* (EACC) for a specific deployment — the policy configuration, the escalation contract, the fire-mode configuration — mod-107 owns it.
- If the artefact is *the platform component that implements a class of controls* (the sandbox fleet, the credential broker, the WORM store, the egress proxy), the peer-role owns it.
- If the artefact is *the agent's architectural pattern* (the planner, the sub-agent decomposition, the retrieval loop), `senior-agentic-ai-engineer` owns it.

**Question 2 — *who is the reviewer* for the artefact?**

- If the reviewer is a safety-review body, a regulator, or the RSP / Preparedness / FSF review path, mod-107 owns the artefact regardless of who implemented the underlying component.
- If the reviewer is a platform-security review body (a security architecture group, a CVE-response review, a supply-chain-security review), `ai-infra-security` owns the artefact.
- If the reviewer is an agent-quality review (task success, latency, cost), `senior-agentic-ai-engineer` owns the artefact.

The two questions catch the load-bearing cases. Ambiguity is resolved by naming both roles as co-owners of the artefact.

## The handoffs `senior-agentic-ai-engineer` provides to mod-107

For the EACC to be authorable, the peer role must ship, or have shipped:

- **A tool inventory.** The set of tool functions the agent may invoke. Version, schema, side-effect description, downstream target. mod-107's EACC section 1 is authored *against* this inventory. A missing tool in the inventory becomes a shadow-tool finding.
- **A planner-observable state.** The planner exposes its current goal / sub-goal / budget-remaining in a machine-readable form. mod-107's HITL flow (chapter 04) renders this state; mod-108's monitors classify it.
- **Sub-agent principal-inheritance semantics.** When the planner spawns a sub-agent, the peer role's implementation names the parent's principal, budget, and scope inheritance rules. mod-107's EACC section-5 pins the *contract* the peer role's implementation must satisfy; if the peer role's implementation would silently widen a scope, mod-107 flags the finding.
- **Memory-read / memory-write hooks.** The peer role's memory implementation exposes read / write hooks the wrapper (chapter 03) can intercept. mod-107's EACC section-2 argument-provenance labels rely on the hook labelling the write source.
- **Multi-agent identity claims.** For A2A / MCP-shaped setups, the peer role's implementation carries a cryptographic identity per agent that the wrapper can verify. mod-107's identity-spoofing containment (composing with mod-102 chapter 05 and mod-105 chapter 05) uses this identity.

Findings from mod-107 that route to `senior-agentic-ai-engineer` are typically shaped as *"the containment contract requires this architectural property; the peer role's implementation would benefit from providing it or currently does not provide it."* Examples:

- *"The EACC requires that a sub-agent's budget be at most 1/N of the parent's remaining budget. The peer role's current implementation shares the parent's budget object with the sub-agent; recommend a wrapping API that mints a scoped-child budget object."*
- *"The EACC requires that the memory-write source be labelled with provenance. The peer role's current memory API accepts writes with no source label; recommend a source-required signature."*
- *"The EACC requires that the tool inventory be immutable at run-time. The peer role's current tool-bus supports dynamic tool registration by the model; recommend gating the registration behind an offline authoring path."*

The finding is *routed*, not *fixed inside mod-107*. mod-107 owns the contract; the peer role owns the implementation change.

## The handoffs `ai-infra-security` provides to mod-107

For the EACC to be *enforceable*, the peer role must ship, or have shipped:

- **A sandbox fleet with a stable service-level agreement on isolation.** gVisor / Firecracker / equivalent, base image build pipeline, CVE-patch cadence, propagation-latency SLA for switch-fire (chapter 05). mod-107's EACC section-7 pins the sandbox class the fleet honours.
- **An egress-proxy platform with allow-list authoring hooks.** The proxy the sandbox routes through, with a per-tool allow-list surface mod-107 can populate. mod-107's EACC section-2 for URL / host validation and section-7 for egress control author the allow-lists; the platform provides the runtime.
- **A credential broker.** The runtime service that mints per-tool, per-session credentials from a KMS-held long-lived secret, ideally with downstream identity propagation (OAuth on-behalf-of, cloud STS with session tags). mod-107's EACC section-4 names the broker and its scoping; the broker itself lives here.
- **A workload-identity fabric.** SPIFFE / SPIRE-shaped or cloud-provider-native, so that every wrapper's own identity is cryptographic. mod-107 relies on this identity for the audit-log's principal-capture and for the HITL flow's approver step-up authentication.
- **A WORM audit-log store with retention outlasting the disclosure window.** S3 Object Lock / GCS Bucket Lock / immutable Azure Blob / on-prem WORM appliance, with retention policy documented and audited. mod-107's chapter-03 log stream lands here.
- **A signing service.** The signing key is not in the wrapper (chapter 03 responsibility 5). The service is a peer-role artefact — a KMS-fronted signing API, an HSM-fronted service, or equivalent.
- **A monitoring / telemetry pipeline.** The place where the audit-log stream is consumed; mod-108's monitors sit on top of it. mod-107's wrappers emit to this pipeline; mod-108 processes.

Findings from mod-107 that route to `ai-infra-security` are typically shaped as *"the containment contract requires this platform property; the peer role's platform would benefit from providing it or currently does not provide it."* Examples:

- *"The EACC requires default-deny egress at the sandbox boundary. The current egress proxy defaults allow with a blocklist; recommend a default-deny configuration with an allow-list surface per tool."*
- *"The EACC requires the credential's TTL to be under 15 minutes with automatic rotation. The current broker mints credentials with a 24-hour TTL; recommend a shorter default with an override flag for justified exceptions."*
- *"The EACC requires the audit log to be signed by a key not held by the wrapper. The current stack signs at the wrapper process; recommend moving to the signing service the platform already runs."*
- *"The EACC requires kill-switch propagation under a 5-second latency budget. The current feature-flag platform's propagation is measured at 20 seconds; recommend the switch-specific propagation path the platform designs."*

Again, findings are *routed*, not *fixed inside mod-107*.

## What mod-107 does *not* build

The following are common temptations for the safety-engineering role to build, and they are peer-role scope:

- **A new sandbox platform.** gVisor and Firecracker exist; the operator's platform team maintains a hardened container fleet or one of these. Do not build a third.
- **A new credential broker.** Cloud providers ship one (AWS STS, GCP Workload Identity Federation, Azure Managed Identity); operators run one on top (Vault, SPIFFE / SPIRE). Do not build a fourth.
- **A new tamper-evident log store.** Object-lock storage, transparency-log libraries, and internal WORM stores exist. Do not build a fifth.
- **A new signing service.** KMS-fronted signing APIs exist. Do not build a sixth.
- **A general-purpose observability pipeline.** The operator has one. Ship to it.

The safety-engineering role's craft is the *contract* on top of these components, the *policy* they enforce, the *review* that the enforcement matches the policy, and the *finding routing* when the enforcement drifts.

## What mod-107 *does* build

Concretely, from mod-107's exercises and the artefacts they produce:

- **An EACC for a specific deployment** — sections 1–7 (chapter 01).
- **A sandbox-contract test suite** — adversarial probes that verify the platform honours the invariants (chapter 02, exercise 02).
- **A monitored tool wrapper** for one specific tool — canonicalisation, diff, verification, audit-log emission, tamper-evident stream (chapter 03, exercise 03).
- **An HITL escalation flow** and its bypass-prevention red-team (chapter 04, exercise 04).
- **A kill-switch design and drill** — mode configuration, fan-in graph, incident-response contract (chapter 05, exercise 05).

Each artefact *names* the peer-role components it plugs into. None re-derives them.

## Reading the boundaries against the rest of the module

- **mod-102 (Threat Modelling).** mod-107 is the containment-engineering answer to the ATMD's tool-misuse / privilege-compromise / resource-overload / HITL-bypass entries. mod-102 threat-models; mod-107 contains.
- **mod-103 / 104 (Prompt Injection / Jailbreak).** The prompts and jailbreaks that make the model *want* to invoke a tool are mod-103 / 104's craft. mod-107 makes the runtime narrower than the model, so the *want* does not become *do*.
- **mod-105 (Agent Attack Surface).** The chain-shaped attacks that compose primitives into agent-level failures are mod-105's craft. mod-107 is one of the defensive postures mod-105 measures ASR against.
- **mod-106 (Dangerous-Capability Eval).** The deployed-stack ceiling that reflects mod-107's containment is one of mod-106's ceilings. When the DCER reports a deployed-stack ceiling, it *cites* the EACC as one of the mitigations engaged.
- **mod-108 (Guardrails and Monitors).** mod-108's monitors consume mod-107's audit-log stream and produce fire-votes for the kill switch (chapter 05). mod-107 owns the wrappers and the switch; mod-108 owns the classifier.
- **mod-109 (Safety Cases).** The EACC's runtime enforcement is an *inability leg* in the safety case: *"the deployment is unable to take actions outside the EACC because the wrapper enforces the gate and the audit log is tamper-evident."* mod-107 produces the evidence; mod-109 authors the argument.
- **mod-110 (Control / Deception).** Adversarial-alignment evaluations that reveal the model can *evade* containment (deliberate rate-limit-under-radar behaviour, plans that sequence approvals to sneak past HITL fatigue) widen the residual-risk claim. mod-107 tightens the containment; mod-110 measures whether it holds under adversarial alignment.
- **mod-111 (Automated Red Team).** mod-107 is the *target* mod-111's fuzzers hammer; findings feed EACC updates.
- **mod-112 (Program & Disclosure).** The kill-switch fire runbook, the disclosure workflow after a fire, the EU AI Act Article 73 serious-incident report — mod-112 owns these; mod-107 hands off to them.

## The boundary in one sentence

Mod-107 owns the **Excessive-Agency Containment Contract** — what the containment must be, and the review that the runtime honours it. The peer roles own the **components** the contract runs on: the agent architecture being contained (`senior-agentic-ai-engineer`) and the platform the containment runs on (`ai-infra-security`).

## Common misreadings to avoid

- **"The safety-engineering role builds the sandbox."** No. The safety-engineering role writes the sandbox contract and reviews that the platform honours it. The platform team builds the sandbox.
- **"The peer roles will figure out the containment contract on their own."** No. The peer roles need the contract. Without an EACC, the platform team does not know which controls to enforce and the agent-architecture team does not know which architectural properties to guarantee.
- **"The safety-engineering role can defer everything to the peer roles."** No. The EACC is the safety-engineering role's artefact and must be signed by that role. Peer roles co-sign the components they implement; the *contract* is not delegable.
- **"The finding is finished when we ship the mod-107 change."** No. Findings that route to peer roles are finished when the peer role delivers. The safety-engineering role tracks the finding through the peer-role backlog.
- **"Peer roles review the EACC as a check-the-box step."** No. Peer-role review is what turns the EACC from *"the safety-engineering role thinks this is right"* to *"the platform team and the architecture team agree the contract is enforceable."* A silent peer-role approval is a finding.

## Summary

- Mod-107 owns the **contract**; peer roles own the **components**.
- **`senior-agentic-ai-engineer`** owns the **agent architecture being contained** — the planner, the tool bus, the memory API, the sub-agent decomposition, the multi-agent protocol.
- **`ai-infra-security`** owns the **runtime-hardening platform the containment runs on** — the sandbox fleet, the egress proxy, the credential broker, the workload-identity fabric, the WORM audit-log store, the signing service, the telemetry pipeline.
- Findings from mod-107 that require an architectural change or a platform change are *routed* to the peer role, not built inside mod-107.
- The EACC is the mod-107 artefact; peer-role co-signatures on the components are what make it enforceable.
- The other mod-1XX modules compose with mod-107 explicitly: mod-108 monitors it, mod-109 cites it, mod-110 stress-tests its assumptions, mod-111 fuzzes it, mod-112 discloses against it.
