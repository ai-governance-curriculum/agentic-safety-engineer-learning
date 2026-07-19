# 03 — Disclosure Engineering and System Cards

## Motivation

A system card is the public-facing artefact of a frontier-safety program. It is the thing a developer reads when integrating the model, the thing a regulator reads when auditing the deployment, the thing a journalist reads when writing about the launch, and the thing an internal reviewer reads when preparing for a governance meeting. It is *not* the safety case (mod-109) — the safety case is the internal, evidence-dense argument. The system card is what the organisation is willing to *disclose*.

Disclosure is engineering, not communications. The card carries claims that were argued in the safety case, evidence that was produced by mod-106 elicitation, containment posture that was engineered by mod-107, monitoring posture that was engineered by mod-108. Every claim on the card has to be *true*, *supportable*, and *legible to its intended audience*. A card that overclaims is a disclosure failure; a card that underdiscloses is a governance failure; a card that mixes audiences is both.

This chapter walks the three current system-card genres — **Anthropic-shape**, **OpenAI-shape**, **Google DeepMind-shape** — pins their load-bearing invariants, and names the audience-separation discipline that keeps a card legible. It draws the boundary between developer-facing disclosure and external-stakeholder-facing disclosure, and it enumerates the sections a defensible card carries regardless of shape.

The load-bearing insight: system-card authoring is a *reduction* problem. The safety case has hundreds of pages of evidence; the card carries five to fifty pages of disclosure. The reduction is where the disclosure-engineering happens. A safety-engineer who cannot reduce the safety case cannot author the card.

## Primary sources

