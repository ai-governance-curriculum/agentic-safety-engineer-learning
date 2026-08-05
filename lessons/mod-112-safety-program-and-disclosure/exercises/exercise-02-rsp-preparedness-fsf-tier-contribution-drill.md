# exercise-02 — RSP / Preparedness / FSF tier contribution drill

**Estimated effort:** 3 hours
**Prerequisite chapters:** 02 (primary), 01 (for FSPC framing). Interfaces: mod-106 (DCER evidence), mod-107 (EACC posture), mod-108 (monitoring posture), mod-109 (safety case), mod-110 (deception / residual-risk).

## Objective

Draft the **tier-decision memo** the safety-engineering role delivers into a Responsible-Scaling / Preparedness / Frontier-Safety-Framework decision body for **one** concrete model version under **one** chosen framework. The memo carries chapter 02's four load-bearing safety-engineer contributions — **pre-registered thresholds**, **elicitation-methodology defence**, **rollback contract**, and **deferred-deployment recommendation** — in chapter 02's nine-section memo shape. The exercise produces the artefact chapter 03's system card reduces from, chapter 04's regulator submission references, and chapter 05's incident response revises.

## Problem statement

Pick **one model version** and **one framework**. The model version is either a real published model your organisation ships (name it explicitly with version + build ID) or a plausible surrogate (a hypothetical next-rev of a real family — mark all downstream claims as surrogate). The framework choice is one of:

- **RSP-shape ASL memo** — Anthropic Responsible Scaling Policy vocabulary (ASL-N determination, RSO signoff slot). <!-- needs-research: pin the current published RSP version and the ASL threshold-language it defines. -->
- **Preparedness-shape scorecard memo** — OpenAI Preparedness Framework vocabulary (scorecard grade per tracked category, Preparedness Committee signoff slot). <!-- needs-research: pin the current Preparedness Framework version and the tracked-category taxonomy. -->
- **FSF-shape CCL memo** — Google DeepMind Frontier Safety Framework vocabulary (Critical Capability Level crossing, mitigation-tier engagement, Safety Board signoff slot). <!-- needs-research: pin the current FSF publication and the CCL / mitigation-tier taxonomy. -->

Write the memo *in the chosen framework's vocabulary*. Cross-framework mapping lives in the thresholds artefact per chapter 02's cross-framework-alignment note; the memo body does not translate.

The memo is not a solution paper. It is a decision surface delivered to a tier-decision body that will read it cover-to-cover in one sitting and either accept, defer, or condition the tier claim. Chapter 02's two named discipline failures — **pre-registration failure** (thresholds without a numeric surface or a named elicitation configuration) and **defer-recommendation avoidance** (memos that never defer) — are the failure modes this drill is written to prevent. If your memo carries thresholds that a reviewer cannot falsify, or carries only *deploy* recommendations across your career of memo-authoring, you have not produced a decision surface; you have produced a rubber-stamp.

Payload discipline is inherited from mod-111 chapter 06: threshold-crossing evidence citations point to DCER run IDs, monitor-fire counts point to mod-108 log IDs, red-team findings point to mod-111 replay-bundle IDs — the memo does not inline elicited harmful outputs, working attacker prompts, or judge rationale text quoting harm.

## Requirements

Produce four artefacts.

### Artefact A — `tier-memo-<model>-<framework>.md`

The tier-decision memo. 8–15 pages. All nine sections chapter 02 pins, in order.

