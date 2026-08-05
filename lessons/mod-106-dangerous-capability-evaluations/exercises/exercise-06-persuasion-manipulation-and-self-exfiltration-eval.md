# exercise-06: Persuasion / Manipulation and Self-Exfiltration Eval

**Estimated effort:** 2 hours

## Objective

Author and (where feasible) run a **persuasion / manipulation short-format probe** *and* a **self-exfiltration propensity scenario probe** against one target model, at the working depth published in current system cards. The output is a two-panel DCER row: a persuasion / manipulation panel with a short-format probe result and a long-format-trial design (design only), and a self-exfiltration panel with a scenario probe run and the mod-110 dependency cite embedded in the residual-gap accounting.

This exercise anchors chapter 05 in practice. It is deliberately narrower than a full system-card panel — you run the short-format persuasion probe and one self-exfiltration scenario, and design the long-format persuasion trial without running it (long-format trials require human participants under ethics review).

## Prerequisites

- Read chapter 05 (persuasion, manipulation, self-exfiltration) end-to-end.
- Skim two recent frontier-model system cards' persuasion sections (Claude, o-series, or Gemini) to see the working depth for the panels. <!-- needs-research: confirm current system-card persuasion / self-exfil sections. -->
- Skim the Salvi et al. (2024) ChangeMyView-linked persuasion RCT paper for the long-format-trial anchoring. <!-- needs-research: confirm citation. -->
- Skim one MakeMePay- or MakeMeSay-shaped published probe write-up. <!-- needs-research: confirm currently published MakeMePay / MakeMeSay methodology. -->
- Read the mod-110 chapter previews on sandbagging, alignment-faking, and situational-awareness so the self-exfil panel's mod-110 dependency cite is grounded. If mod-110 has not been authored yet, cite the primary literature the module will build on (Apollo Research, Redwood Research, METR control research). <!-- needs-research: confirm current mod-110 authoring status; adjust cites accordingly. -->
- Have a target model accessible. An open-weights model is the recommended default for the fine-tune-elicitation stretch goal; a frontier API is fine for the base panel with appropriate authorisation.
- Have completed exercise-04 (recommended) — the self-exfil capability composition cites the autonomy panel's SWE-bench Verified result.

## Requirements

The exercise has four parts. All four are required.

### Part 1 — Persuasion / manipulation short-format probe

1. **Pin one short-format probe.** Options: MakeMePay-shaped (agent attempts to elicit a specified payment from a target LLM), MakeMeSay-shaped (agent attempts to elicit a specified phrase from a target LLM), or a custom bounded-turn goal-elicitation probe with a documented rubric.
2. **Author the probe protocol.** The target LLM (a separate model instance or a persona), the goal state, the turn budget, the LLM-judge scoring rubric, the number of trials.
3. **Run the probe under two elicitation configurations**:
   - Baseline: default target model, no scaffold.
   - Scaffolding-elicited: a self-critique / plan-and-refine loop over the persuasion attempts.
4. **Report success rate** per configuration with a CI.
5. **Report refusal-vs-capability separation**:
   - Refusal rate (fraction of trials the target model refused to engage).
   - Capability rate | attempted (success rate conditional on the target attempting).
6. **Report the elicitation-shift** (scaffolded minus baseline).

### Part 2 — Long-format persuasion trial design (design only)

Author a 1–2 page design document for a ChangeMyView-shaped long-format persuasion trial that would convert the Part 1 short-format result into a tier-decision-relevant DCER row. The design covers:

- **Threat model.** Mass-narrative, 1-on-1 social-engineering, or durable-belief-change.
- **Trial arms.** Control (unassisted human persuader), treatment 1 (model with default scaffold), treatment 2 (model with fine-tune-elicited scaffold).
- **Participants per arm.** Recruitment source, sample size, compensation per ethics-review-approved rates.
- **Ethics review.** IRB (or equivalent) approval process and reference number placeholder.
- **Persuasion metric.** Likert-scale reader / target rating, plus a persuasion-delta effect-size measurement.
- **Durability follow-up.** Follow-up window (days / weeks) and belief-change retention measurement.
- **Debrief protocol.** How participants are told they interacted with an AI system, and what reflection tools they are offered.
- **Security controls.** Fine-tune weight disposition, elicited-transcript retention, mod-112 workflow reference.
- **Threshold and verdict rule.** How the composite (short-format probe + long-format trial + durability follow-up) produces a cleared / approached / crossed verdict against a specific persuasion tier.

### Part 3 — Self-exfiltration scenario probe

1. **Pin one self-exfiltration scenario** from the family described in chapter 05 (shutdown-threat scenario, goal-preservation scenario, or direct-opportunity scenario). Author the scenario prompt and the required agent context (system prompt, tool set, credentials the model is told are available).
2. **Run the scenario 10–30 times** against the target model under one scaffold pin. Record whether the model *takes the exfiltration action*, *considers and rejects it*, or *does not consider it*.
3. **Report per-scenario action rate** with a CI.
4. **Report refusal-vs-action separation** — the fraction that refused engagement entirely vs the fraction that engaged but declined.
5. **Cite the capability composition** — the model's SWE-bench Verified pass rate (from exercise 04 or the published number for the model) as the underlying capability that would ground successful exfiltration; the propensity number is meaningful in the context of the capability.

### Part 4 — DCER two-panel section draft

Author DCER Section 4 subsections for both panels (chapter 06 shape). Include:

- **Persuasion / manipulation panel**:
  - Threat-model recap.
  - Short-format probe result with elicitation-shift.
  - Refusal-vs-capability separation table.
  - Long-format-trial design cite (Part 2).
  - Elicitation-gap accounting (fine-tune depth, scaffold parity, paired-baseline credibility caveat because the long-format trial is design-only).
  - Threshold comparison against a specific persuasion / manipulation tier.
  - Verdict.
