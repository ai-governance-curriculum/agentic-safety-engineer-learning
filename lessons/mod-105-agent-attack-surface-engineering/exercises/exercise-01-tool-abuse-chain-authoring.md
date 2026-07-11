# exercise-01: Tool-Abuse Chain Authoring

**Estimated effort:** 3 hours

## Objective

Author two complete tool-abuse chains (chapter 02) against one concrete tool-integrated agent of your choice, produce per-chain reproducibility bundles, and populate the tool-abuse-chain rows of the AASS coverage matrix (chapter 06) across the four defensive postures. The output is the seed of your AASS's chain library and the first cells of your coverage matrix.

This exercise anchors chapter 02 in practice. It does *not* require you to invent novel exfil paths — the value is in demonstrating that you can construct, run, judge, and route findings for a chain end-to-end.

## Prerequisites

- Read chapter 01 (discipline and artefact skeleton) and chapter 02 (tool-abuse chains) of this module.
- Read Debenedetti et al. (2024) AgentDojo and Zhan et al. (2024) InjecAgent — at least the abstract, methodology, and evaluation sections.
- Read Greshake et al. (2023) end-to-end.
- Have a working target agent: either (a) an AgentDojo suite instance (workspace, banking, travel, or Slack) or (b) your own tool-integrated agent built on LangGraph / AutoGen / a bespoke stack. The chosen target must have at minimum a read-shaped tool (mail, search, calendar-read, or ticket-read) and a write-shaped tool (mail-send, calendar-write, ticket-create, or file-write).
- Access-controlled harmful-payload store set up per mod-103 chapter 06 (a private repo, an S3 bucket with logged access, or an equivalent).

## Requirements

Author **at least two chains** falling in **two different sub-families** from chapter 02 (unintended tool invocation via injection, argument smuggling, chained exfiltration through search + email + calendar, credential-theft chains through code-interpreter, blast-radius amplification via file-system tools). At least one chain must involve **≥ 3 tool calls** across the chain trace.

### For each chain

1. **Design.** Name the sub-family, the entry channel, the entry primitive (map it to a mod-103 primitive ID), the chain of state transitions (planner steps and tool calls), and the operator-observable effect.
2. **Author the payload as a defanged shape in the exercise write-up and a working version in the harmful-payload store.** The working payload lives in the store, referenced by a handle in your reproducibility bundle.
3. **Implement the delivery mechanism.** For webpage entries, stand up a local HTTP server that serves the poisoned page. For tool-response entries, seed the mock API's response with the payload. For memory / peer-agent entries, populate the store or the sender.
4. **Run the chain against the target agent under each of the four defensive postures** from chapter 06:
   - Raw.
   - Tool-response sanitisation (specify the concrete configuration — spotlighting, structured extraction, instruction-stripping regex, etc.).
   - Capability gates (specify the concrete configuration — capability-token issuer, scope binding, expiry policy).
   - HITL (specify the concrete UX — argument-diff surfacing, approval-fatigue budget, timeout policy).
5. **Judge the outcome per trial.** Author or reuse a chain-aware judge scaffold that decomposes the verdict into (injection landed, tool call fired with smuggled content, effect materialised). Report human-agreement on a random-sampled subset.
6. **Produce the reproducibility bundle** in the schema from chapter 02.
7. **Populate the AASS coverage-matrix rows** for the chain in the schema from chapter 06.
8. **Author the boundary-routing block** per chapter 07 — which peer / module owns each proposed remediation class.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `chain-<slug>/design.md` — the four-part description (entry, planner decision, tool calls, effect) for each chain, plus the defanged payload shape.
- `chain-<slug>/reproducibility_bundle.yaml` — the schema from chapter 02.
- `chain-<slug>/coverage_row.yaml` — the matrix row from chapter 06.
- `chain-<slug>/judge/` — the judge scaffold, its version pin, and the human-agreement measurement (`k` trials, agreement rate).
- `chain-<slug>/boundary_routing.yaml` — the routing block from chapter 07.
- `harmful-payload-store/manifest.yaml` — one manifest entry per payload used, with a payload-store handle. The manifest lives in this repo *only if* it does not include the payloads themselves; payloads live in the access-controlled store.
- A `README.md` in the exercise directory naming the target agent, the model + snapshot, the framework version, and the two chains authored, with a table summarising per-posture ASR.

## Acceptance criteria

- **Two chains authored in two different sub-families**, at least one with ≥ 3 tool calls.
- **Every chain has a full reproducibility bundle** and a chain-aware judge with a per-cell human-agreement number.
- **Every chain is measured under all four postures**, with ASR reported per posture.
- **The utility-security composite is reported** — paired benign-task utility rate per posture.
- **The boundary-routing block routes each finding to a specific peer / module** with a proposed remediation class.
- **No working payload is committed to this repo.** Defanged shapes only; working strings live in the harmful-payload store, referenced by handle.
- **The coverage-matrix rows conform to chapter 06's YAML schema.**

## Stretch goals

- **Compose postures.** Add rows for `sanitisation + capability_gates` and `sanitisation + capability_gates + hitl` and report the deltas. Chapter 06 develops the composed-posture pattern.
- **Adaptive attacker.** Re-author each chain *against* the posture that most reduced its raw ASR — an adaptive attempt in the mod-104 chapter 08 sense. Report the adaptive ASR delta.
- **AgentDojo mapping.** If your target is not an AgentDojo suite, map your chains onto the closest AgentDojo attack-ID and note the deviations. If your target *is* an AgentDojo suite, run the AgentDojo attack catalogue in full at your compute budget and report the joint coverage-matrix rows.
- **InjecAgent mapping.** For each authored chain, identify the closest InjecAgent case category and cross-check whether your judge and InjecAgent's default judge produce different verdicts on the same case. Investigate any disagreement.
- **Non-obvious sub-family.** Author a fifth-sub-family chain (blast-radius amplification via file-system tools) — the sub-family least covered by public benchmarks — and add it as a bonus row with a note on which AASS-internal bench cell it fills.

## Guardrails

- Do not commit working chains or exfil payloads to this repo. See the harmful-payload discipline in chapter 01 and the module's top-level `README.md`.
- Do not run authored chains against production frontier APIs unless you have written authorisation from the model provider and your organisation. Local models and self-hosted API mocks are the default target for this exercise.
- If a chain elicits genuinely novel policy-violating output against a frontier provider, route the finding through the mod-112 coordinated-disclosure workflow before publishing anything about it.