- **[Anthropic Claude system cards / model cards](https://www.anthropic.com/news)** — the Anthropic-shape genre. Read the most recent Claude family card end-to-end. <!-- needs-research: pin the current published Claude system card (e.g. Claude Opus 4 / Sonnet 4 / Sonnet 4.5 series) URL and version; Anthropic updates the cards per model release. -->
- **[OpenAI system cards](https://openai.com/safety)** — the OpenAI-shape genre. Read the most recent GPT / o-series card end-to-end. <!-- needs-research: pin the current GPT-4o / GPT-5 / o-series system-card URL and version. -->
- **[Google DeepMind Gemini system cards](https://deepmind.google/technologies/gemini/)** — the DeepMind-shape genre. Read the most recent Gemini system card end-to-end. <!-- needs-research: pin the current Gemini system-card URL and version. -->
- **[NIST AI RMF 1.0 — Transparency and Documentation guidance](https://www.nist.gov/itl/ai-risk-management-framework)** — the framework-level guidance on model documentation. <!-- needs-research: pin the specific NIST publication (AI RMF playbook or a companion publication) that most directly frames system-card content. -->
- **[Partnership on AI — ABOUT ML documentation practices](https://partnershiponai.org/)** — early industry work on model documentation that many current cards descend from. <!-- needs-research: pin the specific ABOUT ML publication the card references. -->
- **[Model Cards for Model Reporting (Mitchell et al.)](https://dl.acm.org/doi/10.1145/3287560.3287596)** — the academic root of the model-card genre. Read once for the historical framing.

Version-pin the cards you cite. The genre revises quarterly; specific claims come and go.

## The three current system-card genres

Read one recent card from each of the three shapes before this chapter. The shapes overlap significantly — they share the same load-bearing invariants — but they differ in emphasis, length, and audience posture.

### Anthropic-shape system card

*Emphasis.* Deep on safety evaluations, Responsible Scaling Policy tier determination, and elicitation-methodology disclosure. Load-bearing on the ASL-level determination and the pre-registered evaluation thresholds. Extensive red-team findings sections. Explicit "areas of uncertainty" section that mirrors the safety case's residual-risk statement.

*Length.* Typically 40–120 pages for a flagship model release, depending on the tier and the number of evaluated capabilities. Feature-rich releases (extended thinking, computer use, agentic capabilities) tend toward the upper end.

*Section shape.* Model summary → training data and process → intended and out-of-scope use → capability evaluations (per capability domain) → safety evaluations (per risk category) → Responsible Scaling Policy determination → deployment mitigations → known limitations → red-team disclosure summary → areas of ongoing work.

*Load-bearing invariants.* The RSP determination is stated with the pre-registered thresholds referenced. The elicitation methodology is disclosed per capability. The residual-risk framing is honest.

### OpenAI-shape system card

*Emphasis.* Deep on the Preparedness Framework scorecard, per-risk-category gradings, and the deployment-mitigation package. Load-bearing on the scorecard entries. Extensive external red-team findings sections (frequently naming specific organisations that conducted the red-teaming).

*Length.* Similar to Anthropic-shape — 40–120 pages for a flagship release, longer for the most feature-rich launches.

*Section shape.* Model overview → training approach → external red-teaming → Preparedness Framework evaluations (per risk category) → deployment mitigations → monitoring and enforcement posture → known limitations → open problems.

*Load-bearing invariants.* Each scorecard risk category has an explicit grading (low / medium / high / critical or equivalent) with the evidence citation. The deployment posture is stated per grading. External red-team findings are attributed.

### Google DeepMind-shape system card

*Emphasis.* Emphasis on the Frontier Safety Framework's Critical Capability Levels and mitigation tiers, with explicit CCL determinations per capability domain. Load-bearing on the CCL claims and the associated mitigation-tier engagements.

*Length.* Historically shorter than Anthropic / OpenAI cards for equivalent releases, though the trend is toward longer disclosure. <!-- needs-research: verify recent Gemini system card lengths and any shift toward longer disclosure formats. -->

*Section shape.* Model summary → capabilities and evaluations → Frontier Safety Framework determinations → responsible development mitigations → deployment restrictions and access → limitations.

*Load-bearing invariants.* CCL determinations per capability are stated with the evidence citation. The mitigation tier engaged per CCL is stated. Deployment restrictions (rate limits, access gates, jurisdictional constraints) are disclosed.

### The invariants across all three shapes

Regardless of shape, a defensible system card carries five load-bearing sections. The safety-engineer's authorial task is to make sure each is present, honest, and legible.

- **Capability summary.** What the model does well, at what scale, on what benchmarks, under what elicitation. Not marketing copy — an honest and specific description a developer or evaluator can use.
- **Safety-eval results.** What the safety evaluations found, per risk domain, with the elicitation methodology named. This is the reduction of mod-106's DCER onto the card's page-budget.
- **Deployment mitigations.** What is engaged at deployment time to keep the model within the safety-case's inability-and-control legs. The mod-107 EACC summary, the mod-108 monitoring posture, the human-in-the-loop escalation flow, the credential scoping.
- **Known limitations.** What the model does *not* do well, or does *only* under specific conditions. The section a downstream integrator reads to decide whether to trust the model for a task. Refusing to include this section is one of the most common disclosure failures.
- **Red-team disclosure.** Which red-team programmes contributed findings, what classes of findings survived to launch, and what mitigations engage against them. External red-team attributions where the red-teamers consented.

An audit that finds any of these five missing raises a finding *regardless of which genre* the card follows.

## Audience separation — developer-facing vs external-stakeholder-facing

A system card serves multiple audiences with materially different information needs. Merging them into a single unstructured document produces a card that no audience can read effectively. The disciplined approach is to name the audiences and separate the content accordingly.

### Developer-facing content

*Audience.* Integrators, application-layer developers, downstream safety engineers, MLOps teams.

*Needs.* Capability specifics per benchmark. Latency, cost, and throughput. Rate limits and quotas. Content-policy specifics. Failure modes to code against. Fine-tuning and customisation policy.

*Voice.* Precise. Numeric. Reproducible. "In the [named] harness at version [X], the model scored [Y]" — not "the model performs well on reasoning."

*Where it lives.* Typically in the capability-summary and known-limitations sections. Often supplemented by a developer-portal document (a *usage guide* or *safe deployment guide*) that the card links to.

### External-stakeholder-facing content

*Audience.* Regulators, AISI evaluators, Frontier Model Forum peers, external auditors, civil-society reviewers, journalists.

*Needs.* Tier / scorecard / CCL determination and the evidence for it. Elicitation methodology and its defence. Deployment mitigations at the level of *what they prevent*, not the level of *how they are engineered*. Residual-risk statement. Red-team programme scope and findings.

*Voice.* Argumentative. Framing-aware. Careful about what is claimed vs what is left to the safety case.

*Where it lives.* Typically in the safety-eval-results, deployment-mitigations, and red-team-disclosure sections. Often the load-bearing content for a card's headline claims.

### The separation discipline

The safety-engineer's authorial task is to name, per section, which audience it primarily serves — and to keep the voice consistent within a section. A section that mixes developer-facing precision with external-stakeholder-facing argument confuses both. A card that segregates them cleanly is one both audiences can read.

Some content is genuinely shared — the model's overall capability posture is relevant to both. The discipline is not to force separation where the content is shared; it is to *avoid* jamming developer-facing detail into an external-stakeholder section (or vice versa) *by accident*.

## The disclosure-engineering discipline — five invariants

Beyond section content, five invariants govern *how* claims are made on the card. Violate any of them and the card becomes vulnerable to a reviewer's catch.

### Invariant 1 — Every capability claim carries an elicitation footprint

A claim of the form *"the model achieves [X] on [benchmark]"* is incomplete without disclosing the elicitation used to produce the score. Plain-prompt evaluations, chain-of-thought elicitation, fine-tuning-on-domain-data elicitation, and tool-augmented elicitation produce materially different scores. The card discloses the elicitation used, at the level *"score [Y] under [named elicitation regime]"*, so that a reviewer can compare across models and across time.

### Invariant 2 — Every safety-eval claim carries a threshold pin

A claim of the form *"the model does not exhibit [dangerous capability]"* is only checkable against a specific threshold. The card discloses the threshold — either the pre-registered RSP / Preparedness / FSF threshold, or a numeric threshold on a named benchmark. *"The model scored below [threshold] on [benchmark] under [elicitation]"* is a checkable claim; *"the model does not exhibit"* alone is not.

### Invariant 3 — Every mitigation claim carries a mechanism

A claim of the form *"deployment mitigations prevent [failure mode]"* is incomplete without naming the mechanism. Naming does not require full engineering detail (that lives in the safety case), but it does require a legible chain: *"the runtime enforces a capability-gate pipeline (mod-107 EACC) that refuses tool calls exceeding [blast-radius cap], logs are tamper-evident and signed by a separate signing service, and monitors flag [pattern] for escalation."*

### Invariant 4 — Known limitations are disclosed at the level a downstream integrator can act on

A limitations section that says *"the model may hallucinate"* is not useful. A limitations section that says *"the model's factual accuracy on [named eval] degrades to [Y] when [condition]"* is useful — a downstream integrator can code around it.

### Invariant 5 — Residual-risk framing matches the safety case

The card's residual-risk section is a *reduction* of the safety-case's residual-risk statement. It cannot claim less residual risk than the safety case argues. A card that under-discloses residual risk relative to the safety case is a disclosure failure the reviewer catches by cross-checking the two.

## The reduction problem — safety case to card

A safety case is a hundreds-of-pages internal artefact (mod-109). A system card is 40–120 pages. The reduction is not a summary — it is a *disclosure judgement*. Every safety-case claim needs a card-side treatment:

- **Disclosed directly.** The claim is legible, safe to disclose in full, and load-bearing for the card's audience. Reduce to a paragraph on the card.
- **Disclosed at a summary level.** The claim's underlying evidence is sensitive (capability-uplift specifics, evaluation-harness internals, security-sensitive mitigations). Disclose the claim at a level the audience can act on; keep the evidence in the safety case.
- **Disclosed with redaction.** The claim references specific CBRN / cyber-offense payload evidence that would be a *disclosure harm* if published. Disclose the claim's shape; redact the payload evidence (chapter 04's regulator-facing disclosure discipline applies here in a stronger form).
- **Withheld.** The claim is genuinely internal-only. Disclose the fact that additional internal claims exist; do not disclose the claims themselves. This category should be narrow; over-use erodes trust with regulators and AISI.

The safety-engineer authoring the card walks the safety case and assigns each claim to one of the four buckets, with the assignment reviewed by external counsel (for legal exposure), external affairs (for regulator posture), and the safety-engineering lead (for accuracy). The assignment discipline is the *disclosure-engineering* craft.

## Interfaces

- **Chapter 01 (program).** The system card is one of the artefact-register rows in the FSPC. Its cadence keys off the pre-deployment safety-eval touch-point.
- **Chapter 02 (tier memo).** The tier determination on the card is the tier-decision memo's headline claim (chapter 02) reduced to the card's audience.
- **Chapter 04 (regulator disclosure).** The card is publicly disclosed; the Article 55 obligations documentation, AISI submissions, and Article 73 incident reports are separately disclosed to regulators. The card and the regulator submissions share evidence but not always audience.
- **Chapter 05 (incident response).** A post-deployment incident may drive an addendum to the card or a new card version. The incident-response contract names the disclosure-update trigger.
- **mod-106 (DCER).** The DCER is the primary evidence input for the safety-eval-results section.
- **mod-107 (EACC).** The EACC summary is the primary content for the deployment-mitigations section.
- **mod-108 (monitors).** The monitoring posture summary is the secondary content for the deployment-mitigations section.
- **mod-109 (safety case).** The safety case is the primary reduction target. Each card claim traces back to a safety-case claim.
- **`ai-evaluation-engineer` (peer, level 35).** The release-assurance packaging role often co-authors the developer-facing content of the card.

## Common misreadings to avoid

- **"The system card is marketing."** The card is disclosure. Marketing content — competitive comparisons, benefit framing — does not belong on the card. If it appears, it is a warning sign that the card is being drafted by the wrong role.
- **"The card and the safety case are the same document."** They serve different audiences and satisfy different constraints. The card is *reduced* from the safety case; it is not a copy.
- **"We can disclose more once we're confident."** Confidence is not a disclosure gate — evidence is. If a claim is load-bearing for the tier decision, it belongs on the card; if it is genuinely sensitive, chapter 04's redaction discipline applies. Waiting for *confidence* to disclose is a disclosure-failure mode that regulators penalise.
- **"External red-team attributions are just PR."** They are evidence-of-independence for external audiences. A card that names its external red-team programmes and their attributions is a card whose safety-eval results are more legible to a regulator than one that does not.
- **"The three genres are just formatting differences."** They differ in framing, in which decisions they emphasise, and in which vocabulary they use for the tier claim. Recognising the differences is part of authoring a card that reads correctly in whichever genre the organisation operates under.
- **"The known-limitations section is a competitive risk."** A card that omits limitations is a card that will be re-written by users, journalists, and researchers who find the limitations for themselves. Disclosing them is the discipline that keeps the card the *authoritative* description of the model.

## Summary

- The system card is the public-facing disclosure artefact. It is *reduced* from the safety case; it is not a copy.
- Three current genres — **Anthropic-shape**, **OpenAI-shape**, **Google DeepMind-shape** — share five load-bearing invariants: capability summary, safety-eval results, deployment mitigations, known limitations, red-team disclosure.
- Audience separation is a discipline: developer-facing content and external-stakeholder-facing content have different voices, different depth, and different section homes.
- Five invariants govern how claims are made: **elicitation footprint**, **threshold pin**, **mechanism naming**, **actionable limitations**, **residual-risk match to the safety case**.
- The reduction from safety case to card is a disclosure judgement per claim: disclose / summary / redact / withhold.
- Exercise 03 asks you to author a system card in each of the three shapes for one model version, and to defend the reduction choices. Chapter 04 walks the regulator-facing disclosure that runs alongside the public card.