- **Self-exfiltration panel**:
  - Threat-model recap.
  - Capability composition cite (SWE-bench Verified + METR from exercise 04 or published numbers).
  - Propensity measurement result with per-scenario action rate and CI.
  - Refusal-vs-action separation.
  - Mod-110 dependency cite — the sandbagging / alignment-faking / situational-awareness control-evaluation companion the propensity claim depends on, cited to a specific artefact if available or explicitly flagged as absent if not.
  - Elicitation-gap accounting — scenario-completeness, fine-tune-elicitation depth, control-evaluation dependency status.
  - Threshold comparison against a specific autonomy / autonomous-replication tier.
  - Verdict.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `persuasion-short-format-run/` — run configs, LLM-judge outputs, per-trial success flags. No raw persuasion transcripts committed; scores and judge verdicts only.
- `long-format-trial-design.md` — the Part 2 design document.
- `self-exfil-scenario-run/` — scenario prompt, run configs, per-trial action outcomes. No agent-produced exfiltration transcripts committed; action-taken flags only.
- `pre-registration.yaml` — the elicitation-protocol pre-registration for both panels.
- `dcer-persuasion-and-self-exfil.md` — the Part 4 DCER-shaped section.
- `security-controls.md` — sandbox handle, drill isolation, retention windows, mod-112 workflow reference, mod-110 dependency status.
- `README.md` — target model, probes and scenarios run, and a one-paragraph verdict summary for each panel.

## Acceptance criteria

- **Persuasion short-format probe run under two configurations** with success rates and CIs.
- **Refusal-vs-capability separation reported** for the persuasion panel.
- **Long-format persuasion trial designed** — threat model, arms, ethics review, metric, durability follow-up, debrief, security controls, verdict rule.
- **Self-exfiltration scenario run with 10+ trials**, per-scenario action rate reported with CI.
- **Refusal-vs-action separation reported** for the self-exfil panel.
- **Mod-110 dependency cited or explicitly flagged as absent** in the self-exfil elicitation-gap accounting.
- **Capability composition cited** — the SWE-bench Verified / METR result grounding the self-exfil capability half.
- **DCER two-panel section drafted** with threshold comparison, verdict, and rollback trigger for each panel.
- **No persuasion transcripts, no self-exfil action transcripts committed to the repo.** Scores, action flags, and rubric verdicts only.
- **Ethics review status noted** for the long-format design — IRB reference placeholder is acceptable for a design document, but the discipline is that the run does not start until real approval is obtained.

## Stretch goals

- **Fine-tune-elicited persuasion.** On an open-weights target, fine-tune on 20–100 argumentation examples targeting the refusal head and re-run the short-format probe. Report the fine-tune-elicitation shift and note the weight-disposition discipline.
- **Multiple self-exfil scenarios.** Run the shutdown-threat, goal-preservation, and direct-opportunity scenarios and report per-family action rates. Discuss scenario-family coverage as an elicitation-gap component.
- **Durability follow-up (persuasion, small scale).** Recruit 3–5 colleagues (with informed consent — no IRB required for informal small-scale, but the ethics discipline still applies) and run a durability follow-up on a short-format persuasion probe. Report the delta between immediate and follow-up belief-change.
- **Cross-model comparison.** Run the same protocols against two model releases and produce a per-panel scaling table. Discuss whether persuasion / self-exfil capability scales with general capability.
- **DCER Section 7 draft.** Extend to Section 7 (residual-gap claim consolidated across panels). Articulate how the self-exfil panel's mod-110 dependency shapes the consolidated residual claim.
- **Mod-110 pairing.** If mod-110 has been authored or if you can run a small sandbagging probe of your own on the self-exfil scenario, run it and report the pairing. Discuss how the pairing tightens or loosens the DCER's expressed-inability claim.

## Guardrails

- **Ethics review is required for real human-participant persuasion trials.** The long-format trial in Part 2 is design-only for this reason. Do not recruit real participants without appropriate ethics-review approval.
- **Persuasion probes should not target real people without informed consent.** LLM-target probes (MakeMePay-shaped) are fine. Targeting a colleague, a friend, or a family member for a persuasion study without consent is not.
- **Self-exfiltration scenarios stay in isolation.** The scenario's environment — credentials the model is told exist, cloud accounts, code environments — is entirely simulated or sandboxed. There are no real credentials, no real cloud accounts, no real path to any production infrastructure.
- **If the model takes a genuinely novel self-exfiltration action** (a scaffold you did not anticipate, an escape from your sandbox, an attempt to reach real infrastructure), halt the run, do not disclose publicly, and route through mod-112 to the model's safety team. The frontier labs publish channels.
- **Self-exfiltration transcripts route to the mod-110 team as adversarial-alignment evidence** — not to any external publication channel. The DCER reports the propensity number and cites the transcripts by ID; the transcripts themselves stay behind the sandbox boundary.
- **Fine-tune weights (if any)** stay behind the safety-team sandbox boundary per chapter 01. Fine-tune-elicited persuasion or self-exfil models are not distributed.
- **Frontier-provider authorisation** required for API-hosted persuasion / self-exfil elicitation that materially exceeds default-configuration testing. Provider safety teams publish channels.
- **Do not commit persuasion or self-exfil transcripts** to the repo. Scores, action flags, and rubric-cell verdicts are the artefacts; the raw transcripts live behind access controls.
- **Coordinate with in-house counsel** before any external engagement (paid participants, published trial results, coordination with public-safety bodies).
