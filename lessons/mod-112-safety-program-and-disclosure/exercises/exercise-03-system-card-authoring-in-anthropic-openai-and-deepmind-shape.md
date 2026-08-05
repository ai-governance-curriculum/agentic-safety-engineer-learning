# exercise-03 — System card authoring in Anthropic / OpenAI / DeepMind shape

**Estimated effort:** 3 hours
**Prerequisite chapters:** 03 primary; 02 (tier-decision memo as reduction input); 04 (regulator-facing redaction discipline the card inherits in stronger form); 06 (release-assurance handoff). Interface: mod-109 (safety case as the reduction source).

## Objective

Author **three system-card variants for one model version** — one in each of the three current genres chapter 03 pins (Anthropic-shape, OpenAI-shape, DeepMind-shape) — reducing from the same underlying safety case and the same tier-decision memo. The variants must be internally consistent (same evidence, same residual-risk framing) and externally distinct (each renders the evidence in the genre a reviewer familiar with that lab's cards would expect). Produce alongside them a per-claim reduction-decision log that walks chapter 03's four buckets (disclose-directly / summary / redact / withhold) and a runbook that defends the authorial choices. The exercise is the disclosure-engineering exercise chapter 03 was written to close.

## Problem statement

Chapter 03's load-bearing insight is that system-card authoring is a *reduction* problem: the safety case (mod-109) is a hundreds-of-pages internal artefact, the card is 40–120 pages, and the reduction is where disclosure-engineering happens. Most authors get this wrong on the first attempt in one of three ways: they treat the card as marketing (the *wrong-role* failure), they copy the safety case (the *audience-collapse* failure), or they author in whichever genre is closest to hand without recognising that the genre carries framing commitments (the *genre-agnostic* failure). This exercise forces the correct decomposition by making the learner author the *same* underlying evidence in *three* genres — the disciplines that survive across all three are chapter 03's five load-bearing invariants; the differences are the genre-specific framing that a safety engineer must be fluent in.

Pick **one model version** — a plausible surrogate is fine; do not use a real unreleased model. Reuse the tier-decision memo from exercise 02 (or an equivalently defensible surrogate memo you author quickly) as the reduction input. Assume the safety case exists at mod-109 shape; reference its claims by ID rather than re-authoring it. Author three card variants for that model version, one per genre, then a per-claim reduction log that assigns every material safety-case claim to one of chapter 03's four buckets, then a runbook that defends the authorial choices against chapter 03's rubric.

The exercise is deliberately not about writing beautiful prose — it is about internalising the invariants, the audience separation, and the reduction discipline that make a card defensible. A learner who can defend every section against chapter 03's rubric has done the exercise; a learner who has produced three well-written cards that cannot survive the rubric has not.

## Requirements

Produce five artefacts.

### Artefact A — `system-card-<model>-anthropic-shape.md`

The Anthropic-shape variant. Follow the section shape chapter 03 pins for the genre: model summary → training data and process → intended and out-of-scope use → capability evaluations (per capability domain) → safety evaluations (per risk category) → Responsible Scaling Policy determination → deployment mitigations → known limitations → red-team disclosure summary → areas of ongoing work.

- Each section carries a per-section **audience label** — `developer-facing`, `external-stakeholder-facing`, or `shared` — per chapter 03's audience-separation discipline. The voice within a section is consistent with the label.
- The **RSP determination** is stated with the pre-registered threshold referenced (by threshold ID from the tier memo, not by inventing a number). The elicitation methodology used to test against the threshold is disclosed at the section level chapter 03's invariant 1 requires.
- The **safety-eval results** section carries every claim in the form *"score [Y] under [named elicitation regime] against threshold [Z]"* per chapter 03's invariant 2. Score placeholders (`<score-from-mod-106>`) are acceptable at this altitude; inventing specific numbers is not.
- The **deployment mitigations** section names the mechanism per invariant 3: the mod-107 EACC summary, the mod-108 monitoring posture, the human-in-the-loop escalation flow, the credential scoping. Naming does not require full engineering detail; it requires a legible chain.
- The **known limitations** section is disclosed at the actionable level per invariant 4 — the section a downstream integrator can code against, not a generic hallucination disclaimer.
- The **residual-risk / areas-of-ongoing-work** section reduces the safety case's residual-risk statement per invariant 5. It cannot claim less residual risk than the safety case argues.
- The **red-team disclosure** section names programmes, findings classes that survived to launch, and mitigation engagement. External attributions are noted where the red-teamers consented (in this exercise, mark consent slots explicitly).

Length target for the card variant: enough to carry all five invariants across the section shape; not a page-count exercise. Placeholder numeric fields (`<threshold-id>`, `<score-from-mod-106>`, `<red-team-programme-name>`) are the correct discipline where the underlying primary source has not been pinned.

### Artefact B — `system-card-<model>-openai-shape.md`

The OpenAI-shape variant. Follow chapter 03's OpenAI genre section shape: model overview → training approach → external red-teaming → Preparedness Framework evaluations (per risk category) → deployment mitigations → monitoring and enforcement posture → known limitations → open problems.

- Per-section audience labels as in Artefact A.
- The **Preparedness Framework scorecard** section carries an explicit grading (low / medium / high / critical or equivalent placeholder) per tracked risk category, with the evidence citation per invariant 2. The scorecard entries are the load-bearing content chapter 03 names for the genre.
- The **external red-teaming** section is up-front (not buried) per the genre's convention. Attributions are named where consent slots are marked; unattributed findings are called out as such.
- The **deployment posture** is stated per scorecard grading — what is engaged at deployment when the grading is `<value>` — per invariant 3.
- Invariants 1, 4, 5 apply as in Artefact A.
- The same underlying claims (from the same safety case) appear here as in Artefact A, but framed in the Preparedness vocabulary. A reviewer familiar with the OpenAI genre should read this variant as OpenAI-shape, not as Anthropic-shape with the vocabulary find-and-replaced.

### Artefact C — `system-card-<model>-deepmind-shape.md`

The DeepMind-shape variant. Follow chapter 03's DeepMind genre section shape: model summary → capabilities and evaluations → Frontier Safety Framework determinations → responsible development mitigations → deployment restrictions and access → limitations.

- Per-section audience labels as in Artefacts A and B.
- The **CCL determinations** are stated per capability domain with evidence citation per invariant 2. The mitigation tier engaged per CCL is named per invariant 3.
- The **deployment restrictions** section explicitly discloses rate limits, access gates, and jurisdictional constraints where they exist for the surrogate model.
- Invariants 1, 4, 5 apply as in Artefacts A and B.
- The genre historically renders shorter than Anthropic / OpenAI cards for equivalent releases (<!-- needs-research: verify recent Gemini system-card lengths and any shift toward longer disclosure formats -->). Do not pad; the shorter variant is not a defect if the five invariants are carried.

### Artefact D — `system-card-<model>-reduction-log.yaml`

The per-claim reduction-decision log. One YAML document. For every material safety-case claim the tier memo references (or the surrogate memo enumerates), one entry:

- `claim_id` — the safety-case claim reference (`<sc-claim-id>` from mod-109) or the tier-memo claim reference (`<tier-memo-claim-id>`) if the claim originates in the memo.
- `claim_shape` — one-sentence description of what the claim asserts (capability, safety-eval outcome, mitigation efficacy, residual-risk statement, red-team finding). No payload text; shape only.
- `bucket` — one of `disclose_directly`, `disclose_at_summary`, `disclose_with_redaction`, `withhold` per chapter 03's four reduction buckets.
- `rationale` — one paragraph justifying the bucket assignment against chapter 03's discipline. If `withhold`, the paragraph must defend against chapter 03's warning that over-use of the bucket erodes trust with regulators and AISI.
- `card_variant_treatment` — how each of the three card variants (Anthropic, OpenAI, DeepMind) renders the claim. In most cases the treatment is identical across variants; where a genre changes the treatment (e.g. the OpenAI scorecard renders a claim more prominently than the Anthropic areas-of-ongoing-work section), the difference is noted with the genre-specific reason.
- `evidence_pointer` — pointer into the safety case (mod-109) or the tier memo (exercise 02). Where the underlying evidence includes CBRN / cyber-offense payload references, the entry carries **pointer + sha256 only**; the payload content is never inlined per chapter 04's redaction discipline.
- `co_authorship_reviewer_slot` — the reviewer slot that co-signs the bucket assignment: `safety_engineering_lead` (accuracy), `external_counsel` (legal exposure), `external_affairs` (regulator posture). Every entry names at least one slot; claims routed to `disclose_with_redaction` or `withhold` name all three.
- `regulator_disclosure_delta` — pointer to whether the same claim appears differently in the mod-112 exercise-04 regulator-facing disclosure (which shares evidence but not always audience). A one-liner is enough; the exercise-04 artefact is the full treatment.
- `residual_risk_link` — for claims that bear on the residual-risk framing (invariant 5), the pointer to the safety-case residual-risk statement the card cannot under-disclose against.

### Artefact E — `system-card-<model>-runbook.md`

An 800–1200 word runbook covering:

- **Model-version choice and reduction source.** Which model version (surrogate), which tier-decision memo (exercise 02 or a defensible surrogate), and which safety-case reference (mod-109 by structure, not by content). Chapter 03's *"the safety case is the primary reduction target"* is the frame.
- **Genre-choice rationale.** For each of the three variants, the framing commitments the genre carries and how the variant honours them. Chapter 03's section on *"the three current system-card genres"* is the authority; a runbook that cannot distinguish Anthropic-shape from OpenAI-shape framing has not internalised the chapter.
- **Audience-separation approach.** How the per-section audience labels were assigned and how the voice within each section was kept consistent. Cite chapter 03's audience-separation discipline; call out any sections where the content is genuinely shared and the label reflects that.
- **Invariant-check self-audit.** For each of chapter 03's five load-bearing invariants (elicitation footprint, threshold pin, mechanism naming, actionable limitations, residual-risk match to the safety case), walk through how each of the three card variants satisfies it. A section that misses an invariant is flagged in the runbook and remediated before submission.
- **Reduction-log methodology.** How the four buckets were applied claim by claim; the co-authorship review path; the discipline for keeping the `withhold` bucket narrow. Cite chapter 03's *"disclose / summary / redact / withhold"* enumeration and its warning against over-use of `withhold`.
- **Release-assurance handoff.** The developer-facing content of each variant is co-authored with the `ai-evaluation-engineer` peer role (level 35) per chapter 06. Name the handoff shape — which sections are co-authored, which are safety-engineer-owned, and how the peer's release-assurance packaging feeds back into the card's developer-facing sections.
- **Cross-artefact traceability.** The tier memo (exercise 02) is the reduction input; the safety case (mod-109) is the reduction source; the regulator-facing disclosure (exercise 04) shares evidence but not always audience; the incident-response contract (chapter 05) drives potential card addenda. State each cross-reference explicitly.
- **Threats to validity.** Genre drift between the pinned card versions and the current published cards; scorecard / RSP / FSF vocabulary revision cycles; the risk of a card variant that reads as genre-agnostic (find-and-replace vocabulary) rather than genre-fluent; the risk of a residual-risk section that under-discloses relative to the safety case; the risk of a `withhold` bucket that a regulator reads as evasive.

## Starter guidance

- **Read chapter 03 end-to-end with a highlighter on the five invariants and the four reduction buckets.** They are the load-bearing rubric; every artefact is checkable against them. A learner who cannot recite the five invariants by artefact-D time has not internalised the chapter.
- **Pin one model-version surrogate before writing any card content.** A card whose subject drifts across the three variants is one whose reduction is not reproducible. The surrogate is a fixed point; every variant reduces from the same tier memo and safety-case shape.
- **Read one recent card from each of the three genres before authoring.** The chapter's primary-sources block enumerates them; the version-pin comment marker is why. If you cannot cite a specific card version as the genre exemplar, mark it `<!-- needs-research: ... -->` and proceed on the section-shape and framing commitments chapter 03 pins.
- **Do not invent numbers.** Score placeholders (`<score-from-mod-106>`), threshold placeholders (`<threshold-id>`), CCL placeholders (`<ccl-level>`), page-count placeholders (never assert a card is N pages long as a factual claim) are the correct discipline. Chapter 03's own primary-sources block uses `<!-- needs-research: ... -->`; the exercise inherits it.
- **The reduction log is where the exercise's discipline lives.** A card variant that looks well-written but whose underlying reduction log is thin is a card that will fail a reviewer's audit. Author the reduction log alongside the card variants, not after.
- **Audience labels are per section, not per variant.** A given section (say, safety-eval results) may have the same audience label in all three variants. The label discipline is intra-variant (voice consistency within a section), not inter-variant.
- **The three variants share evidence, not framing.** The scorecard vocabulary lives only in the OpenAI variant; the CCL vocabulary lives only in the DeepMind variant; the ASL determination lives only in the Anthropic variant. A cross-variant find-and-replace produces genre-agnostic prose, which chapter 03 warns is not disclosure — it is copy-paste.
- **The `withhold` bucket is a bucket of last resort.** Chapter 03 is explicit that over-use erodes trust. Every `withhold` entry must survive a *"why not summary?"* / *"why not redact?"* challenge; if it cannot, promote it to a less-restrictive bucket.
- **Payload discipline (chapter 04) is not optional here.** Where a reduction-log entry references CBRN or cyber-offense payload evidence, the entry carries pointer + sha256 only. Payload content is never inlined in any committed artefact. A reduction log with inlined payload text is a hard fail regardless of the surrounding quality.
- **The release-assurance handoff is a real interface, not a footnote.** Chapter 06 names `ai-evaluation-engineer` (level 35 peer) as the co-author of developer-facing content. The runbook records which sections are co-authored and which are safety-engineer-owned; a card whose developer-facing content is safety-engineer-only is missing the handoff the chapter names.
- **Consistency with the tier memo is a checkable property.** The card's tier-relevant claims (RSP / Preparedness / FSF) reduce from exercise 02's memo; if the card asserts a determination the memo does not support, the runbook must name the delta and defend it. Silent divergence is a finding.
- **Consistency with the regulator-facing disclosure (exercise 04) is a checkable property.** The card and the regulator submission share evidence but not always audience. Where a claim renders differently across the two, the reduction log's `regulator_disclosure_delta` field records the delta.
- **Do not author the safety case here.** mod-109 owns the safety case; the exercise references its claims by ID. A learner who begins drafting safety-case content in this exercise has crossed the module boundary.

## Acceptance criteria

- All three card variants (Artefacts A, B, C) exist for the **same** model-version surrogate and reduce from the **same** tier-decision memo and the same safety-case references. Cross-variant evidence consistency is verifiable.
- Each variant carries all five of chapter 03's load-bearing invariants: capability summary, safety-eval results (with elicitation footprint per invariant 1 and threshold pin per invariant 2), deployment mitigations (with mechanism naming per invariant 3), known limitations (at the actionable level per invariant 4), red-team disclosure. Skipping any invariant in any variant is a hard fail.
- Each variant follows the genre section shape chapter 03 pins: Anthropic-shape has the RSP determination section and areas-of-ongoing-work section; OpenAI-shape has the Preparedness scorecard section and external red-teaming up-front; DeepMind-shape has the CCL-determinations section and deployment-restrictions section. A variant that is genre-agnostic (a find-and-replace of vocabulary across variants) fails the acceptance test.
- Every section in every variant carries a per-section audience label (`developer-facing`, `external-stakeholder-facing`, `shared`) per chapter 03's audience-separation discipline. Voice within a section is consistent with the label.
- Each variant's residual-risk framing matches (does not under-disclose relative to) the safety case per invariant 5. The runbook's invariant-check self-audit verifies this explicitly.
- `system-card-<model>-reduction-log.yaml` (Artefact D) contains one entry per material safety-case claim, with `claim_id`, `claim_shape`, `bucket` (one of chapter 03's four), `rationale`, `card_variant_treatment` across all three variants, `evidence_pointer`, `co_authorship_reviewer_slot`, `regulator_disclosure_delta`, and `residual_risk_link`. Every `disclose_with_redaction` or `withhold` entry names all three reviewer slots.
- **Payload discipline satisfied.** No CBRN / cyber-offense payload content appears in any committed artefact. Where the reduction log references such payload evidence, the entry carries pointer + sha256 only per chapter 04's redaction discipline. A committed artefact that inlines payload text is a hard fail.
- The `withhold` bucket is narrow. Every `withhold` entry survives a *"why not summary? why not redact?"* challenge in the rationale field per chapter 03's warning against over-use.
- `system-card-<model>-runbook.md` (Artefact E, 800–1200 words) covers model-version choice, genre-choice rationale per variant, audience-separation approach, invariant-check self-audit against all five, reduction-log methodology, release-assurance handoff to `ai-evaluation-engineer` (level 35 peer) per chapter 06, cross-artefact traceability to exercise 02 / mod-109 / exercise 04 / chapter 05, and threats to validity.
- Every unverified factual claim (specific published-card URLs and versions, specific NIST publication IDs, specific card page counts, specific scorecard grading vocabularies) is marked `<!-- needs-research: ... -->`. Chapter 03's own primary-sources block uses the same discipline; the exercise inherits it.
- No specific model scores, specific benchmark numbers, or specific published-card citations are asserted as fact without a `<!-- needs-research: ... -->` marker. Placeholders (`<score-from-mod-106>`, `<threshold-id>`) are the correct discipline.

## Stretch goals

- **Author a fourth variant intentionally violating one invariant.** In a clearly marked branch of the runbook (`anti_pattern: true`), author a fourth variant that omits (say) invariant 4's actionable-limitations discipline and record what a reviewer's finding on the omission would read like. Teaching-by-inversion makes the invariant visible.
- **Draft the addendum-trigger contract with the incident-response chapter (chapter 05).** Chapter 05 names the incident-response contract as the driver of card addenda. Draft the trigger contract in prose: which incident classes drive an addendum, which drive a new card version, and who signs off. The contract lands as a pointer from the runbook, not as an executable artefact.
- **Cross-reference the exercise-04 regulator-facing disclosure at claim level.** For every reduction-log entry whose `regulator_disclosure_delta` is non-trivial, produce the paired regulator-side treatment (headed to exercise 04's artefact). This previews the *"same evidence, different audience"* discipline chapter 04 formalises.
- **Author a card-refresh cadence contract.** Chapter 01 names the pre-deployment safety-eval touch-point as the card's cadence key; write out how a model-version bump on one axis (a fine-tune, a new tool integration, a monitoring-posture change) drives which variant refreshes and at what latency. The cadence contract lands as a pointer from the runbook.
- **Sketch a genre-comparison table.** One matrix — rows: chapter 03's five invariants and audience-separation discipline; columns: Anthropic / OpenAI / DeepMind genre — populated with the per-genre framing commitments (RSP vs Preparedness vs FSF vocabulary, ASL vs scorecard vs CCL determinations, areas-of-ongoing-work vs open-problems vs limitations). The table is a reviewer-facing aid; it is not a substitute for the three variants.

## Deliverable location

Personal notes or private repo. Do **not** commit the card variants, the reduction log, or the runbook into this course repo. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference card-authoring skeleton. Where the reduction log references CBRN or cyber-offense payload evidence, **payload content is never committed** — pointer + sha256 only per chapter 04's redaction discipline. The card variants themselves are drafts of a public artefact, but the drafting environment is private; the *published* card is the organisation's release artefact, not the learner's course exercise. Payload discipline (chapter 04) is not optional — verify the pointer-and-hash discipline against the reduction log before any artefact leaves the drafting environment.
