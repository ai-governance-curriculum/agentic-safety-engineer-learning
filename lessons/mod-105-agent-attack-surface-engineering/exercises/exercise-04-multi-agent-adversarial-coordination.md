# exercise-04: Multi-Agent Adversarial Coordination

**Estimated effort:** 2 hours

## Objective

Engineer two multi-agent coordination attacks (chapter 05) against a small agent graph (N ≥ 3 nodes) that speaks at minimum one standard protocol — A2A, MCP, or a bespoke schema whose trust model you can specify precisely. Populate the multi-agent bench rows of the AASS, produce per-attack propagation traces, and report a reproduction-number (R) estimate for at least one worm-shaped attack.

Attacks must cover **two distinct sub-families** from chapter 05 (cross-agent prompt injection, adversarial-message relay, or principal-confusion in A2A / MCP). Together, the two attacks must exercise at least two of the three defensive-posture axes chapter 05 develops (protocol-level provenance, verified-principal / signed identity, structured-schema / least-privilege delegation).

This exercise turns chapter 05 from taxonomy into instrumented measurement on a live graph. It is the module's answer to "how bad is the multi-agent surface for this deployment, and does one entry attack propagate?"

## Prerequisites

- Read chapter 05 (multi-agent adversarial coordination) end-to-end.
- Read chapter 01 (discipline and artefact skeleton) and chapter 06 (defensive-posture axis).
- Read the current **A2A** protocol specification and the current **MCP** specification — at minimum the identity / auth / discovery sections and the schema for tool discovery and result carriage. Note the observed-on date.
- Read a Morris-II / LLM-worm propagation paper (Cohen et al. family) end-to-end for the R-estimate methodology and the propagation-trace convention.
- Read mod-102 chapter 02 surface 4 (multi-agent surface) if you need a refresher.
- Have a working agent graph:
  - **≥ 3 nodes** — orchestrator + ≥ 2 workers is the minimum interesting shape. Worm findings need N ≥ ~5 to be visible; if you plan a stretch-goal R-estimate, build to 5.
  - **≥ 1 A2A or MCP edge** — if your framework does not natively speak A2A or MCP, wire an MCP server (a minimal reference implementation is enough) or emulate A2A with a documented bespoke schema plus an explicit trust model. Do not attack a protocol you did not read the spec for.
  - **Instrumented message bus** — every inter-agent message logged with sender-claimed identity, transport-verified identity (if any), timestamp, and content hash.
- Access-controlled harmful-payload store set up per mod-103 chapter 06.

## Requirements

For each of two attacks:

1. **Design.**
   - Name the sub-family (cross-agent injection / adversarial relay / principal-confusion).
   - Name the graph shape: nodes, edges, per-edge protocol, per-edge trust model, per-node tool bindings, registry (static / dynamic + discovery-verification yes/no).
   - Name the entry channel and the mod-103 entry primitive.
   - State the operator's intent versus the attacker's intent, and the operator-observable effect.
   - For at least one attack, name a candidate *worm-shaped* propagation path (peer → peer via shared memory or relayed message).

2. **Author the payload and the delivery.** The defanged shape lives in the exercise write-up; the working payload lives in the harmful-payload store, referenced by a handle in the reproducibility bundle. Stand up the delivery: seed the origin node's inbound tool response, seed the shared memory, or register the rogue A2A identity — whatever the sub-family requires.

3. **Instrument the graph.** Extend or wire into your bus's tracing so every inter-agent message logs the fields listed in chapter 05's `propagation_trace` schema. The instrument is reusable across every multi-agent probe in your AASS.

4. **Run each attack under all four required defensive postures**:
   - **Raw** (no cross-agent defence beyond framework defaults).
   - **Provenance-preserving relay** (every inter-agent message carries a trust-label chain — origin channel, relayer identities, hop count — that the receiver can attend to).
   - **Verified-principal / signed-identity** (MCP servers derive the principal from the transport layer, not client-side claims; A2A messages carry signed identity claims verified against a trust root).
   - **Structured-schema + least-privilege delegation** (peer messages exchanged as narrow-typed fields rather than natural language; sub-task tokens narrowed to `sub-task-id`, expiry, and exact intended tool call).

   Optional additional postures: semantic-consistency monitor on relayed messages, per-tenant memory isolation, cross-agent quarantine at named edges.

