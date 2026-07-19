# 04 — Regulator-Facing Disclosure: EU and AISI

## Motivation

The system card (chapter 03) is public disclosure. Regulator-facing disclosure is a different animal. It runs on statutory timelines, into named regulator inboxes, in shapes those regulators codify, with redaction discipline strong enough to disclose dangerous-capability findings without disclosing the payload that made the finding dangerous. It is the discipline that ties the frontier-safety program's evidence to the jurisdictions the organisation operates in.

At the time of writing, four regulator-facing disclosure surfaces dominate a level-40 safety-engineer's day-to-day work:

- The **EU AI Act**'s Article 55 (obligations for General-Purpose AI models with systemic risk) and Article 73 (serious-incident reporting) tracks. Statutory. Load-bearing timelines.
- The **EU General-Purpose AI (GPAI) Code of Practice**'s safety-and-security disclosure track. Adopted under Article 56 of the AI Act as the presumption-of-conformity route.
- The **UK AI Safety Institute (UK AISI)** pre-deployment evaluation methodology. Voluntary but load-bearing for organisations operating under public commitments.
- The **US AI Safety Institute (US AISI, under NIST)** pre-deployment evaluation methodology. Similar posture to UK AISI, with different reporting conventions.

Each of these has its own vocabulary, its own timing constraints, its own escalation channels, and its own redaction expectations. The safety-engineering role in mod-112 authors the submissions and drives them through internal review; legal and external-affairs co-own the submissions; the organisation's regulator-relationship lead delivers them. This chapter walks the four surfaces, names their load-bearing shape and constraints, and pins the redaction discipline that keeps a dangerous-capability disclosure safe.

The load-bearing insight: regulator-facing disclosure is engineered. It is not a communications activity. The submission's evidence, the submission's redactions, the submission's timeline compliance, and the submission's cross-referencing to the safety case are all engineering artefacts that trace back to the mod-106 / mod-107 / mod-108 evidence base and forward to the FSPC's artefact register.

## Primary sources

