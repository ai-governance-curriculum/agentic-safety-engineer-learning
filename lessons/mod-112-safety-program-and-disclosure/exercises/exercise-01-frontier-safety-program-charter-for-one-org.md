# exercise-01 — Frontier Safety Program Charter for one org

**Estimated effort:** 2 hours
**Prerequisite chapters:** 01 (primary); helpful: 02, 03, 04, 05, 06.

## Objective

Author a **Frontier Safety Program Charter (FSPC)** for **one concrete organisation**, populating all seven sections chapter 01 names — Scope, Cadence, Artefact register, Decision register, Sign-off routing, Incident-response contract, Certifications and program assurance — with the four safety-eval touch-points, the three named decision bodies, and an explicit cross-framework mapping across Anthropic RSP, OpenAI Preparedness, and Google DeepMind FSF. The FSPC is the module-level artefact the rest of mod-112 builds against; this exercise is what turns chapter 01's operating-rhythm frame into a signed document a Safety Board, a regulator, or an external auditor can read.

## Problem statement

Chapter 01's load-bearing claim is that a frontier-safety program is an *operating rhythm*, not a document library — four safety-eval touch-points, a named artefact register, a named decision register, a named sign-off routing. Most organisations get this wrong on the first attempt by publishing a policy document that names principles without naming the *cadence, the artefacts, the bodies, or the sign-off chain*; the document then sits on a shelf while the actual decisions are made ad hoc. This exercise forces the discipline: pick one organisation, name the rhythm end-to-end, and produce an artefact that binds the organisation to the rhythm.

Pick **one organisation**. A plausible surrogate is fine: a specific frontier lab whose posture you shadow (author the FSPC as if you were its RSO), a hypothetical mid-size lab with a specific model family and jurisdiction footprint you can defensibly describe, or your own employer with explicit disclosure permission. Toy orgs (`AcmeAI`, `SafetyCorp`) are out of scope — chapter 01's *"if a director cannot point at a document that says here is when we evaluate, here is who signs, here is what we disclose"* framing means the signer must be plausible, the artefacts must be plausible, and the decisions must be plausible. Pick **one primary framework** (RSP, Preparedness, or FSF) as the operating framework; the cross-framework mapping to the other two is what preserves cross-industry legibility.

Every section of the FSPC is authored end-to-end; a stubbed section is a hard fail. Chapter 01's cadence, decision register, and sign-off routing are the specific shapes each corresponding section must adopt.

## Requirements

Produce four artefacts.

### Artefact A — `fspc-<org>.md`

The Frontier Safety Program Charter itself. Named per your chosen organisation (`fspc-anthropic-shadow.md`, `fspc-midsize-lab-eu.md`, `fspc-<your-employer>.md`). All seven sections in the chapter-01 order, populated end-to-end. A version tag, a **named signer** (the RSO or equivalent for the chosen org), and named **co-signature slots** (Preparedness Committee chair, Safety Board chair, legal, external affairs) per chapter 01's sign-off discipline.

