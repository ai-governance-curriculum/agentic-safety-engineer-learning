# exercise-04 — EU Article 73 and GPAI Code safety disclosure drill

**Estimated effort:** 2 hours
**Prerequisite chapters:** 04 (primary); 05 (incident-response contract the disclosure lives inside); 01 (template as artefact-register row); 03 (public-vs-regulator disclosure separation).

## Objective

Author the durable **pre-cleared templates** for the two Article 73 filings a level-40 safety-engineer keeps on-call — the initial notification and the full report — and the shape of a **GPAI Code of Practice safety-and-security disclosure entry** for the two or three commitments most materially triggered by one synthetic-but-plausible serious-incident scenario. Fill each template for that one scenario end-to-end. The exercise produces the artefacts chapter 04's *pre-authored template approach* pins as what makes chapter 04's statutory timelines achievable under pressure, and slots them into the FSPC (exercise 01) as artefact-register rows with a co-authorship chain (legal / external affairs / communications) named per chapter 04's *redaction discipline's third leg*.

## Problem statement

Chapter 04's four regulator-facing surfaces (Article 55, Article 73, GPAI Code of Practice safety-and-security disclosure, UK AISI / US AISI pre-deployment reports) are engineered artefacts, not communications. Article 73 in particular runs on statutory timelines that the chapter is explicit about *not* inventing hour values for; a submission that misses the statutory window is a regulatory finding regardless of the underlying incident's severity. Chapter 04's remedy is a pre-authored template with a pre-cleared internal-review path and a pre-nominated on-call safety-engineer with authority to draft-and-file. The template is the durable artefact; the fill is the drill.

Pick **one incident scenario** from the small set below — do not invent a specific real-world incident, do not name real victims, do not name a real product incident. The scenarios are chosen to exercise chapter 04's *redaction discipline's three legs* against material dangerous-capability content without the exercise itself producing dangerous-capability payload text:

- **Scenario J** — a jailbroken-agent tool-abuse incident with contained blast radius (chapter 05 class taxonomy: tool-abuse, contained). The elicitation surface bypassed the mod-107 EACC posture; the mod-108 monitors surfaced the abuse; blast radius was bounded before user harm materialised.
- **Scenario D** — a dangerous-capability-disclosure incident surfaced by a bilateral UK AISI or US AISI evaluation (chapter 05 class taxonomy: DCER regression, evaluator-surfaced). The AISI's controlled evaluation surfaced a capability exceedance against the model's registered tier / CCL; the finding routed through the AISI's controlled channel before public reporting could occur.
- **Scenario R** — a safety-eval regression discovered by post-deployment monitoring (chapter 05 class taxonomy: safety-eval regression, monitor-surfaced). A monitored eval slice degraded past a pre-registered threshold on a stable-model deployment; upstream cause is a scaffold or retrieval-index change.

For that one scenario, author the three template shapes and their filled instances, plus the redaction log and the runbook.

Payload discipline: NO simulated dangerous-capability content, NO CBRN or cyber-offense payload text, NO working jailbreak prompt text in any committed file. The exercise's own redaction discipline applies to the exercise's own artefacts. Chapter 04's *disclose the shape, redact the payload, route redacted content — do not withhold* is the rule for the drill itself, not just the drill's output.

## Requirements

Produce five artefacts.

### Artefact A — `art-73-initial-template.md` and `art-73-initial-<incident-id>.md`

The Article 73 initial-notification **template** (durable, pre-cleared skeleton) and one **filled instance** for the chosen scenario.

The template carries a blank pre-cleared skeleton with fields per chapter 04's *pre-authored template approach*:

- **Header block** — provider identity, model identity (version, weights hash or provider build ID, deployment surface, jurisdictions in scope), incident identifier (internal ID; the chapter 05 incident-response contract issues it), notification identifier, notification timestamp (UTC), statutory-clock start timestamp.
- **Incident-class field** — the AI Act Article 3 serious-incident class the incident maps to. <!-- needs-research: verify Article 3's serious-incident enumeration and any GPAI-specific extensions in the current consolidated text. -->
- **Incident-shape field** — one paragraph, the shape of what occurred, no payload text.
- **Initial mitigation actions** — the actions taken between detection and notification.
- **Redaction pointer block** — enumeration of the elements redacted from this notification, the routing channel for each redacted element (AI Office + Member State competent authority; UK AISI controlled facility; NIST controlled facility), and the pointer into the payload store.
- **Contact block** — the named on-call safety-engineer authorised to respond, the internal escalation path if unavailable.
- **Co-author sign-off slots** — legal, external affairs, communications, per chapter 04's *redaction discipline's third leg*.
- **Timeline pin** — `<!-- needs-research: verify the current Article 73 initial-notification statutory window under the implementing acts and any AI Office guidance in force at filing time. -->` Do not invent an hour value.