- **[EU AI Act — Regulation (EU) 2024/1689, consolidated text](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)** — the statutory instrument. Articles 55 (systemic-risk GPAI obligations) and 73 (serious-incident reporting) are the primary regulator-facing surfaces at model-provider scope. <!-- needs-research: verify EUR-Lex URL and confirm article numbers are correct in the current consolidated text. -->
- **[European Commission — General-Purpose AI Code of Practice](https://digital-strategy.ec.europa.eu/en/policies/ai-code-practice)** — the Code of Practice under Article 56, including the safety-and-security disclosure chapter. <!-- needs-research: pin the current adopted version of the Code of Practice and the specific commitments in the safety-and-security chapter. -->
- **[UK AI Safety Institute — Publications and Approach](https://www.aisi.gov.uk/)** — the UK AISI's published evaluation methodology and pre-deployment report format. <!-- needs-research: pin the specific UK AISI methodology publications and pre-deployment evaluation reports the submission cites. -->
- **[US AI Safety Institute (NIST)](https://www.nist.gov/aisi)** — the US AISI's publications and evaluation methodology. <!-- needs-research: pin the specific US AISI publications and any Memoranda of Understanding governing model-access-for-evaluation arrangements. -->
- **[European AI Office](https://digital-strategy.ec.europa.eu/en/policies/ai-office)** — the enforcement body for the AI Act's GPAI obligations. <!-- needs-research: pin the AI Office's publication of implementing guidance and templates for Article 55 / 73 submissions. -->
- **[Executive Order 14179 (US)](https://www.whitehouse.gov/briefing-room/)** — the January 2025 executive order that revoked and replaced EO 14110. <!-- needs-research: verify the exact US executive order landscape as of the current date (EO 14110 was revoked; 14179 is the replacement; subsequent executive orders may have further revised). -->

Version-pin every regulator source. Regulatory instruments revise, guidance is issued, and mis-citing the current text is one of the reviewer's fastest catches.

## Surface 1 — EU AI Act Article 55 obligations (systemic-risk GPAI)

*What it is.* Article 55 places obligations on providers of General-Purpose AI models with systemic risk. These include model evaluation (with adversarial testing to identify and mitigate systemic risks), tracking and reporting of serious incidents (Article 73), cybersecurity protections at the model and physical-infrastructure level, and documentation to be made available to the AI Office and the national competent authorities. <!-- needs-research: verify the precise wording and full enumeration of Article 55 obligations in the current consolidated text. -->

*Trigger.* A model classified as GPAI with systemic risk. Classification thresholds are pinned in Article 51 of the AI Act (compute thresholds and other criteria) and elaborated by the AI Office. <!-- needs-research: pin the current systemic-risk classification threshold (10^25 FLOPS or updated value) and the AI Office's classification criteria. -->

*Documentation the safety-engineer contributes.* An evaluation-and-mitigation dossier that carries: the model's capability profile per risk category, the elicitation methodology defence (mod-106), the systemic-risk assessment, the mitigations engaged (mod-107 EACC posture, mod-108 monitoring posture, safety-case argument from mod-109), and the cybersecurity posture at the model and infrastructure level (which routes to `ai-infra-security`).

*Timing.* Obligations begin at classification. Ongoing documentation is maintained; specific artefacts are made available to the AI Office and national competent authorities on request. The Article 73 serious-incident track has its own timelines (below).

*Common failure the reviewer catches.* The Article 55 dossier is a re-badging of the system card. The reviewer asks for the specific mitigations engaged against the specific systemic-risk categories, at the level of *mechanism* rather than *summary*. A dossier that reads like a card is a dossier that has not been engineered for its regulator audience.

## Surface 2 — EU AI Act Article 73 serious-incident reporting

*What it is.* Article 73 requires providers of high-risk AI systems (with Article 55 extending obligations to systemic-risk GPAI) to report serious incidents to the market surveillance authorities of the Member States where the incident occurred, within statutory timelines. <!-- needs-research: verify Article 73's precise scope, timing, and applicability to systemic-risk GPAI providers under Article 55; the statutory timelines vary by incident class (immediate for widespread infringements, longer for serious incidents narrowly defined). -->

*Trigger.* A **serious incident** as defined in the AI Act (Article 3's definitions section). Includes: incidents that lead to death or serious harm to a person's health, serious and irreversible disruption of critical infrastructure, breach of fundamental-rights obligations, and serious damage to property or environment. <!-- needs-research: verify Article 3's serious-incident definition, including any GPAI-specific extensions. -->

*Timing.* The AI Act sets specific reporting timelines — immediately (or within a specified number of days) for the initial report, followed by a full report within a longer window. <!-- needs-research: pin the exact timelines (e.g. 2 days for initial notification of widespread infringements, 15 days for full report of serious incidents, or the current updated timelines). -->

*Reporting shape.* An initial notification (concise; enough for the market-surveillance authority to understand what happened and whether coordination is needed), followed by a full report. The full report carries: the incident description, root-cause analysis, affected users / systems, mitigation actions taken, and future preventive measures. Chapter 05 walks the incident-response contract that drives the Article 73 timeline.

*Redaction discipline.* Article 73 reports frequently reference dangerous-capability findings — CBRN uplift, cyber-offense payload evidence, autonomy exceedance. The redaction discipline below applies: disclose the incident's shape and consequence; redact the payload evidence and the specific technical detail that would let a malicious actor reproduce the harm. Market-surveillance authorities coordinate with law-enforcement and national-security bodies for redacted content; the redaction is not a refusal to disclose but a *routed* disclosure.

*Common failure the reviewer catches.* The Article 73 report is delayed beyond the statutory timeline while internal review completes. The remedy is a pre-authored template, a pre-cleared internal-review path, and a pre-nominated on-call safety-engineer with authority to draft-and-file (chapter 05).

## Surface 3 — EU GPAI Code of Practice safety-and-security disclosure

*What it is.* The GPAI Code of Practice, adopted under Article 56 of the AI Act, is a voluntary code that provides a presumption of conformity with GPAI obligations for signatories. The safety-and-security chapter of the Code details commitments around risk assessment, adversarial testing, incident reporting, and cybersecurity that translate the Article 55 obligations into operationally checkable commitments. <!-- needs-research: pin the current adopted version of the Code of Practice and the specific commitments in the safety-and-security chapter; the Code was adopted in staged form and may continue to evolve. -->

*Trigger.* Signatory status. A GPAI provider that signs the Code commits to its measures. Non-signatories must demonstrate compliance with the AI Act by other means.

*Documentation the safety-engineer contributes.* Per-commitment evidence in the shape the Code prescribes. Commitments cover risk assessment (drawing on mod-106 evidence), adversarial testing (mod-104 / mod-105 / mod-110 / mod-111), incident reporting (Article 73), and cybersecurity (routes to `ai-infra-security`). The Code prescribes reporting cadence and evidence shape per commitment.

*Timing.* Commitment-by-commitment. Some commitments require pre-deployment evidence (risk assessment and adversarial testing before market placement); others require ongoing evidence (post-deployment monitoring and incident reporting).

*Common failure the reviewer catches.* The Code's commitments are treated as re-badging of Article 55 obligations rather than as operationally checkable commitments the AI Office reads and audits against. The Code is more prescriptive than the AI Act on evidence shape; a submission that treats it as summary-level under-discloses.

## Surface 4 — UK AISI and US AISI pre-deployment reports

*What it is.* Both the UK AISI (`aisi.gov.uk`) and the US AISI (under NIST at `nist.gov/aisi`) publish evaluation methodologies for frontier models and — under bilateral arrangements with major model providers — conduct pre-deployment evaluations of specified models. <!-- needs-research: verify current MoU / access arrangements between the AISIs and specific model providers (Anthropic, OpenAI, Google DeepMind, others). -->

*Trigger.* A model that falls within the AISI's evaluation scope (specific capability thresholds, specific model families) and the provider's participation in the bilateral arrangement. Not statutory in the same sense as Article 55; nonetheless load-bearing for organisations that operate under public commitments to AISI evaluation.

*Documentation the safety-engineer contributes.* An evaluation-access package that includes: model access (as agreed under the MoU), documentation of the internal capability evaluations (mod-106 DCER), documentation of the elicitation methodologies used, and — critically — a *methodology contribution*. UK and US AISI both publish evaluation methodologies that peer providers contribute to; a defensible submission is not just *access* but *methodology contribution* (proposed evaluations, elicitation improvements, benchmark-construction advice).

*Reporting shape.* The AISI produces its own report, typically after a defined evaluation window. The provider's contribution to that report is (a) the initial documentation and access package, and (b) response to the AISI's findings during a review-and-comment window.

*Common failure the reviewer catches.* The organisation treats AISI evaluation as *external audit* rather than *methodology partnership*. AISI's evaluations are strongest when the model provider contributes methodology and elicitation improvements; a provider that only responds to AISI's evaluations misses the strongest signal available. AISI methodology contributions are also one of the hiring-signal artefacts chapter 06 discusses.

## Structural expectations across the four surfaces

Despite differing statutory bases, the four surfaces share load-bearing structural expectations. Every regulator-facing submission carries:

- **Incident / model identity.** Specific model version, deployment surface, jurisdictions in scope. Version-pinned.
- **Capability profile per risk category.** The tier / scorecard / CCL determination (chapter 02) and the underlying evidence citation.
- **Mitigation posture.** The mod-107 EACC summary and the mod-108 monitoring posture, at the level of *what they prevent* rather than *how they are engineered*.
- **Elicitation methodology defence.** The regimes run, the methodology-gap argument.
- **Residual-risk statement.** What the submission does not claim to have ruled out.
- **Contact.** The named safety-engineer (or safety-engineering lead) authorised to respond to regulator follow-up, and the internal escalation path if that person is unavailable.
- **Cross-references.** To the internal safety case, the system card, the tier-decision memo, and prior submissions.

The submissions differ in emphasis, timing, and specific vocabulary — the discipline is to author each in its own genre while sharing the evidence base.

## Redaction discipline — CBRN and cyber-offense payload evidence

Regulator-facing disclosure that references dangerous-capability findings needs a redaction discipline that keeps the disclosure honest without publishing payload material that would uplift a malicious actor. The discipline has three legs.

### Leg 1 — Disclose the shape, redact the payload

A disclosure of *"the model produced synthesizable-grade guidance for [CBRN precursor] under [elicitation]"* is a disclosure of shape; the specific guidance, chain-of-thought, or actionable output is the payload. The disclosure names the finding, the elicitation configuration, the mitigation engaged, and the ceiling the mitigation is argued to hold to; the payload evidence is retained in an internal, controlled-access location and made available to the regulator through a *separate* controlled channel (in-person review, secure data room, cleared reviewer).

### Leg 2 — Route redacted content, do not withhold

The redaction is not a refusal to disclose. The submission names the fact that specific payload evidence exists, states that it is redacted from the public / non-controlled submission, and specifies the channel by which the regulator can access it. For EU submissions, this channel typically routes through the AI Office and the Member State's competent authority in coordination with national security services. For UK AISI, it routes through AISI's controlled-access facilities. For US AISI, it routes through NIST's controlled-access facilities.

### Leg 3 — Legal and communications co-authorship

The redaction discipline is not the safety-engineer's alone. Legal reviews the redaction against disclosure obligations; external affairs / communications reviews the redaction against the organisation's public commitments and the regulator relationship. A safety-engineer authoring a redacted submission has co-authors named on the submission itself.

*Common failure the reviewer catches.* The redaction is *removal without acknowledgement*. A submission that redacts payload evidence without naming that it did so under-discloses; a regulator asks *"was there payload evidence you did not include?"* and the answer becomes an incident of its own. Every redaction is *labelled* with the routing channel through which the redacted content is available.

## Timing constraints and the pre-authored template

Regulator-facing disclosure runs on statutory or agreed timelines. Missing a timeline is a regulatory finding regardless of the underlying incident's severity. The discipline that meets timelines under pressure is *pre-authored templates* with *pre-cleared internal-review paths*:

- **Article 73 initial notification template.** Skeleton pre-cleared by legal, safety-engineering, and external-affairs. Fields include incident identity, model version, deployment surface, incident-class per the Act's taxonomy, initial mitigation actions, and contact. The on-call safety-engineer (chapter 05) fills the template within the statutory initial window.
- **Article 73 full report template.** Skeleton pre-cleared. Fields include the initial-notification content plus root-cause analysis, affected-users summary, mitigation actions taken, and future preventive measures. Filled within the full-report statutory window.
- **AISI response templates.** Skeletons pre-cleared for common AISI evaluation findings (methodology-comment response, additional-elicitation-request response, timeline-of-mitigation response).

The pre-authored templates are versioned artefacts in the FSPC's artefact register (chapter 01). They are updated when the AI Act's implementing acts revise the reporting schema; they are drilled quarterly alongside the rollback drill (chapter 02).

## Interfaces

- **Chapter 01 (program).** Every regulator submission is an artefact-register row. Timing constraints and internal-review paths are pinned there.
- **Chapter 02 (tier memo).** The tier determination on regulator submissions is the memo's headline claim, in the regulator's vocabulary.
- **Chapter 03 (system card).** The card and the regulator submissions share evidence; the card is public, the submissions are (variously) regulator-controlled.
- **Chapter 05 (incident response).** The Article 73 track is the primary regulator-facing surface for post-deployment incidents. Chapter 05's incident-response contract drives the Article 73 timeline.
- **mod-106 (DCER).** The load-bearing evidence input for the capability profile and elicitation-methodology defence sections.
- **mod-107 (EACC).** The mitigation posture summary.
- **mod-108 (monitors).** The monitoring posture and the incident-detection surface.
- **`ai-infra-security` (peer, level 35).** The cybersecurity posture at the model and infrastructure level, as required by Article 55.
- **Legal, external affairs, communications.** Co-owners of every regulator submission. The safety-engineer authors; these functions co-sign.

## Common misreadings to avoid

- **"Regulator disclosure is legal's job."** Legal reviews. External affairs delivers. The safety-engineer *authors*, because the substance of the disclosure — capability profiles, elicitation defence, mitigation shape, residual risk — is engineering content legal cannot draft accurately alone.
- **"The Article 73 timeline is aspirational."** It is statutory. Missing it is a regulatory finding independent of the incident's severity. The pre-authored template and pre-cleared review path are what make the timeline achievable.
- **"AISI is external audit; we respond to what they ask."** AISI is a methodology partnership. Providers that only respond miss the strongest signal available and forfeit the hiring-signal value of methodology contribution (chapter 06).
- **"Redaction protects the organisation."** Redaction *routes* sensitive content to the appropriate regulator channel. Redaction that *withholds* content without labelling the routing is under-disclosure — the discipline is disclose-shape-and-route-payload.
- **"The Code of Practice is voluntary, so we can skip it."** The Code provides the presumption of conformity with Article 55. Non-signatories carry a higher evidence burden with the AI Office. For most systemic-risk GPAI providers, signing and complying with the Code is the operationally cheaper path.
- **"US and UK AISI submissions are the same."** They share methodology heritage but differ in access arrangements, reporting cadence, and the specific evaluations they run. Author each in its own shape.

## Summary

- Regulator-facing disclosure is engineered, not communicated. The load-bearing surfaces are **EU AI Act Article 55** (systemic-risk GPAI obligations), **Article 73** (serious-incident reporting), **the GPAI Code of Practice safety-and-security chapter**, and **UK AISI / US AISI pre-deployment reports**.
- Every submission shares a load-bearing structure — model identity, capability profile, mitigation posture, elicitation-methodology defence, residual-risk statement, named contact, cross-references.
- **Redaction discipline** for dangerous-capability payload evidence: disclose the shape, redact the payload, route the payload through the appropriate controlled channel, and label the redaction on the submission.
- **Pre-authored templates and pre-cleared review paths** are what make statutory timelines achievable under pressure. They live in the FSPC's artefact register and are drilled quarterly.
- Legal, external affairs, and communications co-own every submission. The safety-engineer authors; those functions co-sign.
- Exercise 04 asks you to draft an Article 73 initial notification and a Code-of-Practice safety-and-security disclosure for one incident scenario. Chapter 05 walks the incident-response contract that drives the submissions.