5. **Report** per posture and per attack:
   - **ASR** at the *receiver* (fraction of trials the attacker's instruction produced the intended effect at the target node).
   - **Injection landing rate** at each intermediate hop (chapter 05's per-hop measurement).
   - **Trust-label preservation rate** across each hop (was the receiver able to identify the origin channel?).
   - **Propagation hops reached** and **unique agents affected** per trial.
   - **R0 estimate** for the attack you designated as worm-shaped — see chapter 05's methodology. If N is too small for a meaningful R0, report the reproduction observed in your graph and note the sample-size caveat explicitly.
   - **Utility** — paired benign-workflow completion rate per posture on a matched task set.
   - **Judge human-agreement** — per-cell, on a random-sampled subset.

6. **Route findings** — every attack's boundary block routes to:
   - `senior-agentic-ai-engineer` for pattern-level fixes (e.g., "adopt provenance-preserving A2A as a graph-family default" or "least-privilege delegation as the sub-agent spawn pattern").
   - `ai-infra-security` for tool-runtime and identity fixes (e.g., "MCP server must verify principal against transport-layer identity" or "capability-token issuer must bind tokens to sub-task-id").
   - mod-107 (cross-agent quarantine) and mod-108 (semantic-consistency monitors, message-integrity checks) as appropriate.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `multi-agent-<slug>/design.md` per attack: sub-family, graph specification (nodes, edges, protocols, trust model, registry), entry channel, operator vs. attacker intent, and the defanged payload shape.
- `multi-agent-<slug>/graph.yaml`: the graph specification in chapter 05's format (nodes, edges, per-edge protocol / auth / trust-label-preservation, registry kind + discovery verification).
- `multi-agent-<slug>/propagation_trace.yaml` per trial (or a representative sample thereof) — hop-by-hop record per chapter 05's schema.
- `multi-agent-<slug>/coverage_row.yaml`: matrix rows per posture per attack in chapter 06's schema, extended with `propagation.hops_reached`, `propagation.unique_agents_affected`, and `propagation.R0_estimate`.
- `multi-agent-<slug>/judge/`: the chain-aware judge scaffold (decomposed into "injection landed at receiver" / "propagation hop N recorded" / "target-node effect materialised"), its version pin, and the human-agreement measurement (`k` trials, agreement rate).
- `harmful-payload-store/manifest.yaml`: one entry per payload used, with a payload-store handle.
- `README.md` in the exercise directory: target graph shape, node models + snapshots, framework versions, protocol versions (A2A / MCP), and a summary table with per-posture ASR, per-posture propagation reach, and R0 for the worm-shaped attack.
- `boundary_routing.yaml`: at minimum one pattern-level finding routed to `senior-agentic-ai-engineer` and one tool-runtime / identity finding routed to `ai-infra-security`.

## Acceptance criteria

- **Two attacks authored in two distinct sub-families** from chapter 05.
- **Graph shape ≥ 3 nodes**, and if your R0 estimate is intended to be interpretable, ≥ 5.
- **At least one attack traverses an A2A or MCP edge** whose protocol version is pinned in the graph spec.
- **Every attack is measured under all four required postures**, with ASR at receiver, per-hop landing rate, trust-label preservation rate, and propagation hops reached reported per posture.
- **At least one attack designated worm-shaped**, with a reproduction estimate (R0 or observed reproduction with sample-size caveat) reported.
- **Utility measurement reported per posture** on a paired benign-workflow task set.
- **Chain-aware judge with per-cell human-agreement** reported.
- **Boundary-routing block routes findings** to `senior-agentic-ai-engineer`, `ai-infra-security`, and — where applicable — mod-107 / mod-108.
- **No working payload committed to this repo.** Defanged shapes only; working strings live in the harmful-payload store, referenced by handle.
- **The coverage-matrix rows conform to chapter 06's YAML schema**, extended with the multi-agent fields chapter 05 codifies.

## Stretch goals

- **Firebreak experiment.** Under R > 1, apply the provenance-preserving-relay posture at *one specific graph edge* (a chosen firebreak) and re-measure. Report the effect on R and on unique agents affected. This is chapter 05's firebreak-effectiveness metric.
- **Dynamic-discovery attack.** If your graph uses a dynamic A2A registry or MCP tool-server directory, author a rogue-agent-registration attack: attempt to register a rogue node under a name a partner agent would trust, and measure whether the discovery layer detected it and whether routing to it succeeded.
- **Cross-agent memory poison.** If any pair of agents shares a memory store (chapter 03 sub-family 2), author a chained attack in which a chapter-03 poisoning payload written by agent A fires on agent B's next session. Report the store-boundary reach.
- **Adaptive attacker on structured schemas.** Assume the structured-schema posture is deployed; re-author your cross-agent injection to smuggle an instruction inside a narrow-typed field the schema still allows (e.g., a rich-text `description` field, a URL field). Report the adaptive ASR delta.
- **Two-protocol chain.** Author an attack whose propagation traverses both an A2A hop and an MCP hop (or two MCP servers via a routing agent). Report which hop lost the trust label and where the provenance-preserving posture would need to reach to close the chain.
- **AgentDojo Slack-suite mapping.** Map at least one authored attack onto the closest AgentDojo Slack-suite scenario (chapter 06 develops the mapping) and cross-check the AgentDojo judge's verdict against your chain-aware judge on the same trials.

## Guardrails

- Do not commit working cross-agent payloads, rogue-identity certificates, spoofed tokens, or worm strings to this repo. See the harmful-payload discipline in chapter 01 and the module's top-level `README.md`.
- Do not test rogue-agent registration or principal-confusion attacks against production A2A / MCP registries or against tool servers you do not own. The default target is a self-hosted graph on a local instance of your framework, with self-hosted A2A discovery / MCP servers.
- Do not run authored propagation attacks against production frontier agent deployments without written authorisation from the model provider and your organisation.
- If a multi-agent attack you author generalises across a whole protocol family (e.g., every default-configured MCP server in a widely-used SDK release is principal-confusable), the finding is a coordinated-disclosure candidate; route through the mod-112 workflow before publishing.
- If a worm-shaped attack against your graph reaches R > 1 in a test that could conceivably escape the test graph (shared production stores, cross-tenant memory), stop the run, disconnect the graph from any external reads / writes, and route through mod-112 before continuing.