The filled instance populates the header, the incident class, the incident shape, the initial mitigation actions, the redaction pointers, and the contact block for the chosen scenario. Every redaction is *labelled* per chapter 04's *common failure the reviewer catches* — a redaction without a labelled routing channel is a hard fail.

### Artefact B — `art-73-full-template.md` and `art-73-full-<incident-id>.md`

The Article 73 full-report **template** and one **filled instance** for the same scenario. The template is authored to be filled within the statutory full-report window (per chapter 04's *timing constraints and the pre-authored template*).

Sections chapter 04 pins for the full report:

- **Incident description.** Longer form than the initial notification; still shape not payload.
- **Root-cause analysis.** The technical root cause at the level chapter 04's *evidence-shape* discipline expects — mechanism, not summary; cites the mod-107 EACC posture element that failed (for scenario J), the mod-106 DCER methodology-gap element (for scenario D), or the mod-108 monitor / eval-slice element (for scenario R).
- **Affected users / systems.** Enumerated jurisdictions, deployment surfaces, user populations. Chapter 04's *incident / model identity* header discipline; version-pinned.
- **Mitigation actions taken.** The actions between initial notification and full-report filing. Cross-references to the mod-107 EACC update and any mod-108 monitor / guardrail change.
- **Future preventive measures.** The forward-looking mitigations, with the FSPC artefact-register rows (exercise 01) that will carry them.
- **Redaction pointer block.** Same discipline as the initial notification; the full report typically carries more redacted elements because the root-cause analysis names mechanism.
- **Cross-reference block.** To the internal safety case (mod-109), the system card (chapter 03 / exercise 03), the tier-decision memo (chapter 02), prior submissions on the same model, and the chapter-05 incident-response record.
- **Co-author sign-off slots.** As in Artefact A.
- **Timeline pin.** `<!-- needs-research: verify the current Article 73 full-report statutory window under the implementing acts and any AI Office guidance in force at filing time. -->` Do not invent a day value.

The filled instance populates every section for the chosen scenario. Do NOT invent Article 73 exact wording; do NOT cite a specific Member State authority by name without a `<!-- needs-research: ... -->` marker.

### Artefact C — `gpai-cop-<incident-id>-entries.md`

Per-commitment disclosure entries for the GPAI Code of Practice safety-and-security chapter, covering the two or three commitments the chosen incident most-materially triggers. Chapter 04's *Surface 3* pins the shape: per-commitment evidence in the shape the Code prescribes, with commitment-by-commitment cadence — some pre-deployment, some ongoing.

For each of the two or three commitments picked:

- **Commitment identifier.** The commitment reference in the Code's safety-and-security chapter. <!-- needs-research: pin the current adopted version of the GPAI Code of Practice and the specific commitment identifiers in the safety-and-security chapter. -->
- **Trigger.** The element of the chosen incident that materially triggers reporting under this commitment.
- **Evidence shape.** The evidence the Code prescribes for this commitment; the pointer into the mod-106 / mod-107 / mod-108 / mod-109 / mod-110 evidence base that carries it.
- **Reporting cadence.** Pre-deployment / post-deployment / incident-triggered per chapter 04's *timing* pin.
- **Redaction pointer.** Same labelling discipline as Artefacts A and B.
- **Cross-reference to Article 73.** Where the same incident populates an Article 73 submission, name the cross-reference so the AI Office reader sees one incident-shape across the two surfaces.

The Code is more prescriptive on evidence shape than the AI Act itself; chapter 04's *common failure the reviewer catches* for Surface 3 is treating Code entries as summary-level rebadging of Article 55 obligations. Author each entry as *operationally checkable* evidence.

### Artefact D — `art-73-<incident-id>-redaction-log.yaml`

A structured log of every redaction across Artefacts A, B, and C, enforcing chapter 04's *redaction discipline's three legs* — disclose the shape, route the payload, name the co-authors.

Per redacted element:

- `element_id` — stable identifier referenced from the submission's redaction-pointer block.
- `submission_ref` — which artefact and which section the redaction appears in (A / B / C, section name).
- `shape_disclosed` — a one-line summary of the shape the submission discloses (no payload text).
- `payload_redacted` — a one-line categorisation of what is redacted (e.g. *"CBRN precursor guidance produced under [elicitation regime]"*), no payload text.
- `routing_channel` — which controlled channel carries the redacted payload: `ai_office_plus_member_state_competent_authority`, `uk_aisi_controlled_facility`, `nist_controlled_facility`, or a combination. Chapter 04 Leg 2 is the authority.
- `payload_store_pointer` — pointer + sha256 into the org's payload store; never a public URI, never inline text.
- `legal_signoff_slot` — named slot for legal review.
- `external_affairs_signoff_slot` — named slot for external affairs / communications review.
- `regulator_reader_access_role` — the controlled-access identity that reads the payload at the routing channel.

The log is the auditable surface for chapter 04's *"every redaction is labelled with the routing channel through which the redacted content is available"* rule. A redaction in Artefacts A/B/C that does not appear in the log is a hard fail; a log entry whose `routing_channel` is empty is a hard fail.

### Artefact E — `art-73-<incident-id>-runbook.md`

An 800–1200 word runbook covering:

- **Incident-scenario framing.** Which of scenarios J / D / R you picked, the chapter 05 class-taxonomy row it maps to, the model-family and deployment-surface framing (synthetic-but-plausible; not a real product incident). Why this scenario exercises chapter 04's *four surfaces* meaningfully (Article 55 dossier is stable across scenarios; Article 73 track fires; Code of Practice commitments trigger differently per scenario; UK / US AISI channel appears in the redaction log for scenario D and often in J).
- **Statutory-timeline compliance approach.** How the pre-authored template + pre-cleared internal-review path + pre-nominated on-call safety-engineer combine to meet the statutory windows chapter 04 says are non-negotiable. State explicitly that the exercise did NOT invent hour or day values — every timeline field carries a `<!-- needs-research: ... -->` marker and the runbook's real-filing precondition is *confirm the pin against the current implementing acts and AI Office guidance*.
- **Template pre-clearance process.** How the template is pre-cleared with legal, external affairs, and communications before an incident occurs; the cadence at which the template is re-cleared when the AI Act's implementing acts revise, when the Code of Practice revises, or when AI Office guidance is issued. Chapter 04's *pre-authored templates are versioned artefacts in the FSPC's artefact register* and *drilled quarterly alongside the rollback drill* is the authority.
- **Redaction discipline audit.** Walk chapter 04's three legs against the redaction log: Leg 1 (shape disclosed, payload redacted), Leg 2 (routing channel named for every redacted element), Leg 3 (legal + external affairs / communications co-authorship). State how the log's schema precludes the *"redaction as removal without acknowledgement"* failure chapter 04 names.
- **Co-authorship chain.** The chapter 04 co-owners named explicitly: the safety-engineer authors, legal reviews the redaction against disclosure obligations, external affairs / communications reviews the redaction against public commitments and the regulator relationship, the regulator-relationship lead delivers. Name the sign-off order and the escalation route when a co-author blocks.
- **Interface to chapter 05.** The Article 73 track *is* the primary regulator-facing surface for post-deployment incidents; chapter 05's incident-response contract drives the Article 73 timeline. Exercise 05 produces the incident narrative these submissions reference; the runbook names how the incident-response contract's classifier output populates the incident-class field and how the incident-response contract's on-call rotation names the contact block.
- **Interface to exercise 03.** The system card (exercise 03) is *public* disclosure that runs alongside the regulator-facing disclosure; chapter 03's public-vs-regulator separation is the frame. Name what the card discloses about the same incident and what it does not, and how the redaction discipline differs between the card and the submissions.
- **Interface to the FSPC (exercise 01).** The two Article 73 templates, the Code of Practice entries, and the redaction log are artefact-register rows in the FSPC. Name the row for each, the owner, the review cadence (quarterly per chapter 04), and the drilling cadence (quarterly per chapter 04 alongside the rollback drill).
- **Threats to validity.** Template drift when the implementing acts revise between drills; the on-call safety-engineer being unavailable at the statutory-clock start; the co-author sign-off chain blocking under pressure; the payload store's controlled-channel routing failing on a non-standard incident class; the temptation to inline payload text in the runbook itself (which the exercise's own discipline forbids).

