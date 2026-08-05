# exercise-04: Rebuttal and Evidence-Provenance Review

**Estimated effort:** 2 hours

## Objective

Play the reviewer. Take a safety case authored by someone else — a peer engineer's exercise-03 output, one of the published safety cases from the module's primary sources (Goemans et al., a redacted frontier-lab RSP artefact where available), or a *deliberately-flawed* case you can construct from chapter 05's pitfall catalogue if no peer artefact is available — and adversarially review it. The output is a **review memo** naming every locatable finding, classified against chapter 05's five pitfall classes, with a remediation route back to the specific upstream module the finding demands work from.

The exercise is the mirror image of exercise 03. Exercise 03 is the author's craft; this is the reviewer's craft. A level-40 safety engineer does both — authors cases and reviews others' cases — and the discipline of *routing findings back to upstream modules* is what makes reviews constructive rather than adversarial-only.

## Prerequisites

- Read chapter 05 (Safety-Case Pitfalls and Rebuttal Handling) end-to-end. The five pitfall classes and the rebuttal-handling four-move pattern are the material this exercise leans on hardest.
- Skim chapter 04 §step 7 for the *break-the-argument* pass — the review posture chapter 04 asks the author to pre-run against themselves is the same posture this exercise asks you to run against someone else's case.
- Skim [Clymer et al. §5 — common failure modes](https://arxiv.org/abs/2403.10462) for the paper's own catalogue.
- Complete exercise 03 (or, at minimum, have skimmed chapter 04's running example) so that the shape of a safety case is familiar.
- Pick the source case. Options in order of preference:
  - **A peer engineer's exercise-03 case.** Pair up. You review theirs; they review yours. Coordinate on a shared submission format.
  - **A published safety case artefact** — the Goemans et al. worked template's fully-instantiated example (if the paper ships one), a redacted RSP safety-case example from Anthropic if publicly released, a redacted Preparedness Framework safety case from OpenAI if publicly released. Cite the published artefact with version pin.
  - **A deliberately-flawed self-authored case.** Take your own exercise-03 case, deliberately introduce three or four pitfall-instances from chapter 05 (an under-bounded δ, a hidden compositional assumption, an unpinned solution citation, a moving-target threshold, an unresponded-to *"could not verify"* finding), and hand it to yourself 48 hours later so that your eyes are cold. This is a worse test than a peer review but a better test than nothing.

## Requirements

### Part A — Pre-review setup (`review-memo.md § 1`)

Open the memo with a short setup section:

- **Case under review** — the specific artefact, its version pin, and its author (name / handle / *"self-authored, deliberately flawed"*).
- **Framework context** — which framework the case was authored against (RSP / Preparedness / FSF / EU AI Act), so that framework-drift and tier-language findings can be classified correctly.
- **Reviewer posture** — one paragraph naming the reviewer stance you are adopting: internal-safety-review body (RSO / Preparedness Committee / Safety Board) or external evaluator (AISI, EU AI Office, third-party red-team). The posture matters — an AISI reviewer's *"could not verify"* findings and an RSO's *"tier-language mismatch"* findings look different (chapter 05 §*"Modelling on RSP / Preparedness / FSF and on AISI"*).

### Part B — Structured walk of the case (`review-memo.md § 2`)

Walk the case's *seven-step* structure (chapter 04) and record findings per section. For each of the case's sections, note:

- **What is present.** Two-sentence summary of the section's content.
- **What is missing or under-defended.** Findings against the section, if any.
- **Chapter-05 pitfall class** for each finding — `EVIDENCE-PROVENANCE`, `ARGUMENT-COMPLETENESS`, `ELICITATION-GAP-AMBIGUITY`, `MOVING-TARGET-THRESHOLD`, or `REBUTTAL-HANDLING`. Every finding gets exactly one primary classification; sub-findings can carry secondary tags.
- **Detection signature.** How you found the finding — the specific question you asked that the case could not answer. Chapter 05 §*"How a reviewer catches it"* per pitfall is the reference for the questions the reviewer actually asks.
- **Severity.** *blocking* (case cannot ship until fixed), *major* (case can ship with a named residual-risk statement and a follow-on plan), or *minor* (finding is worth capturing but does not affect the sign-off).
- **Node ID(s) affected.** Which specific goal / strategy / solution / context / assumption node the finding lands on. If the finding is against the case as a whole, note it — a case-wide finding is itself a signal that the case is under-modularised (chapter 05 §*rebuttal-handling pattern* move 1).

### Part C — Focused evidence-provenance audit (`review-memo.md § 3`)

Pick the *three* most load-bearing evidence citations in the case (typically: the DCER cited by the Inability leg, the EACC + wrapper-log cited by the Control leg, and the control-eval or monitor-coverage cited by the Control leg). For each, produce a **provenance-completeness check**:

- Does the citation carry a **version pin** (git tag, doc hash, log-index snapshot, Merkle-root anchor)?
- Does the citation name the **model fingerprint** the evidence was collected against?
- Does the citation name the **protocol / methodology** used?
- Does the citation name the **grader / evaluator / operator** — internal safety team, external domain-expert panel, third-party red-team, AISI?
- Does the citation name the **date / period** the evidence covers?
- Can a reviewer with the citation *reproduce* the evidence (not necessarily re-run it, but at least locate the artefact and its inputs)?

Any *no* is a finding. Chapter 05 §pitfall 1 is the reference.

### Part D — Rebuttal-register stress-test (`review-memo.md § 4`)

If the case has a rebuttal register (chapter 05 §*rebuttal-handling pattern*, exercise 03 step 7), pick *two* entries and stress-test them. For each entry:

- **Is the rehearsed response actually responsive** to the counter-argument stated? Or is it a paraphrase of the counter-argument that dodges the challenge?
- **Would a reviewer accept the response?** Consider both the internal-safety-review reviewer (who has to sign the case) and the external evaluator (whose *"could not verify"* findings the register may target).
- **If the response is *acknowledge and route*** — is the routing target explicit (a specific module, sprint, closure date) or a hand-wave?

If the case has no rebuttal register, that is itself a finding — chapter 05 §pitfall 5 — and Part D writes *what the register would need to contain* for the case's top three load-bearing legs to be defensible.

### Part E — Remediation route (`review-memo.md § 5`)

For every finding in Parts B–D, produce a remediation route back to the specific upstream module. Chapter 05 §*"How each pitfall drives red-team demand back at earlier modules"* is the reference table:

- `EVIDENCE-PROVENANCE` findings → route back to mod-104 / 105 / 106 / 107 / 108 / 110 with an explicit request (version-pin the artefact, publish the grader roster, anchor the log stream to a Merkle root, etc.).
- `ARGUMENT-COMPLETENESS` findings → route back to mod-102 (hazard-map completeness) or to this module (chapter 04 step 6, assumption-and-context capture).
- `ELICITATION-GAP-AMBIGUITY` findings → route back to mod-106 (widen the elicitation protocol, tighten δ) and mod-110 (add adversarially aligned control-eval evidence).
- `MOVING-TARGET-THRESHOLD` findings → route back to mod-101 (framework-version-pin the citation) and to this module (chapter 04 step 1, tighten the review period).
- `REBUTTAL-HANDLING` findings → route back to this module (chapter 04 step 7 iteration; rebuttal register authorship).

Each remediation route names: the specific module, the specific artefact to update, and the expected closure timeline. Vague routing (*"talk to the mod-106 team"*) is chapter-05 pitfall 5 in disguise; specific routing (*"mod-106 sprint S-11 should publish DCER v3.3 with adversarial-fine-tune elicitation and a δ ≤ 1.2× target, expected close 2026-05-15"*) is the level-40 posture.

### Part F — Ship / do-not-ship recommendation (`review-memo.md § 6`)

Close the memo with an explicit recommendation:

- **Ship** — the case is defensible as-is.
- **Ship with named remediations** — the case is defensible provided the *N* blocking findings close before ship, and the *M* major findings are captured in the residual-risk statement.
- **Do not ship** — the case has fundamental gaps that cannot close in the current review period; re-author with the remediations.

Explain the recommendation in one paragraph. If you recommend *ship*, the paragraph names the residual risk you accept; if you recommend *do not ship*, the paragraph names the specific pitfall class(es) that drove the recommendation.

## Deliverables

Commit to your exercise-solution area:

- `review-memo.md` — Parts A–F above, structured as one document with numbered sections.
- `findings-table.md` — a table view of every finding: **ID**, **section**, **pitfall class**, **severity**, **node ID**, **remediation route**, **owner module**, **expected closure**. This is the programmatic form of the memo, suitable for tracking follow-ups.
- If reviewing a peer's case: send `review-memo.md` and `findings-table.md` back to the author. Pair-review is bidirectional; expect their review of your case in return.

## Acceptance criteria

- **Every finding is classified against exactly one chapter-05 pitfall class** (with optional secondary tags). Un-classified findings are prose narrative, not review-memo findings.
- **Every finding carries a detection signature** — the specific question the reviewer asked. *"The case seems weak here"* is not a finding; *"the DCER's δ is stated as `~2×` without a fine-tune-elicitation reference; how do you bound it under adversarial fine-tuning?"* is.
- **Every finding carries a severity classification** — blocking, major, minor. Un-severity-classified findings do not support a ship recommendation.
- **Every load-bearing evidence citation in Part C is checked against all six provenance questions.** Any *no* becomes a finding.
- **The remediation-route table names a specific module and a specific artefact per finding.** Vague routing is a fail; specific routing is a pass.
- **The ship / do-not-ship recommendation is explicit** and explained in one paragraph. A memo without a recommendation is not a review memo.
- **The memo is *constructive*** — findings are opportunities for the case to improve, not personal attacks on the author. Chapter 05 §*"How each pitfall drives red-team demand back"* frames this correctly: findings are the case's *product*.
- **The reviewer posture named in Part A is consistent through Parts B–F.** An internal-safety-review posture and an external-AISI posture ask different questions; do not silently switch mid-memo.

## Stretch goals

- **Cross-review two different cases** side by side. Compare the shapes of the findings — is one case pitfall-prone in evidence-provenance while the other is pitfall-prone in elicitation-gap-ambiguity? Meta-findings across a portfolio of cases inform where the *organisation's* upstream investment should sit.
- **Author the *"if I were the author, what would I have done differently at step N"* section.** Turning the review into a workflow-improvement signal is what makes the review posture teachable to other engineers.
- **Convert the findings-table into a *rebuttal-register seed* for the author.** Every finding you generated is a pre-rehearsed counter-argument the author can populate the rebuttal register with. This is the exercise-04-to-exercise-03 feedback loop chapter 05 §*rebuttal-handling pattern* is optimising for.
- **Practice the rebuttal-handling pattern *live*.** Set up a 30-minute session with the case author; deliver two of the findings verbally; observe the four-move response pattern (locate, classify, respond, update). The written memo is the deliverable; the live session is the skill-transfer.
- **Author a *"pattern language" appendix* to the memo** — three findings from this case that you have seen before (in your own exercise-03 case, in a published safety case, in a prior peer review). The pattern is the seed for exercise 05's pitfall catalogue.

## Guardrails

- Do not weaponise the review. The posture is *"findings are the product,"* not *"I found N flaws in your work."* Chapter 05 §*"Common misreadings"* is explicit that findings against the case are not findings against the author.
- Do not review your own case *simultaneously* with authoring it. Author it (exercise 03), let 24–48 hours pass, then review it if you are self-reviewing. Fresh eyes catch pitfalls the author's own eyes cannot.
- Do not silently invent evidence gaps. If the case's citation is thinly documented and you cannot verify from the artefact, note it as *"citation is thinly documented; reviewer could not verify because <specific reason>"* — do not upgrade to *"evidence is fabricated"* without cause. AISI's *"could not verify"* language is the calibration reference.
- Do not skip Part E's remediation route. A review memo without remediation routes is *adversarial-only*; the module's posture is *constructive-adversarial*.
- Do not commit real personnel names for peer reviews without their consent. Use handles / roles for peer artefacts.
- Do not treat published safety cases from the primary sources as *"reviewable to be found flawed."* They are working templates; the point of reviewing them is to internalise their structure, not to score points. Cite politely and cite specifically.
