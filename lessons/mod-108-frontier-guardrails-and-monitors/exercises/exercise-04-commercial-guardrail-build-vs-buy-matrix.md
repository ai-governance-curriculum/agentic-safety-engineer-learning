# exercise-04: Commercial Guardrail Build-vs-Buy Matrix

**Estimated effort:** 2 hours (matrix authoring + evidence-collection plan; a real vendor POC runs longer and is a stretch)

## Objective

Author a defensible **build-vs-buy matrix** for one or two layer slots in your FGAC. The matrix scores at least four commercial vendor candidates plus the build baseline against a fixed criterion set drawn from chapter 05, with **evidence in every cell** rather than opinion. The deliverable is the matrix plus a signed **recommendation memo** a procurement / legal / safety-review body would read and act on.

The exercise's failure mode is *the matrix that scores vendors on the vendor's own marketing sheet*. A defensible matrix scores every vendor on the *deployment's* benchmark set, latency profile, data-flow constraints, and hazard taxonomy. Where the operator has not yet run the vendor's endpoint against its own benchmark, the cell is an explicit *unknown* the memo names.

## Prerequisites

- Read chapter 05 (Rule and Flow Guards and Vendor Trade-offs) end-to-end. Read the vendor sections of `resources.md` for the current landing pages.
- Complete exercise 01 or have an equivalent FGAC in hand — you need a layer slot the matrix is scoring for and the hazard classes that slot has to cover.
- Ideally complete exercise 02 as well — the fine-tuned classifier from exercise 02 is the *build baseline* row in the matrix.
- Skim the [OWASP GenAI — LLM and Generative AI Security Solutions Landscape](https://genai.owasp.org/resource/llm-and-generative-ai-security-solutions-landscape/) to confirm the current vendor set. The chapter-05 vendor list is the starting point; the OWASP landscape may add or remove entries.
- Coordinate with the peer procurement / legal function for the contract-terms and vendor-viability rows. Non-numeric criteria still need documented evidence.

## Requirements

Author `build-vs-buy-matrix.md` (or `.yaml`, or a spreadsheet with a committed CSV export) and `recommendation-memo.md`.

### Section 1 — Scope

- **Layer slot(s).** Pick one or two of the FGAC's layer slots to score. Common choices at frontier scope: layer 2 (input classifier), layer 3 (rule / flow guard for prompt-injection detection), layer 4 (output classifier), or a bundled layer-2-plus-layer-4 vendor product. Do not try to score every layer — the exercise's discipline is depth on one or two slots.
- **Hazard coverage.** Which hazard classes from the FGAC's section-1 the slot has to cover. The matrix rows will be scored against these; a candidate that does not cover the class is scored as such, not omitted silently.
- **Deployment constraints.** Latency budget, cost budget, data-flow posture (SaaS admissible or not, jurisdictional constraints, retention posture), regulator posture (EU AI Act obligations, HIPAA, PCI DSS, industry-specific regulators).

### Section 2 — Candidates

At least *four commercial vendor candidates* plus the *build baseline*. Reasonable candidate pool from chapter 05: **Lakera Guard**, **HiddenLayer AIDR**, **Protect AI Guardian**, **Robust Intelligence**, **CalypsoAI**, **HydroX AI**. Add operator-specific vendors your deployment has an existing contract with (Microsoft Azure AI Content Safety, AWS Bedrock Guardrails, Google Cloud Model Armor, etc.) if they are candidates for this slot. The build baseline is a chapter-03 fine-tuned classifier (your exercise-02 output) or, where the slot is a rule guard, an in-house NeMo Guardrails / Guardrails AI configuration.

Each candidate has a candidate-brief:

- Product name and version at authoring time.
- Deployment model (SaaS-only, self-hosted, hybrid, on-prem).
- Documentation and public evidence sources cited (vendor docs, whitepapers, third-party evaluations, benchmark leaderboard entries where they exist).
- Any `needs-research` marks — every product-feature claim not verified at authoring time.

### Section 3 — The matrix

Rows: candidates from section 2. Columns: the criteria chapter 05 pins.

- **Hazard-coverage fit** per hazard class from section 1. Cell: covered / partial / not covered, with the source.
- **Precision / recall per hazard class on the deployment's benchmark.** Cell: measured value + bootstrap 95% CI, or "unmeasured — POC required" if the operator has not run the vendor's endpoint against its own benchmark. Vendor-published numbers are annotated separately, never in the deployment-benchmark cell.
- **Adaptive-attack survival.** Cell: measured (against a bounded red-team from mod-111 or exercise 05), vendor-reported (against a public benchmark like HarmBench / JailbreakBench with citation), or "no evidence".
- **Latency p50 / p95** on the deployment's traffic profile. Cell: measured, or "unmeasured — shadow deployment required".
- **Cost per 1 000 calls** including any per-seat / tier overhead, amortised. Cell: cited from vendor pricing at authoring date; annotate `needs-research: confirm current pricing at authoring time`.
- **Deployment model.** Cell: SaaS-only / self-hosted / hybrid / on-prem; whether that matches the operator's constraint from section 1.
- **Data-flow implications.** Cell: what data the vendor receives; jurisdictional path; retention posture from the vendor's DPA / terms; whether the operator's data-handling policy admits it.
- **Transparency.** Cell: can the operator inspect classifier internals, taxonomy, training data, evaluation set? Commercial vendors usually cannot; note the transparency cost.
- **Vendor viability.** Cell: company stage, funding, notable customers, published SLA, product-support commitments. Cite public evidence.
- **Contract terms.** Cell: termination / data-return / escrow / indemnification posture, from the vendor's public terms or from a signed NDA-scoped contract; annotate the review by legal / procurement peers.
- **Regulator posture.** Cell: whether the vendor carries standing evidence for the operator's regulator (SOC 2, ISO 27001, EU AI Act obligations, industry-specific certifications).

Cells with no evidence are marked *unknown*. An unknown-heavy matrix is a *procurement plan*, not a defensible decision — the recommendation memo names which unknowns must be closed before shipping.

### Section 4 — Weighting

Not all criteria are equal for every deployment. Per criterion, assign a weight (0–10 or a percentage) with a one-line justification. Publish the weighting; do not hide it. Common weightings:

- **Regulated financial services / healthcare** — data-flow, deployment model, regulator posture weighted highest.
- **Research preview / internal tools** — adaptive-attack survival, latency, cost weighted highest.
- **Multilingual global consumer** — hazard-coverage fit (per-language), precision-and-recall-per-class weighted highest.

The weighting is an FGAC section-2 field per slot; the matrix's summary score is the weighted sum, but the memo does not lean on the summary score alone — it reads the individual cells.

### Section 5 — Recommendation memo

A 1–2 page memo naming:

- **The recommendation** — one candidate per slot, or a *composition* of candidates (an open-source layer plus a vendor layer for a different hazard, if that is the defensible choice).
- **The three strongest reasons** — cited to specific matrix cells.
- **The three biggest risks** — cited to specific matrix cells. Where the risk is an unknown, the memo names the POC or shadow-deployment plan that will close it.
- **The vendor-exit path** — chapter 05 pins that a vendor-adopted layer's exit path is a safety-engineering artefact, not just a legal one. Name the fallback (revert to build baseline / swap to second-place vendor / degrade to a fewer-hazard-class posture) and estimate the time-to-exit.
- **The residual-risk fold** — what the vendor cannot cover, and how the FGAC's other layers or the mod-109 safety case handle the residual.
- **The renewal-review cadence** — when the matrix is re-run (usually quarterly to yearly).

### Section 6 — FGAC integration

Update your FGAC's section 2 (layer inventory) with the selected candidate: version pin, ownership, fallback posture. Update section 4 with the target performance the vendor's cell commits to; when the operator runs the vendor's endpoint against its own benchmark (exercise 05 pipeline), replace the target with the measured number.

## Deliverables

Commit to the paired solutions repo:

- `build-vs-buy-matrix.md` (or `.csv` + `.md` for cell-level notes) — the full matrix, cell-by-cell evidence and citations.
- `candidate-briefs.md` — one section per candidate with the section-2 brief.
- `weighting.md` — section 4 with justifications per criterion.
- `recommendation-memo.md` — section 5, signed by you as the safety engineer.
- `FGAC-update.patch` — the specific FGAC edits your recommendation produces (section 2 row, section 4 target).
- `README.md` — the layer slot(s) scored, the recommended candidate(s), and a one-paragraph summary of the residual risk.

## Acceptance criteria

- **At least four commercial vendors plus a build baseline are scored.** Fewer than four vendors is a rejection — the matrix's value is comparison.
- **Every cell has evidence or an explicit `unknown` mark.** Empty cells are a rejection; the matrix's honesty is the point.
- **Deployment-benchmark cells are separated from vendor-published cells.** Conflating the two is a rejection.
- **Adaptive-attack survival is one of the columns.** Static-benchmark-only comparison is a rejection.
- **Weighting is published with per-criterion justification.** Hidden weights are a rejection.
- **The recommendation memo names the vendor-exit path.** Missing exit-path is a rejection; chapter 05 pins that vendor lock-in is a safety-engineering risk.
- **The recommendation memo names the residual-risk fold.** A memo that claims full coverage without residual-risk mention is a rejection.
- **`needs-research` marks are explicit** on every product-feature claim not verified at authoring time. Every vendor's landing page and pricing shifts; the marks are what make the matrix re-runnable.
- **The FGAC's section 2 is updated.** Free-standing matrices without an FGAC update are a rejection — the matrix's output is a decision, and the decision lives in the FGAC.

## Stretch goals

- **Run a real POC on one vendor.** Register for the vendor's free tier or trial, run the operator's benchmark set through the vendor's endpoint, and populate the measured cells with the results. Report the FP / FN vs the vendor's published numbers.
- **Include a "compose vendor A and vendor B" row.** Some slots admit a composition — for example, vendor A on the input side and the build baseline on the output side. Score the composition against the same criteria; often the composition beats any single vendor on hazard-coverage.
- **Run the matrix for a second layer slot.** Different slots often have different winners; the exercise's insight generalises when you see it on more than one slot.
- **Formal-decision framework.** Author the matrix as a scored decision matrix (MCDA / SAW / weighted-sum with sensitivity analysis) rather than a narrative. Report the sensitivity of the recommendation to the weighting.
- **Vendor-viability deep-dive.** For the top-two candidates, publish a one-page vendor-viability brief: funding, headcount, public customers, published SLA breaches, executive changes. Vendor-availability risk is real.
- **Regulator-posture cross-reference.** For each candidate, map their published certifications and compliance evidence to the operator's regulator's specific requirements (EU AI Act Articles 55 / 56 for GPAI, industry regulator's rules). Cite the specific article or clause.

## Guardrails

- **Do not paste vendor NDA-scoped materials into the solutions repo.** Cite the source; do not copy the content. Vendor decks and pricing sheets under NDA remain under NDA.
- **Do not run any vendor POC without procurement and legal sign-off.** POCs can commit the operator to data-processing terms even when free-tier.
- **Do not run a benchmark against a vendor's SaaS endpoint using real production traffic without data-flow approval.** Use a sanctioned benchmark set — public or PII-scrubbed.
- **Do not report vendor-published numbers as deployment numbers.** Vendor numbers are on the vendor's benchmark and are marketing evidence; the deployment's numbers come from the operator's own eval.
- **Do not omit a vendor because you dislike them.** The matrix is a comparison; omitting a candidate biases the recommendation. If a candidate is out of scope for a specific reason (deployment model, regulator posture), score them and name the reason.
- **Do not present the summary score as the decision.** The memo reads the individual cells; the score is a summary, not a substitute for judgement.
- **Do not skip the vendor-exit path.** A vendor adoption without an exit path is a vendor-lock-in incident waiting to happen.