## Starter guidance

- **Read chapter 04 end-to-end before drafting.** The *four surfaces*, the *redaction discipline's three legs*, and the *pre-authored template approach* are the load-bearing pins. A template drafted without absorbing chapter 04's *common failure the reviewer catches* boxes will reproduce the failures chapter 04 spent its whole length calling out.
- **The template is the durable artefact; the fill is the drill.** Author the template first, generically. Then fill it for the chosen scenario. A template that reads like a filled instance with placeholders swapped in is a template that will not survive the next incident.
- **Do not invent statutory hour values.** Chapter 04 explicitly refuses to pin the Article 73 timelines because implementing acts and AI Office guidance are in motion. Every timeline field carries `<!-- needs-research: ... -->`. The runbook makes the *confirm-the-pin-before-real-filing* precondition load-bearing.
- **Do not invent Article 73 exact wording.** The template's field labels are your discipline (incident class, incident shape, initial mitigation actions, contact, co-author sign-off) — not the Act's exact statutory phrasing. Where the Act's phrasing is load-bearing (e.g. the *serious incident* definition from Article 3), reference the Act with a `<!-- needs-research: ... -->` marker and do not paraphrase.
- **Redaction discipline applies to the exercise's own artefacts.** No CBRN or cyber-offense payload text; no working jailbreak prompt text; no working elicitation prompt text; no simulated tool-abuse transcript. The `shape_disclosed` and `payload_redacted` fields in the redaction log are one-liners, not narratives.
- **Do not name real victims or real product incidents.** The three scenarios are synthetic-but-plausible; the runbook says so explicitly. A submission that reads like it is filing a real incident is a submission that has failed the exercise's payload discipline.
- **Do not name specific Member State authorities without a `needs-research` marker.** The competent authority varies by Member State and by incident class; chapter 04 leaves this to primary-source verification at filing time.
- **The Code of Practice is more prescriptive than the AI Act on evidence shape.** Chapter 04's Surface 3 *common failure* is treating Code entries as summary-level. Author two or three entries; do not author entries for every commitment the Code carries — that is not the drill.
- **Cross-reference the same incident across the three surfaces.** The AI Office reads Article 73 and the Code of Practice disclosures against the same incident; a submission set where the incident-shape differs across surfaces is a finding.
- **The redaction log is the auditable surface.** A reviewer given the redaction log and the payload-store pointers must be able to reconstruct *what was disclosed as shape*, *what was redacted as payload*, and *where the payload is available*. If the log cannot support that reconstruction, the exercise has failed chapter 04 Leg 2.
- **The co-authorship chain is not optional.** Chapter 04's Leg 3 says so explicitly. A template without legal / external-affairs / communications sign-off slots is a template that fails the reviewer's first pass.
- **The FSPC row is the durability check.** Exercise 01's artefact register is where the template lives across model versions, incidents, and implementing-act revisions. If the template cannot be described as an artefact-register row (owner, cadence, review path), it is not a template — it is a draft.