- **§1 Executive summary** — one page. The tier claim in the chosen framework's vocabulary (ASL-N / scorecard-grade per category / CCL-crossing determination), the deploy / defer / gated-launch recommendation, and the two or three most load-bearing evidence citations (by artefact ID + version pin, not by prose paraphrase).
- **§2 Model and scope** — the model version (provider version tag + build ID + weights hash if open-weight), the intended deployment surfaces (API, product, partner integration — enumerated), sibling models this memo compares against, and the scope of what the memo does *not* claim (out-of-scope surfaces, out-of-scope capabilities, out-of-scope framework versions).
- **§3 Pre-registered thresholds and evidence** — the thresholds pre-registered *before* the eval (per Artefact B), the eval results, and the crossings. Each threshold section names the benchmark, the benchmark-version pin, the elicitation configuration hash, the numeric threshold, the confidence-interval width required for the point to be actionable, and the observed point + interval. Threshold status is one of `not_crossed`, `crossed`, `ambiguous_within_ci`.
- **§4 Elicitation-methodology defence** — the four mod-106 chapter 03 elicitation regimes (fine-tuning-on-domain-data, tool-augmented, multi-agent decomposition, expert-in-loop) named per capability domain, with the *strength* of each run named. Per capability: the methodology-gap argument (form: *"we believe current elicitation is within [gap] of the strongest attainable elicitation by a determined adversary with [resources]"*), third-party corroboration where available (METR, UK AISI, US AISI, FMF peer citations with pinned publications), and failed-elicitation disclosure (regimes attempted that did *not* uplift the score — a reviewer will ask if you tried them; disclose first).
- **§5 Deployment mitigations engaged** — mod-107 EACC posture summary in the shape the tier decision consumes (capability-gate configuration ID, tool inventory pin, sandbox posture), mod-108 monitoring posture (predicted monitor-fire rates, escalation flow, human-in-the-loop routing), and the specific mitigations engaged *because of* the threshold-crossings in §3.
- **§6 Rollback contract** — the per-surface rollback plan (per Artefact C), rollback authority, rollback latency budget per surface, user-facing shape, drill cadence commitment, and pointer to the most-recent drill log. An untried rollback is called out as such.
- **§7 Deferred-deployment recommendation** — the honest recommendation. Either an explicit *defer* / *narrow-launch* / *capability-gated launch* / *monitored launch* with a named deferral trigger, named deferral shape, named exit criterion (form: *"what evidence, produced by whom, on what timeline, would let the recommendation flip"*), and cost frame (launch-slip cost vs tier-under-estimate cost); or an explicit *deploy* recommendation that names *why defer was NOT chosen* — the specific evidence, methodology-gap size, and mitigation posture that made deploy the honest call over defer. A memo whose §7 says "deploy" without engaging the defer alternative is a memo that has practised defer-recommendation avoidance.
- **§8 Residual-risk statement** — what the memo does *not* claim to have ruled out. mod-110 deception-detection findings that entered the residual-risk column, mod-111 red-team findings that survived triage without crossing a pre-registered threshold, elicitation-gap-uplift risk, monitor-blind-spot risk. Chapter 02 pins this as the safety-case honesty section per mod-109 chapter 03 pattern.
- **§9 References** — version-pinned to primary sources. Framework version pin (RSP / Preparedness Framework / FSF), benchmark version pins, third-party evaluation publication pins, DCER / EACC / monitor / safety-case / red-team artefact IDs.

Memo signature block: signed by the safety-engineering lead who authored it, tier-body reviewer slot named (RSO / Preparedness Committee chair / Safety Board equivalent), delivery timestamp scheduled **5+ working days ahead of the tier body's meeting** per chapter 02.

The memo body does not inline the thresholds table or the rollback-contract spec in full; those are Artefacts B and C. §3 and §6 reference their artefacts by name, summarise, and cite.

### Artefact B — `tier-memo-<model>-thresholds.yaml`

The pre-registered thresholds table. **At least three thresholds.** Each threshold pre-registered *before* the eval it grades — the pre-registration date and the git SHA of this file at pre-registration are recorded per threshold entry.

Per threshold:

- `threshold_id` — stable identifier the memo §3 references.
- `capability_domain` — the mod-106 capability domain (CBRN-uplift, cyber-offense, autonomy-uplift, persuasion, self-exfiltration, agentic-misuse, etc.). <!-- needs-research: pin the framework-specific taxonomy for tracked categories — Preparedness and FSF use different vocabularies. -->
- `benchmark` — the named benchmark (e.g. `cybench`, `re-bench`, `swe-bench`, `wmdp`, a lab-internal DCER harness). <!-- needs-research: pin the exact benchmark identifier and its current published version. -->
- `benchmark_version` — the pinned version + git SHA of the benchmark harness at the eval it grades.
- `elicitation_config` — the mod-106-shape elicitation configuration, with `prompt_template_hash`, `tool_set` (named tools + versions), `budget` (wall-clock, tokens, cost cap), `regime` (one of `fine_tuning_on_domain_data`, `tool_augmented`, `multi_agent_decomposition`, `expert_in_loop`), and `elicitation_config_hash` for the composite.
- `numeric_threshold` — the specific number a passing score must exceed to trigger next-tier consideration.
- `ci_width_required` — the confidence-interval width the eval must achieve for the point to be actionable (per chapter 02's *"confidence interval, not point estimate"* rule).
- `observed_point` — the eval result. `null` if the threshold is pre-registered but not yet evaluated.
- `observed_ci` — the observed CI at the required width. `null` if not yet evaluated.
- `threshold_status` — `not_crossed | crossed | ambiguous_within_ci | not_yet_evaluated`.
- `cross_framework_mapping` — the equivalent thresholds in the *other two* frameworks (RSP ↔ Preparedness ↔ FSF) so chapter 01's cross-framework mapping is legible even though the memo is written in one vocabulary.
- `pre_registration_date` and `pre_registration_git_sha` — the audit trail that the threshold was pre-registered before the eval.
- `evidence_pointer` — the DCER run ID / Inspect log ID / third-party publication citation that grades this threshold.

A threshold entry whose `numeric_threshold` is prose, whose `elicitation_config_hash` is missing, or whose `ci_width_required` is unset is a pre-registration failure — chapter 02's first named discipline failure. The exercise wants each threshold in the artefact to pass this test explicitly.

### Artefact C — `tier-memo-<model>-rollback.yaml`

The rollback-contract spec. One entry per deployment surface enumerated in memo §2.

Per surface:

- `surface_id` — the deployment channel identifier (`api_public`, `product_web`, `product_mobile`, `partner_<name>`, etc.).
- `rollback_plan` — what specifically rolls back: `model_version_pin`, `tool_inventory_snapshot`, `capability_gate_config` (mod-107 EACC ID), `system_prompt_hash`, `memory_posture`. Named per surface.
- `rollback_target` — the pinned known-good state the surface reverts to (prior model version + build ID, prior EACC config ID, prior monitor posture ID).
- `latency_budget` — time from decision to rollback-live: minutes for API model-pin swaps, hours for tool-inventory changes, days for partner-integration re-negotiation. Per chapter 02.
- `rollback_authority` — who is authorised to initiate. Almost always the RSO / Preparedness Committee chair / Safety Board equivalent *plus* a nominated on-call safety-engineer with pre-authorised authority for time-sensitive fires (chapter 05 scope).
- `user_facing_shape` — what users see: service-degradation notice, capability-availability change, outage. Communications-team preparation is named as a dependency.
- `drill_cadence` — the commitment (at least quarterly per chapter 02; more frequent for higher-tier surfaces).
- `most_recent_drill_log_pointer` — the drill artefact ID + timestamp. If `never_drilled`, the entry is flagged and the memo §6 states so — an untried rollback is not defensibly a rollback.
- `comms_prep_pointer` — the pre-authored user-facing communications template ID.
- `escalation_contact_chain` — the ordered on-call chain during a rollback event.

A surface entry with `most_recent_drill_log_pointer: never_drilled` is not disqualifying, but the memo §6 must call it out; a reviewer at chapter 02's level asks for the drill log first.

### Artefact D — `tier-memo-<model>-runbook.md`

A memo-authoring runbook, 800–1200 words, covering:

- **Framework choice.** Which of RSP / Preparedness / FSF, and why. What would have shifted the vocabulary — a different regulator audience, a different internal signoff body, a different sibling-memo lineage. Chapter 02's *"the memo is written in the chosen framework's vocabulary"* is the frame; the runbook justifies the choice explicitly.
- **Threshold pre-registration process.** How the three-plus thresholds in Artefact B were pre-registered *before* the eval — who signed off, on what date, against which git SHA. The specific mechanism the organisation uses to prevent post-hoc numeric tuning of the threshold once the observed score is known (chapter 02's *"pre-registration moves the negotiation before the result is known, when incentives are neutral"*). Name the pre-registration failure the runbook was written to prevent.
- **Elicitation-methodology defence rationale.** Per capability domain in scope, which of the four mod-106 chapter 03 regimes ran, which achieved the strongest elicitation, and the methodology-gap argument in your own words. Third-party corroboration invoked (METR / UK AISI / US AISI / FMF peer) and where corroboration was *unavailable* — those are the capabilities carrying the largest gap uplift. Failed-elicitation regimes disclosed (a reviewer will otherwise ask).
- **Rollback drill status.** Which surfaces have been drilled, which have not, and — for undrilled surfaces — the plan and calendar. A rollback contract that names ten surfaces and drills two is a contract with eight aspirational entries; the runbook is where that honesty lives.
- **Defer-vs-deploy weighing.** The explicit reasoning that produced memo §7's recommendation. If §7 is *defer*, name the trigger, shape, exit criterion, and cost frame in the runbook's language. If §7 is *deploy*, name the specific evidence, methodology-gap size, and mitigation posture that made defer the wrong call. Chapter 02's second named discipline failure — defer-recommendation avoidance — is what this section prevents; state explicitly whether your career of memo-authoring (or the surrogate lineage this memo joins) has recommended defer at least once, and if not, name the pattern.
- **Memo tier-body review calendar.** Delivery timestamp, tier-body meeting timestamp, and the 5+ working-day lead-time chapter 02 requires. Any peer-review the memo went through before delivery (safety-engineering leadership co-signature, legal review of the rollback contract's user-facing shape, communications review of the user-facing shape).
- **Threats to validity.** Surrogate-model concerns (if the memo grades a plausible-surrogate rather than a real ship), benchmark-version drift between pre-registration and eval, elicitation-methodology gap the memo has under-estimated, monitor-fire-rate prediction uncertainty in §5, rollback drill staleness for surfaces not drilled recently, framework-version drift between the memo and the tier body's currently-recognised framework version.
- **Handoff to sibling exercises.** Exercise 03 reduces this memo into a system card (name which §s are disclosed and which are not — audience separation is chapter 03's craft). Exercise 04 references this memo in AISI / Article 55 submissions (name the redaction posture the regulator submission carries relative to the memo). Exercise 05 revises this memo during incident response (name the trigger conditions per chapter 05).

## Starter guidance

- **Read chapter 02 end-to-end before writing a single line of the memo.** The four load-bearing contributions and the nine-section memo shape are not optional; a memo missing one section is a memo the tier body cannot use.
- **Pick the smallest scope that will fit in 8–15 pages.** One model version, one framework, one intended deployment surface family. Chapter 02's *"the memo is 8–15 pages, is read cover-to-cover by the tier body in a single sitting"* is the length discipline.
- **Pre-register the thresholds first, populate the observed scores second.** Author Artefact B's `pre_registration_date` and `pre_registration_git_sha` before you author any `observed_point`. If the temptation to numeric-tune appears after you see the observed score, that is the discipline failure chapter 02 named; the runbook records how you resisted.
- **Do not write threshold prose that a reviewer cannot falsify.** *"The model shows dangerous capability in cyber-offense"* is a mood. *"On [named benchmark] at version [X] with elicitation configuration [hash], a passing score exceeding [Z] with CI width [W] triggers ASL-4 consideration"* is a threshold. Every threshold in Artefact B passes the falsifiability test.
- **The elicitation-methodology defence is where mod-106's DCER work becomes tier-legible.** A memo §4 that cites *"we ran DCER"* is under-specified; a memo §4 that names the four regimes, the strongest-attainable-elicitation gap per capability, and the third-party corroboration is defensible.
- **The rollback contract is engineered, not aspirational.** Every surface has a drill log or an explicit `never_drilled` flag. Chapter 02's *"an untried rollback is a rollback that will not work under pressure"* is the frame.
- **Write §7 last, but not sanitised.** The defer / deploy call is the load-bearing decision surface of the memo. A §7 that hedges — "we recommend continued monitoring" without engaging deploy-or-defer — is defer-recommendation avoidance rendered as procedural language. Chapter 02 named this failure; the runbook is where you record that you did not commit it.
- **Cross-framework mapping lives in Artefact B, not in the memo body.** The memo speaks the chosen framework's vocabulary; the mapping is legible in the artefact for the reader who translates.
- **Do not inline harmful evidence.** Threshold-crossing evidence citations point to DCER run IDs, mod-108 log IDs, mod-111 replay-bundle IDs. The memo, thresholds file, rollback file, and runbook contain no working attacker prompts, no elicited harmful completions, no judge rationale text quoting harm. Payload discipline is inherited from mod-111 chapter 06.
- **Third-party corroboration is worth citing even when it partially disagrees.** A METR or UK AISI publication that measured a lower elicitation gap than yours is *stronger* evidence than no third-party citation; disclose the disagreement in §4 rather than omitting the citation.
- **The signature block is not decorative.** The tier-body reviewer slot is a real person or role. The 5+ working-day lead-time is a real calendar entry. If the memo is a drill, the runbook names it as a drill; if the memo is for a real ship, the reviewer slot names the RSO / Committee chair / Board equivalent.
- **Mark every unverifiable claim.** Framework version specifics, benchmark score specifics, regulator statutory-threshold specifics — where the exercise cannot verify from primary sources at authoring time, tag `<!-- needs-research: ... -->` per chapter 02's own primary-sources block discipline.

## Acceptance criteria

- Artefact A memo has all **nine sections** chapter 02 pins, in the pinned order, and fits within the 8–15 page range. §1 executive summary is one page; §7 is not omitted (a *deploy* recommendation still requires §7 with the defer-not-chosen reasoning).
- Artefact A memo is written in **one framework's vocabulary** (RSP ASL / Preparedness scorecard / FSF CCL), chosen and named in §1. Cross-framework translation does not appear in the memo body.
- Artefact A memo signature block names the safety-engineering lead (author) and the tier-body reviewer slot (RSO / Preparedness Committee chair / Safety Board equivalent), with a delivery timestamp **5+ working days ahead** of a named tier-body meeting.
- Artefact B pre-registers **at least three thresholds**, each with `benchmark`, `benchmark_version`, `elicitation_config_hash` (composed from `prompt_template_hash` + `tool_set` + `budget` + `regime`), `numeric_threshold`, `ci_width_required`, and `cross_framework_mapping`. Each threshold has a `pre_registration_date` and `pre_registration_git_sha` recorded *before* any `observed_point`.
- No threshold in Artefact B has a prose-only `numeric_threshold`, a missing `elicitation_config_hash`, or an unset `ci_width_required` — chapter 02's first discipline failure (pre-registration failure) is explicitly avoided per entry.
- Artefact A §4 elicitation-methodology defence names the **four mod-106 chapter 03 regimes** (fine-tuning-on-domain-data, tool-augmented, multi-agent decomposition, expert-in-loop) per capability domain, carries a methodology-gap argument per capability, cites third-party corroboration where available, and discloses failed-elicitation regimes.
- Artefact C rollback-contract spec has **one entry per deployment surface** enumerated in memo §2, with `rollback_plan`, `latency_budget`, `rollback_authority`, `drill_cadence`, and `most_recent_drill_log_pointer` per surface. Undrilled surfaces flagged `never_drilled` and called out in memo §6.
- Artefact A §7 carries an **honest defer / narrow-launch / capability-gated launch / monitored launch / deploy** recommendation. If *defer* or narrower, the trigger, shape, exit criterion, and cost frame are named. If *deploy*, the reasoning explicitly engages *why defer was NOT chosen* — chapter 02's second discipline failure (defer-recommendation avoidance) is explicitly avoided.
- Artefact A §8 residual-risk statement names what the memo does *not* claim to have ruled out, sourced from mod-110 deception-detection findings, mod-111 red-team findings that survived triage, elicitation-gap uplift risk, and monitor-blind-spot risk.
- Artefact D runbook (800–1200 words) covers framework choice, threshold pre-registration process, elicitation-methodology defence rationale, rollback drill status, defer-vs-deploy weighing, memo tier-body review calendar, threats to validity, and handoff to exercises 03 / 04 / 05.
- **Payload discipline satisfied.** No working attacker prompts, no elicited harmful completions, no judge rationale text quoting harm appears in any of Artefacts A, B, C, D. Threshold-crossing evidence is referenced by DCER run ID / Inspect log ID / mod-111 replay-bundle ID only.
- Every unverified factual claim (framework version specifics, benchmark identifier specifics, regulator statutory-threshold specifics, third-party publication specifics) is marked `<!-- needs-research: ... -->` per chapter 02's own primary-sources-block discipline.
- Handoff notes at the end of the runbook name **exercise 03** (system-card reduction, audience separation), **exercise 04** (AISI / Article 55 regulator submission with redaction posture), and **exercise 05** (incident-response revision trigger conditions).

## Stretch goals

- **Author the tier-body decision addendum shape.** Chapter 02 pins the tier-body's decision as an *addendum* appended to the memo — the vote (where applicable), the conditions attached, and the tier-decision's effective date. Draft the addendum template your memo expects the tier body to complete; this is the artefact mod-109's safety case cites and chapter 03's system card discloses.
- **Cross-map the memo into the other two frameworks.** Take your RSP memo and sketch the Preparedness scorecard §s the same evidence would populate (or vice versa). The exercise is not to rewrite the memo — it is to demonstrate that the pre-registered thresholds' `cross_framework_mapping` in Artefact B is load-bearing. Chapter 01's cross-framework mapping is the frame.
- **Draft a threshold-revision proposal.** A pre-registered threshold that a reviewer disputes needs a revision path — chapter 02 does not detail one, but a mature safety-engineering practice has one. Draft the shape: what evidence justifies revising a threshold, who signs off, what audit trail is retained. The mechanism must not defeat the pre-registration discipline it is amending.
- **Sketch the rollback drill artefact.** Pick one surface from Artefact C with `most_recent_drill_log_pointer: never_drilled`, and author the drill-plan document that would produce the drill log. Chapter 02 does not detail the drill artefact shape; drafting one previews the chapter 05 incident-response drill vocabulary.
- **Author a memo-review checklist for a peer safety-engineering lead.** The tier-body reviewer will read the memo, but a peer safety-engineering lead reads it first. Draft the ~one-page checklist the peer walks through — the four load-bearing contributions, the nine sections, the two named discipline failures, the payload-discipline check. This is the artefact that catches the failure modes chapter 02 named before the tier body ever sees the memo.

## Deliverable location

Personal notes or private repo. Do **not** commit the tier-decision memo, the thresholds YAML, the rollback-contract YAML, or the runbook into this course repo. The memo carries elicitation-methodology detail, rollback-authority chains, and residual-risk statements that are internal-audience artefacts; premature disclosure is exactly the chapter 03 audience-separation failure this module was built to prevent. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference memo skeleton. Threshold-crossing evidence (DCER run outputs, mod-108 monitor logs, mod-111 red-team replay bundles) lives in your org's evidence store per mod-111 chapter 06 payload discipline; the committed artefact here is the memo referencing evidence by ID, never inlining evidence content.
