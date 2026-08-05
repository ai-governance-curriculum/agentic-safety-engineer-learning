# exercise-05: Safety-Case Pitfall Catalogue

**Estimated effort:** 1 hour

## Objective

Package the review discipline from exercise 04 into a **reusable pre-sign-off pitfall catalogue** — a checklist plus example findings plus explicit routing rules that a level-40 author (or a peer reviewer) runs a safety case against *before* it reaches the internal-safety-review body. The output is a `pitfall-catalogue.md` your team could adopt as its own; the *value* of the artefact is that it turns chapter 05's five pitfall classes into a tool that other engineers can pick up and use without re-reading the chapter.

This exercise closes the module. Exercise 01 got you notation-fluent, exercise 02 got you shape-composition-fluent, exercise 03 authored a case, exercise 04 reviewed a case. Exercise 05 turns the review posture from *a skill a specific engineer has* into *an artefact the organisation has* — a checklist that survives when the engineer who wrote it moves on.

## Prerequisites

- Read chapter 05 (Safety-Case Pitfalls and Rebuttal Handling) end-to-end. The five pitfall classes are the *rows* of the catalogue you will build; chapter 05 §*"How each pitfall drives red-team demand back"* is the *remediation-routing* column.
- Complete exercise 04. The findings from your review memo (or several review memos, if you have run multiple) are the *example findings* the catalogue's rows are populated from. If you have not completed exercise 04, complete a lightweight version first — even a 30-minute self-review of your exercise-03 case yields catalogue seeds.
- Skim [Clymer et al. §5](https://arxiv.org/abs/2403.10462) — the paper's own failure-mode catalogue is the reference this exercise extends into a *checkable* form.
- Skim [Bloomfield and Bishop — assurance-case failure catalogue](https://openaccess.city.ac.uk/id/eprint/463/) <!-- needs-research: confirm canonical citation and URL for Bloomfield and Bishop's assurance-case review. --> for cross-industry failure-mode language.
- Look at one or two operational-safety checklist examples from mature safety-critical industries (aviation SMS pre-flight checks, nuclear operating-envelope reviews, rail signalling assurance-case sign-off checklists). The *shape* of a mature checklist — question-and-signal-of-passing pairs, remediation call-outs, explicit *stop-the-line* triggers — is what this exercise is imitating.

## Requirements

### Part A — Catalogue structure (`pitfall-catalogue.md § 1`)

Open the catalogue with a short structure section:

- **Purpose.** One paragraph naming the artefact's intended use: a pre-sign-off checklist a safety-case author (or a peer reviewer) runs the case against before submission to the internal-safety-review body. Not a substitute for review; a pre-audit that reduces review-time findings.
- **When to run it.** One paragraph naming the moments the catalogue applies: pre-first-review-draft, pre-sign-off, on evidence-drift, on framework-drift, on re-authoring after an incident, on external-evaluator engagement kick-off.
- **How to use it.** One paragraph describing the run: read each check-item; note *pass / fail / partial* per item; for each fail or partial, note the finding, the routed remediation, and the closure timeline. The catalogue itself does not sign the case; it *audits* the case.

### Part B — Per-pitfall check items (`pitfall-catalogue.md § 2–6`)

One catalogue section per chapter-05 pitfall class. For each of the five sections, produce:

- **Section header** — the pitfall class name and a one-line intent statement (*"a solution node cites evidence whose provenance chain is incomplete, inconsistent, or non-reconstructible"* for section 2, and so on).
- **Check items** — at least five per section, phrased as *questions the auditor asks the case*, each with a clear *signal of passing* and *signal of failing*. Example items chapter 05 seeds directly:
  - *"Every evidence citation carries a version pin (git tag / doc hash / log-index snapshot / Merkle-root anchor)? [signal of passing: every solution node's citation is hash-anchored or version-tagged; signal of failing: any 'latest' / 'v_current' / undated citation appears.]"*
  - *"Every threat-model hazard maps to at least one sub-goal or is explicitly scoped out with a justification? [signal of passing: hazard-to-goal cross-reference is exhaustive; signal of failing: a hazard is unaddressed and un-justified.]"*
  - *"The δ-bound on the elicitation-gap protocol is quantitative and adversarial-fine-tune-elicited? [signal of passing: a specific numeric δ with the protocol reference; signal of failing: `~2×` / `we did our best` / qualitative claim.]"*
  - *"Every framework citation is version-pinned to a specific published revision? [signal of passing: RSP v.X.Y, Preparedness `Autonomy` scorecard as-of YYYY-MM-DD; signal of failing: 'RSP', 'Preparedness'.]"*
  - *"Every load-bearing leg has ≥ 1 pre-rehearsed counter-argument in the rebuttal register? [signal of passing: the register cites at least one counter-argument per leg with a rehearsed response or explicit acknowledge-and-route; signal of failing: the register is empty or targets minor legs only.]"*

Extend to at least five items per pitfall class. Do not repeat the same item across sections; each section's items should target the specific failure signature that pitfall class produces.

- **Example finding.** For each pitfall class, include one *worked example* — a two-to-three-sentence finding of the shape *"in section 3 of the case, the Sn-DCER-SYNTHESIS citation reads `mod-106 DCER v3.2` without a hash; the version-pin item fails; remediation route is mod-106 sprint S-11 to publish DCER v3.2 with a hash anchor and re-cite in the case."* Findings from your exercise-04 memo are ideal seeds; anonymise the source if reviewing a peer.
- **Remediation-routing rule.** For each pitfall class, name explicitly which upstream module owns the remediation, per chapter 05 §*"How each pitfall drives red-team demand back at earlier modules"*. Include the shape of the demand signal (*"mod-106 owes DCER hash-anchoring; open a mod-106 sprint ticket with the specific artefacts to hash"*).

### Part C — *Stop-the-line* triggers (`pitfall-catalogue.md § 7`)

Not every finding blocks a sign-off. A safety-critical checklist names the *few* findings that must block. For your catalogue, pick three to five *stop-the-line* triggers — the findings that, if present, mean the case cannot be signed as-is even with remediations captured as follow-ups. Suggested candidates from chapter 05:

- Any load-bearing evidence cited in the case cannot be reconstructed by a reviewer with the citation (provenance-chain failure).
- Any hazard from the deployment's mod-102 threat model is uncovered by any leg *and* is not explicitly scoped out with justification.
- The Inability leg is load-bearing at ASL-3-equivalent or above (chapter 03 §*"When Inability is not defensible"*).
- The rebuttal register is empty despite the case shipping to an external-evaluator-review context.
- The framework citation's version pin does not match the framework's current published revision, *and* the case's review period is not tight enough to expire before the current revision applies.

Name yours explicitly. The stop-the-line list is deliberately small — three to five items — because a longer list dilutes the *stop* signal.

### Part D — Route to the peer roles (`pitfall-catalogue.md § 8`)

For each pitfall class, name the peer-role handoff (chapter 06):

- Evidence-provenance-gap remediations that require *release-assurance platform* work (immutable evidence bundles, hash-anchored registers) route to `ai-evaluation-engineer`.
- Argument-completeness-gap remediations that require *control-library* updates (a new control ID, a new framework mapping, a new harm-class definition) route to `senior-ai-governance-architect`.
- Rebuttal-handling remediations that shape the disclosure surface for external audiences route to mod-112.

Naming these routings in the catalogue makes the catalogue *deployable as an organisational tool* — a safety-case author who runs it does not have to figure out on their own who to bring the finding to.

### Part E — Try it on your own case (`pitfall-catalogue.md § 9`)

Close the catalogue with a section that *runs the catalogue on your own exercise-03 case*. For each check item, note *pass / fail / partial* and record the findings. This section serves two purposes: (1) it validates that the catalogue is actually usable (a check item that is unclear or unanswerable in this section is a check-item authoring bug you fix before shipping the catalogue); (2) it produces a self-audit of your exercise-03 case that feeds into a re-authoring pass.

If exercise 03's case was authored well, expect *most* items to pass and *some* to fail — that mixture is the fluency signal. A catalogue-run that scores everything a pass on the first attempt is either a suspiciously well-authored case or a suspiciously permissive catalogue; either way, iterate.

## Deliverables

Commit to your exercise-solution area:

- `pitfall-catalogue.md` — Parts A–E structured as one document. This is the artefact your team could adopt.
- `catalogue-run-<deployment-slug>.md` — the Part-E audit run on your exercise-03 case, as a standalone file the next author can point to as a worked example.

## Acceptance criteria

- **All five pitfall classes have ≥ 5 check items** (Part B). Fewer than five per class is under-covered.
- **Every check item has a *signal of passing* and a *signal of failing*.** Un-signalled items are questions, not checks. A checklist without pass/fail signals does not survive its first user.
- **Every pitfall section has a *worked example finding*.** Un-exampled sections are theory, not tool. The example is what a first-time user reads to calibrate the pass/fail signals.
- **Every pitfall section has an explicit *remediation-routing rule*** — the specific upstream module (chapter 05 §*"How each pitfall drives red-team demand back"*) and the shape of the demand signal.
- **Three to five *stop-the-line* triggers** (Part C). Fewer than three dilutes the signal; more than five dilutes the *stop*.
- **Every pitfall class has an explicit peer-role handoff** (Part D). Findings that require platform / architecture / disclosure work route to the correctly-named peer role (chapter 06).
- **The Part-E self-run produced *mixed* results** — some passes, some fails, some partials. A single-outcome self-run is suspicious.
- **The catalogue is *pickup-usable* by another engineer** — someone who has read chapter 05 but has never seen your catalogue should be able to run it on a case in under an hour. If your best-guess for pickup time exceeds two hours, the catalogue is too complex; tighten.

## Stretch goals

- **Score-weight the catalogue.** Not all pitfalls are equal; assign a *weight* (1–5) to each check item and produce a composite pre-sign-off score. Calibrate against the case's tier (ASL-2 tolerates a lower score than ASL-3-equivalent). The composite is not a substitute for a signer's judgement, but it is a summary statistic reviewers can consume.
- **Author *diff-checks* against framework drift.** For each pitfall class, name the specific framework update that would newly trigger a fail (e.g., *"if RSP is updated to tighten the ASL-3 uplift bound, the moving-target-threshold checks fire on every case authored against the prior bound"*). This makes the catalogue framework-drift-aware.
- **Contribute the catalogue to the paired `agentic-safety-engineer-solutions` repo** as a shared team artefact. The value of a checklist scales with the number of authors who run it against a common baseline.
- **Add an *AISI-response* section** — a set of check items specifically targeting the AISI *"could not verify"* finding-classes. This is a chapter 05 §pitfall 5 specialization; frontier deployments engaging AISI will benefit.
- **Publish a *lessons-learned* register** alongside the catalogue. As the catalogue is used on real cases, catalogue-item revisions are captured with the case that surfaced the revision. The register turns the catalogue into a *living artefact* — the exact posture chapter 01 §*"the review cycle"* names.
- **Cross-map the catalogue against Clymer et al. §5.** For each check item, cite the specific Clymer §5 failure-mode it operationalises. This positions the catalogue as *"a checkable form of Clymer §5"* rather than *"our team's opinion."*

## Guardrails

- Do not turn the catalogue into a *substitute for a signer*. The catalogue audits; the signer decides. Chapter 04 §*"common misreadings"* is explicit that author and signer are separated by design; the catalogue is neither.
- Do not treat the catalogue as a *permanent artefact*. Frameworks update, elicitation methodology matures, incidents produce new pitfall classes. Version the catalogue and re-review annually or on major framework change.
- Do not let the catalogue balloon. Chapter 05 named *five* pitfall classes deliberately; a catalogue with fifty is not more useful, just harder to run. Keep the five sections; extend depth within a section rather than adding new sections.
- Do not include check items that require access the pre-sign-off auditor does not have. If a check item asks *"was the DCER re-graded by three domain experts?"* and the auditor cannot see the grader roster, the check is un-runnable — remove it or restate it as a citation-check on the roster's presence.
- Do not commit real deployment names, personnel names, or organisation-specific IDs in the catalogue's example findings. Use placeholders; the catalogue is a shared artefact.
- Do not treat a passing catalogue-run as the case being *good*. Chapter 05 §*"Common misreadings"* is explicit that a mature case has *fewer* pitfalls, not zero; the catalogue reduces the review-time surprise, not the case's residual risk.