## Acceptance criteria

- ✅ `art-73-initial-template.md` is a blank pre-cleared skeleton with header, incident-class, incident-shape, initial-mitigation, redaction-pointer, contact, co-author sign-off, and timeline-pin fields per chapter 04's *pre-authored template approach*. Timeline pin carries `<!-- needs-research: ... -->`; no hour value is invented.
- ✅ `art-73-initial-<incident-id>.md` populates every template field for the chosen scenario J / D / R. Every redaction is labelled with a routing channel; no payload text appears.
- ✅ `art-73-full-template.md` is a blank pre-cleared skeleton covering incident description, root-cause analysis, affected users / systems, mitigation actions taken, future preventive measures, redaction pointer block, cross-reference block, co-author sign-off, and timeline pin (with `<!-- needs-research: ... -->`).
- ✅ `art-73-full-<incident-id>.md` populates every section for the same scenario, with root-cause analysis at *mechanism* level (not summary) and cross-references to the mod-106 / mod-107 / mod-108 / mod-109 evidence base per chapter 04's *evidence-shape* discipline.
- ✅ `gpai-cop-<incident-id>-entries.md` carries per-commitment entries for **two or three** commitments materially triggered by the chosen scenario. Each entry has commitment identifier, trigger, evidence shape, reporting cadence, redaction pointer, and Article 73 cross-reference. Commitment identifiers carry `<!-- needs-research: ... -->` where the current adopted Code version has not been version-pinned.
- ✅ `art-73-<incident-id>-redaction-log.yaml` enumerates every redaction across Artefacts A, B, and C, with `element_id`, `submission_ref`, `shape_disclosed`, `payload_redacted`, `routing_channel` (one of the chapter 04 Leg 2 controlled channels), `payload_store_pointer` (pointer + sha256, never inline), `legal_signoff_slot`, `external_affairs_signoff_slot`, and `regulator_reader_access_role`. A redaction in A/B/C not appearing in the log is a hard fail; a log entry with an empty `routing_channel` is a hard fail.
- ✅ `art-73-<incident-id>-runbook.md` (800–1200 words) covers incident-scenario framing, statutory-timeline compliance approach (with the *confirm-the-pin-before-real-filing* precondition made load-bearing), template pre-clearance process, redaction discipline audit against chapter 04's three legs, co-authorship chain, interface to chapter 05's incident-response contract, interface to exercise 03's system card (public-vs-regulator separation per chapter 03), interface to the FSPC (exercise 01) artefact register, and threats to validity.
- ✅ **Payload discipline satisfied.** No CBRN payload text, no cyber-offense payload text, no working jailbreak / elicitation prompt text, no simulated tool-abuse transcript, and no real victim or real product-incident name appears in any committed file. The redaction log's `shape_disclosed` and `payload_redacted` fields are one-liners; `payload_store_pointer` is a pointer, not inline text.
- ✅ Every unverified factual claim (Article 3 serious-incident enumeration, Article 73 statutory windows, Code of Practice commitment identifiers, current Member State competent authorities, MoU arrangements with UK / US AISI) is marked `<!-- needs-research: ... -->`. Chapter 04's own primary-sources block carries the same markers; the exercise inherits the discipline.
- ✅ Cross-references land: the runbook cites exercise 05 as the source of the incident narrative the disclosures reference, exercise 03 as the public disclosure running alongside, and exercise 01 (FSPC) as the artefact-register home for the templates. Chapter 04's *interfaces* section is the authority for what to cite.

