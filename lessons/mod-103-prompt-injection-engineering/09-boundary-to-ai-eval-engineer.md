# 09 — The Boundary to `ai-eval-engineer`: Traces, Judges, and Plumbing

## Motivation

The Prompt-Injection Evaluation Harness (PIEH) from chapter 06 does not stand alone. It consumes app-side infrastructure the operator's product team already builds: request tracing, LLM-judge scaffolds, eval-gated CI, online-eval dashboards, cost accounting. That infrastructure belongs to the **`ai-eval-engineer`** role (level 30, AI Engineering family), which is a *peer* to this agentic-safety track — not a subordinate, not an upstream provider you can dictate to.

This chapter names the boundary. It says which artefacts the PIEH consumes from the peer and which artefacts it *produces* for the peer. Getting the boundary right is what turns "the red team throws findings over the wall" into "the safety harness and the app-side eval harness feed each other."

## Why the boundary is called out

Three failure modes that appear when the boundary is not made explicit:

1. **The safety team rebuilds trace infrastructure the app team already has.** Every red-team engineer who has ever written their own trace format ends up with a half-broken re-implementation of the peer's harness. The peer's harness is more mature, has more usage, is calibrated against production data — reuse it.
2. **The safety team's judges are not the app team's judges.** The app team is measuring RAG faithfulness, answer quality, cost, latency. The safety team is measuring injection ASR, refusal rate, exfil detection. If the two never share judge scaffolding, both teams pay the LLM-judge implementation cost twice and neither gets the benefit of shared calibration.
3. **The safety findings don't gate app releases.** The safety team finds a regression on a defence layer, the app team ships anyway because the CI gate is theirs alone. The boundary contract fixes this by naming the safety-side signal that must feed the app-side release gate.

## The peer role's actual scope