- **Section 1 — Scope.** The organisation, the model families in scope, the deployments in scope (API, first-party product, partner integration, on-prem), and the jurisdictions in scope (US, EU, UK, other). Named. Deployments and jurisdictions the FSPC *does not* cover — with the artefact that covers them named — per chapter 01's *"silent skip is a finding"* discipline (inherited from mod-111 chapter 01).
- **Section 2 — Cadence.** All four safety-eval touch-points from chapter 01's *"four safety-eval touch-points"* section: pre-training, mid-training, pre-deployment, post-deployment. Each touch-point names its question, its audience, its artefacts, and — the load-bearing addition beyond chapter 01's exposition — the **calendar / event trigger** that fires it for this specific org (per-run pre-commit, per-checkpoint at a named compute threshold, per-version-launch, monthly Preparedness Committee review + quarterly Safety Board review). Pre-registered thresholds for the mid-training touch-point are named as slots (the threshold values themselves live in chapter 02's tier-decision memo — cite the slot, do not invent the numbers).
- **Section 3 — Artefact register.** The register from chapter 01's *"artefact register"* section, populated for this org. At minimum: pre-training safety memo, mid-training checkpoint report, pre-deployment safety case (mod-109), system card (chapter 03 / exercise 03), AISI pre-deployment submission (chapter 04 / exercise 04), Article 73 serious-incident report template (chapter 04 / exercise 04), quarterly Safety Board report. Each row carries `owner`, `reviewer`, `cadence`, `retention`. Artefact B is the machine-readable form; the section-3 prose summarises and points at Artefact B.
- **Section 4 — Decision register.** Every decision class the program supports mapped to exactly one primary body per chapter 01's *"ambiguity here is a governance failure"* rule. At minimum: RSP tier / ASL determination, Preparedness scorecard rating, FSF CCL threshold determination, deploy / defer / rollback verdict, rollback-fire trigger, incident-class assignment (chapter 05 / exercise 05). Each decision names the body with authority, the evidence input, and the escalation path. Artefact C is the machine-readable form; section 4 prose summarises.
- **Section 5 — Sign-off routing.** The three named bodies from chapter 01's *"sign-off routing"* section — Responsible-Scaling Officer, Preparedness Committee, Safety Board — instantiated for this org (or the org's specific equivalents; some orgs collapse two bodies, some split three ways). Each body names its members (by role, not by real names unless you have permission), its meeting cadence, its quorum requirement, and its escalation contract to the next body up. If the org operates under Preparedness (no RSO in the RSP sense), name the Preparedness-Committee chair as the accountable individual per chapter 01's *"single accountable individual"* discipline.
- **Section 6 — Incident-response contract.** The class taxonomy, the triage timeline, the coordination roster (safety-engineering on-call, legal, communications, external-affairs, AISI liaison, regulator liaison), and the disclosure shape. This section is the handoff to exercises 04, 05, and 06; the FSPC names the contract, and those exercises populate the details (Article 73 templates in exercise 04, coordination playbook in exercise 05, regression-fixture back-feed in exercise 06). Cite each explicitly.
- **Section 7 — Certifications and program assurance.** External assurance the program leans on and the assurance calendar. At minimum reference ISO/IEC 42001 (AI management system), IAPP AIGP-carrying reviewer coverage, ForHumanity IAA (EU AI Act conformity), BABL AI (algorithmic-audit training). Each named with the assurance cadence (annual audit, biennial recertification, per-launch external review). Exercise 07 is the deep dive; the FSPC names the posture.

The FSPC references its **primary framework** (RSP, Preparedness, or FSF) directly and includes a **cross-framework mapping table** — one row per decision class in section 4, columns for the primary framework's vocabulary and the other two frameworks' equivalent vocabularies. Chapter 01's *"regulators, AISI, and Frontier Model Forum peers will read across all three"* is the rule that forces the table.

### Artefact B — `fspc-<org>-artefact-register.yaml`

The machine-readable artefact register — the chapter 01 section-3 register in structured form. One YAML document. Per artefact row:

- `artefact_id` — stable identifier (`pre-training-safety-memo`, `system-card`, `article-73-incident-report`, etc.).
- `title` — human-readable name.
- `owner` — the role that authors (safety-engineering lead per model family, incident-response lead, communications, external counsel).
- `reviewer` — the role or body that signs off (RSO, Preparedness Committee, Safety Board, legal, external affairs).
- `cadence` — the calendar / event trigger (per training-run pre-commit, per checkpoint, per version launch, per incident, monthly, quarterly).
- `retention` — how long the artefact is retained and where (7 years minimum, model-lifetime + 3 years, regulator-required retention for Article 73 submissions, public-permanent for system cards).
- `source_module` — the sibling module that authors the underlying evidence (mod-106 for DCER, mod-107 for EACC, mod-108 for monitor-fire reports, mod-109 for safety cases, mod-110 for deception findings, mod-111 for red-team back-feed).
- `downstream_consumers` — the bodies, regulators, or external parties the artefact routes to (RSO, Safety Board, US AISI, UK AISI, EU AI Office, Frontier Model Forum peers, public).
- `sensitivity` — public / internal / restricted / regulator-only; keyed to the disclosure discipline chapters 03 and 05 develop.

Every artefact row cites the mod-112 chapter that owns its authoring shape. A row without an owning chapter is either out of FSPC scope or a scoping gap the runbook flags.

### Artefact C — `fspc-<org>-decision-register.yaml`

The machine-readable decision register — the chapter 01 section-4 register in structured form. One YAML document. Per decision row:

- `decision_id` — stable identifier (`asl-tier-determination`, `preparedness-scorecard-rating`, `fsf-ccl-threshold`, `deploy-defer-rollback-verdict`, `rollback-fire`, `incident-class-assignment`).
- `decision_class` — one of `tier-determination | deploy-defer | rollback-fire | incident-class-assignment | pre-registered-threshold-cross`. Chapter 01's exposition is the source vocabulary.
- `primary_body` — the single body with authority (RSO, Preparedness Committee, Safety Board, or the org's equivalent). Chapter 01's *"ambiguity here is a governance failure"* means exactly one body per row.
- `evidence_input` — the artefact(s) from Artefact B the body reads to make the decision (tier-decision memo, safety case, DCER, EACC summary, monitor-fire report).
- `escalation_path` — the next body up if the primary body defers or a threshold is crossed that the primary body's charter does not cover.
- `framework_mapping` — for each of RSP / Preparedness / FSF, the vocabulary the decision maps to in that framework (`asl-3`, `high-risk in cyber category`, `ccl-3 in autonomy`). Where a framework has no equivalent, mark `n/a` explicitly.
- `sla` — the maximum time from evidence-ready to decision-made. A decision with no SLA is a decision that drifts.

Every decision row has exactly one `primary_body`; a row with two primary bodies is a hard fail per chapter 01's discipline.

### Artefact D — `fspc-<org>-runbook.md`

A short (~800–1200 word) runbook covering:

- **Org choice rationale.** The one organisation you picked, the disclosure posture (shadowed public lab / hypothetical / employer-with-permission), why this org is FSPC-worthy rather than a toy. Chapter 01's *"named"* discipline for scope, bodies, and signer is the frame.
- **Primary framework choice rationale.** Why you picked RSP, Preparedness, or FSF as the operating framework for this org. Chapter 01's three-framework exposition is the source; the runbook argues the choice against the org's model family, jurisdiction footprint, and existing governance surface.
- **Cross-framework mapping approach.** How you populated the section-4 mapping table. Where a framework has no clean equivalent for a decision class (RSP has no scorecard rating; Preparedness has no ASL; FSF is domain-scoped rather than scorecard-scoped), the runbook records the modelling choice explicitly. `<!-- needs-research: ... -->` markers travel with the mapping where the current published framework versions do not settle the question.
- **Sign-off routing rationale.** Why the three bodies (or the org's specific equivalents) sit where they do in section 5. If the org collapses RSO + Preparedness Committee chair into one role, argue why; if the org splits Safety Board into a technical committee + a fiduciary committee, argue why. Chapter 01's *"single accountable individual"* rule is what each argument tests against.
- **Incident-response contract rationale.** The class taxonomy the section-6 contract adopts, the triage timeline (informed by chapter 05), and the coordination roster. Handoffs to exercises 04, 05, 06 are named; the FSPC does not re-teach the details those exercises own.
- **Certifications posture rationale.** Which external assurance frames the section-7 posture invokes and why. Chapter 06 (and exercise 07) are the deep dive; the runbook records the shape.
- **Sibling-module interface map.** The specific interfaces to mod-106 (DCER as pre-training / mid-training / pre-deployment evidence), mod-107 (EACC as containment posture in the safety case and system card), mod-108 (monitor-fire stream as post-deployment evidence), mod-109 (safety case as the pre-deployment-touch-point artefact), mod-110 (deception findings as residual-risk input), mod-111 (red-team findings as regression-fixture back-feed). Chapter 01's interfaces section is the checklist.
- **Threats to validity.** Framework-version drift over the FSPC's own review cycle (RSP, Preparedness, FSF all revise; the AI Act's implementing acts accrete), the single-signer-in-this-exercise problem (co-signature slots are named but not collected), scope creep as the org adds a model family or a jurisdiction, and the *shelf-artefact* failure mode chapter 01's misreadings section warns about.

## Starter guidance

- **Author the seven headers first, then populate.** Chapter 01 names all seven; write the skeleton before writing content. A skeleton with all seven headers is the diagnostic that no section will be silently skipped.
- **Pick a real org (or defensible surrogate) before writing any content.** Chapter 01's *"named"* discipline is what makes the FSPC legible; if you cannot name the org, the model families, the deployments, and the jurisdictions, the FSPC has no scope.
- **Match cadence triggers to the org's actual release rhythm.** A lab that ships a model family every 6 months has a different cadence than one that ships every 18 months. Pre-training touch-point triggers on compute-commit; mid-training on a pre-registered compute checkpoint; pre-deployment on version-launch calendar; post-deployment on monthly + quarterly review cycles. Chapter 01's touch-point exposition names the questions; the FSPC names the triggers.
- **Cross-framework mapping is a table, not prose.** Chapter 01's three-framework exposition suggests the columns; each row is a decision class from section 4. Where a framework does not carry the decision cleanly, write `n/a` and cite the `<!-- needs-research: ... -->` marker. Do not invent an equivalence.
- **Sign-off routing must name accountable individuals by role.** *"The safety team"* is not a body; *"the RSO"* is. Chapter 01's *"single accountable individual for RSP-shaped tier decisions"* is the discipline. Use role names (RSO, Preparedness Committee chair, Safety Board chair) rather than personal names unless you have explicit disclosure permission.
- **The artefact register cites the mod-112 chapter that owns each artefact.** A row that cannot cite an owning chapter is either out of scope or a gap. Chapter 01's interfaces section names the sibling-module inputs; chapters 02–06 name the mod-112 outputs.
- **Do not fill in exercise 02's tier-decision memo, exercise 03's system card content, exercise 04's regulator submission body, or exercise 05's incident-response playbook.** The FSPC names the *slot* those exercises fill; the exercises fill it. Chapter 01's *"exercises 02–06 ship the internals"* is the boundary.
- **The incident-response section is a contract, not a playbook.** The class taxonomy, the triage timeline, the coordination roster, the disclosure shape — those are the contract. The playbook itself lands in exercise 05. Chapter 01's *"class taxonomy, triage timeline, coordination roster, disclosure shape"* enumeration is the section-6 checklist.
- **Certifications section names the frame; exercise 07 names the individuals.** ISO/IEC 42001, IAPP AIGP, ForHumanity IAA, BABL AI — the FSPC names which frames the program leans on and the assurance cadence. Exercise 07 authors the hiring-signal planner and the per-reviewer certification map.
- **Version-pin every framework and regulatory reference.** Chapter 01 and the resources file both warn that RSP, Preparedness, FSF, and the AI Act revise. A `references` block at the FSPC foot lists the version + URL + retrieval-date for each. Chapter 01's *"version-pin all of these in the FSPC's references section"* is the rule.
- **A signer without a co-signature is a signer alone.** Chapter 01's sign-off routing names three bodies; the FSPC's signature block names slots for all three. This exercise does not require collecting the co-signatures — it requires the slots to exist.
- **Length target.** The FSPC document itself is dense but bounded — 3000–5000 words. Artefact B and C are structured; the runbook is the argumentation surface. Chapter 01's exposition length is a reasonable prose-density calibration.
- **Do not invent framework thresholds, regulator statistics, or specific incident precedents.** Where a specific claim is required but not verifiable from the primary sources, mark `<!-- needs-research: ... -->` inline in the prose. The FSPC is a *signable* artefact; unverified claims are a signature liability.

## Acceptance criteria

- All seven FSPC sections (Scope, Cadence, Artefact register, Decision register, Sign-off routing, Incident-response contract, Certifications and program assurance) are populated end-to-end in `fspc-<org>.md`. No section is stubbed.
- Section 1 names one concrete organisation, model families, deployments, and jurisdictions in scope, plus explicit out-of-scope items with their covering artefact named.
- Section 2 populates all four safety-eval touch-points (pre-training, mid-training, pre-deployment, post-deployment) with question, audience, artefacts, and calendar / event trigger per touch-point.
- Section 3 lists at minimum the pre-training safety memo, mid-training checkpoint report, pre-deployment safety case, system card, AISI submission, Article 73 report template, and quarterly Safety Board report, each with owner / reviewer / cadence / retention. Artefact B carries the machine-readable form.
- Section 4 maps each decision class (tier determination, deploy / defer / rollback, rollback fire, incident-class assignment) to exactly one primary body. Ambiguity (two primary bodies on one row) is a hard fail. Artefact C carries the machine-readable form.
- Section 5 names the three sign-off bodies (RSO, Preparedness Committee, Safety Board, or the org's specific equivalents) with members-by-role, meeting cadence, quorum, and escalation contract.
- Section 6 carries the class taxonomy, triage timeline, coordination roster, and disclosure shape, with explicit handoffs to exercises 04, 05, and 06.
- Section 7 names ISO/IEC 42001, IAPP AIGP, ForHumanity IAA, BABL AI as the assurance frames the program leans on, with the assurance calendar named. Exercise 07 handoff is cited.
- The FSPC names **one primary framework** (RSP, Preparedness, or FSF) and includes a **cross-framework mapping table** with one row per decision class from section 4 and columns for all three frameworks.
- `fspc-<org>-artefact-register.yaml` structures the section-3 register with `artefact_id`, `title`, `owner`, `reviewer`, `cadence`, `retention`, `source_module`, `downstream_consumers`, `sensitivity` per row.
- `fspc-<org>-decision-register.yaml` structures the section-4 register with `decision_id`, `decision_class`, `primary_body`, `evidence_input`, `escalation_path`, `framework_mapping`, `sla` per row. Each row has exactly one `primary_body`.
- `fspc-<org>-runbook.md` (800–1200 words) covers org choice rationale, primary-framework choice rationale, cross-framework mapping approach, sign-off routing rationale, incident-response contract rationale, certifications posture rationale, sibling-module interface map, and threats to validity.
- The FSPC carries a **named signer** (RSO or equivalent) and **named co-signature slots** (Preparedness Committee chair, Safety Board chair, legal, external affairs). Slots may be empty for this exercise; slot names are not optional.
- Explicit interfaces to mod-106 (DCER), mod-107 (EACC), mod-108 (monitors), mod-109 (safety cases), mod-110 (control / deception), mod-111 (automated red-team) are named in the runbook's interface map.
- Every unverified factual claim (specific framework version numbers, specific regulator thresholds, specific published incident classes, specific certification body arrangements) is marked `<!-- needs-research: ... -->` inline in the prose so it blocks auto-merge.
- A `references` block at the FSPC foot version-pins every framework and regulatory instrument cited (RSP, Preparedness, FSF, EU AI Act Articles 55 / 56 / 73, ISO/IEC 42001, NIST AI RMF where cited) with URL and retrieval date.

## Stretch goals

- **Author the mid-training pre-registered-threshold slot with worked slot definitions (not values).** Chapter 01 names pre-registered thresholds as the discipline that makes mid-training load-bearing; the FSPC can name the *shape* of a threshold (a specific DCER elicitation metric, a specific mod-108 monitor-fire regression, a specific mod-110 deception-eval regression) even without the numerical value. Exercise 02 fills in the values; the FSPC names the slot.
- **Author a section-4 escalation dry-run.** Pick one decision class (a mid-training threshold cross that the RSO does not resolve within SLA) and walk the escalation path through Preparedness Committee to Safety Board in the runbook. The dry-run tests whether the routing is actually operable or only nominally defined.
- **Add a fifth touch-point specific to the org.** Chapter 01 names four; some orgs (agentic-deployment-heavy, high-frequency-release, or regulated-industry-facing) benefit from a fifth (e.g. per-customer-deployment review). Add it, justify it in the runbook, and populate its row in section 2. Chapter 01's four are the floor, not the ceiling.
- **Draft the section-6 disclosure-shape one-pager.** The incident-response contract's disclosure shape becomes a template the on-call reaches for at 2am. Author the one-pager (regulator recipient list, AISI recipient list, public-disclosure decision tree, communications-lead sign-off requirement) as an appendix to the FSPC. Exercises 04 and 05 refine it.
- **Author the cross-framework mapping table as a standalone artefact.** Extract the section-4 mapping table into `fspc-<org>-cross-framework-map.yaml` with per-row citations back to the primary framework publications. This is the artefact a Frontier Model Forum working-group peer or an AISI liaison actually reads; the FSPC prose summarises, the standalone table proves.

## Deliverable location

Personal notes or private repo. Do **not** commit the FSPC, the artefact register, the decision register, or the runbook into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference FSPC skeleton. The FSPC names real (or plausibly-real) organisations, bodies, and members-by-role; disclosure discipline (chapter 05, chapter 06) means the signed artefact lives in the organisation's governance system, not in a public course repo. Where the FSPC references sensitive internal roles or named individuals, redact before any external circulation and route through the coordination roster section 6 defines.