## Stretch goals

- **Author a UK AISI / US AISI response template alongside.** Chapter 04's *timing constraints and the pre-authored template* names AISI response templates as parallel durable artefacts. Draft the skeleton for one AISI-response class (methodology-comment response, additional-elicitation-request response, or timeline-of-mitigation response) and fill it for scenario D. Route the redactions through the AISI controlled facility per chapter 04's Leg 2.
- **Draft the quarterly template-drill runbook.** Chapter 04 pins that templates are *drilled quarterly alongside the rollback drill*. Author the drill runbook — the tabletop-shape exercise the on-call safety-engineer runs against a synthetic incident to verify the template fills within the statutory window, the redaction log audits clean, and the co-authorship chain closes. This slots into the FSPC (exercise 01) as its own artefact-register row.
- **Sketch the multi-Member-State coordination path.** Where an incident's blast radius spans multiple Member States, chapter 04's Surface 2 notes market-surveillance-authority coordination is needed. Draft the routing table — which Member State's competent authority receives the initial notification, which receive copies, and how the AI Office coordinates. Every specific authority name carries a `<!-- needs-research: ... -->` marker.
- **Author the *submission-set consistency* audit.** A three-line audit that checks the same incident-shape summary is used verbatim across Artefacts A, B, and C — the AI Office reads all three and a divergent summary is a finding. Wire the audit as a pre-filing checklist item in the runbook.

## Deliverable location

Personal notes or private repo. Do **not** commit the templates, the filled instances, the Code of Practice entries, the redaction log, or the runbook into this course repo. Payload content (dangerous-capability evidence referenced by any redaction pointer) never appears in committed files — pointer + sha256 into the org's payload store only, per chapter 04's redaction discipline and the FSPC's artefact-register controls (exercise 01). The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference template pair. Before authoring the templates, verify the payload store is provisioned, the per-role IAM is in place, and the controlled-channel routing to the AI Office + Member State competent authority, UK AISI, and NIST AISI is understood at the co-authorship level — a template drafted without those preconditions is a template that cannot ship.