The `ai-eval-engineer` role — see the [ai-eval-engineer-learning curriculum](https://github.com/ai-engineering-curriculum/ai-eval-engineer-learning) for the definitive scope — owns:

- **App-side traces** — the request/response record, tool-call log, retrieval log, latency and cost breakdown, per-request metadata. The unit of debugging and evaluation.
- **LLM-judge scaffolds** — the reusable code for prompting, calling, and calibrating LLM-judges. Include agreement measurement against human labels, cost accounting, judge-vs-judge disagreement analysis.
- **RAG evaluation** — retrieval relevance, faithfulness of grounded answers, retrieval-quality regressions.
- **Eval-gated CI/CD** — the mechanism by which failing evaluations block a release. Includes stack-sensitive gating (which tests run at PR time vs. nightly vs. pre-release).
- **Online evaluation** — the production-time evaluation: sampling live traffic, running judges over it, dashboarding drift, alerting on regressions.
- **Cost / latency / quality trade-off management** — the three-way balance app-side product teams live with.

This module *consumes* items 1, 2, 4, and 5 from that list. Items 3 (RAG eval) and 6 (cost / latency / quality) are the peer's concerns and this module does not encroach.

## Consumed artefact 1 — Traces

The PIEH needs a per-request trace that includes:

- The **exact prompt sent to the model** — after chat template rendering, with role delimiters, with retrieval snippets concatenated.
- The **exact model output** — before any post-processing, exactly as emitted.
- The **tool-call log** — every tool call attempted, its arguments (as-emitted and as-executed after any argument-validator normalisation), its response (raw and as-injected-back-into-context).
- **Retrieval log** — which vector store, which query embedding, which top-k results, which metadata.
- **Memory read/write log** — what was read from memory, what was written back.
- **Peer-agent messages** if any, with signed identity claims.
- **Reproducibility fields** — model ID + snapshot, decoding config, template ID, timestamps.

The PIEH assumes the peer role's trace format already contains fields 1–4 for product reasons (debugging, quality eval); this module contributes fields 5–7 to the shared trace schema. The schema is jointly owned; changes go through a shared review workflow.

### The specific requirements the PIEH adds

- **Trust labels on every string.** Each concatenated segment in the rendered prompt carries a label (`user`, `system`, `retrieval:<source>`, `tool_response:<tool>`, `memory:<store>`, `peer:<agent_id>`). The PIEH's injection findings will name a segment and its label; without labels, findings are unroutable.
- **Deterministic template rendering.** Given the trace, the rendered prompt can be reconstructed exactly. Non-deterministic template rendering (whitespace variation, arbitrary ordering) is a bug the PIEH will file.
- **Raw-vs-sanitised split.** For every segment, both the raw source and the sanitised form that entered the model are logged. Sanitiser effectiveness is measured on the delta.

## Consumed artefact 2 — Judge scaffolds

The PIEH's per-cell judges (chapter 06) sit on top of the peer role's judge infrastructure:

- **Prompt template management** — the judge prompt is versioned, its git history tracked, its diffs reviewed.
- **Model choice** — the judge model is selected per-cell with a documented rationale (cost, capability, sibling-family bias avoidance).
- **Calibration harness** — a shared library that measures judge–human agreement on labelled samples, tracks calibration over time, and alerts on drift.
- **Cost accounting** — judge tokens per run, judge cost per release, cost-per-finding.

The safety-specific extension the PIEH contributes:

- **Adversarial-robust judge design.** Injection judges must not themselves be steerable by the injection payloads they score. This is an explicit requirement peculiar to the safety domain — the peer's product-quality judges do not face adversarial payloads in the same way.
- **Success-criterion rubrics for injection primitives.** The rubric for "task-override succeeded" (the agent invoked attacker tool, produced benign user summary) is authored here; the rubric-scoring machinery is the peer's.
- **Judge-vs-defence separation.** The PIEH's judge measures whether the payload succeeded; the operator's defence (chapter 07) measures whether the response should have been rejected. They must be different judges even if they use the same model — otherwise the judge's rating is contaminated by the defence's opinion.

## Consumed artefact 3 — Eval-gated CI

The PIEH plugs into the peer's CI machinery:

- **PR-time evaluation** — a fast subset (Tier A cells only, small trial counts) runs on every PR to catch obvious regressions.
- **Nightly / pre-release evaluation** — the full harness (Tier A + B, larger trial counts, adaptive-attack budget) runs before every release.
- **Release-gate policy** — the peer's CI honours the PIEH's release-gate output as a blocker; the release-gate policy itself is negotiated with the safety team (this module) and the deployment-tier decision from mod-102.

The safety-specific extension:

- **Severity-tiered gating.** A finding above a critical severity (Tier A cell, adaptive-ASR regression above a threshold, deployment-tier-relevant) blocks *any* release regardless of the app-side quality picture. Non-critical findings feed a queue with a deadline.
- **Boundary with mod-112 disclosure.** Findings above the tier that triggers external disclosure (mod-112) are held out of the auto-published release notes until the disclosure workflow completes.

## Consumed artefact 4 — Online evaluation

The PIEH's coverage matrix is exercised in pre-production, but injection also happens in production and the PIEH's monitoring feed should live on the peer's online-eval dashboard:

- **Production sampling.** A random-sampled subset of production traces is scored by a lightweight injection-judge (fewer trials than the pre-release version, but running continuously).
- **Trend dashboards.** Injection-ASR (or its production-signal proxy) per cell over time; alerts on regressions.
- **Feedback into the offline harness.** Novel injection shapes observed in production are lifted into the offline harness's replay bundle (with the harmful-payload discipline preserved).

## Produced artefact 1 — Injection findings feed

The PIEH emits findings the peer role consumes:

```yaml
finding_id: PIEH-2026-04-14-0007
matrix_cell: {primitive: task_override, channel: retrieval.webpage, obfuscation: base64}
defence_stack_version: v3.2.1
static_asr: 0.14
adaptive_asr: 0.42
gap: 0.28
severity: critical
root_cause: sandwich_reminder_bypass
sample_payloads:  # by ID, in the external store
  - PIEH-IND-WEB-TASK-011
  - PIEH-IND-WEB-TASK-014
recommendation: promote sandwich reminder to include an explicit tool-call-provenance check
owner_teams:
  - retrieval-team
  - safety-classifier-team
release_gate: block
```

The peer's CI ingests these and routes.

## Produced artefact 2 — Regression fixtures

Every finding that gets fixed becomes a **regression fixture**: a named test in the harness that must continue to pass. The peer's CI machinery honours the regression fixture set as a subset of the pre-release evaluation.

The fixture set is the module's institutional memory: "we saw this once, we won't see it again unsupervised."

## Where the boundary tightens with other modules

- **mod-105 (Agent-Specific Attack Surface Engineering)** — extends the harness to tool-abuse chains and agent-emergent behaviour. Shares the same peer boundary and trace schema.
- **mod-107 (Excessive-Agency Containment Engineering)** — owns the boundary-controls layer of the defence catalogue. Consumes PIEH findings to prioritise its containment work.
- **mod-108 (Frontier-Scale Guardrails and Safety-Monitor Engineering)** — owns the classifier-defence layer. Consumes PIEH findings to prioritise its classifier training.
- **mod-111 (Automated and Scaled Red-Teaming)** — owns the industrialised attacker loops. Feeds the PIEH's adaptive-ASR numbers.
- **mod-112 (Frontier-Safety Program and Disclosure)** — owns the disclosure workflow. Consumes PIEH findings above the disclosure threshold.

## Governance-side coordination note

The boundary to `ai-eval-engineer` is horizontal (peer-to-peer within the org). The vertical coordinates matter too:

- **Upstream (level 25 `ai-risk-engineer`)** — the prerequisite role provides the general harm-model craft the PIEH's cell selection references. See mod-102's grounding for the harm-corpora fluency.
- **Sideways (level 30 `model-evaluation-engineer`)** — provides statistical calibration methodology (best-of-N CI, judge calibration). The PIEH's confidence intervals derive from that methodology.
- **Sideways (level 35 `security` / `ai-infra-security`)** — provides sandbox, egress, and credential-scoping infrastructure. The boundary-controls layer of the defence catalogue leans on this.
- **Downstream (level 50 `senior-ai-governance-architect`)** — consumes the PIEH's release-gate policy as an input to the org-wide control-library design.

The boundary contract makes each of these coordinations concrete rather than aspirational.

## Common engineering mistakes at this boundary

- **Rebuilding the peer's infrastructure.** The temptation is real — the peer's schema may be missing a field the PIEH needs. File a schema change; do not fork.
- **Under-specifying the trace schema deltas.** The trust-label and raw-vs-sanitised requirements are load-bearing. Get them in writing.
- **Sharing judge implementations without sharing judge calibration.** A shared judge without shared calibration is a shared bug. Fund the calibration harness explicitly.
- **Not gating releases on adaptive numbers.** The release-gate should honour adaptive-ASR, not static, once adaptive is running for a cell.
- **Missing the online-eval feedback loop.** Production surprises must lift into the offline harness or the harness silently misses whichever shape the current attacker prefers.
- **Not tracking mod-112 disclosure state on findings.** A finding that has entered disclosure has a state machine; the peer's release notes must respect it.

## Summary

- The PIEH does not stand alone: it consumes traces, judge scaffolds, eval-gated CI, and online-eval from the peer `ai-eval-engineer` role, and it produces an injection-findings feed and a regression-fixture set the peer's CI honours.
- The trace schema needs trust labels, deterministic template rendering, and raw-vs-sanitised splits — those are the safety-side additions to the shared trace schema.
- Judge scaffolding is shared; adversarial-robust judge design is the safety-side responsibility on top.
- Release gating is severity-tiered and coordinates with mod-112's disclosure workflow.
- Related boundaries (mod-105 attack surface, mod-107 containment, mod-108 classifiers, mod-111 automation, mod-112 disclosure) share the same trace schema and finding format, so the contract compounds across modules.
