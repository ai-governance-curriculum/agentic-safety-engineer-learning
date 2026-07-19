# 06 — Coverage, Reproducibility, and Harmful-Payload Discipline

## Motivation

Chapters 02 through 05 build the machinery. This chapter is where the machinery's output becomes an *artefact* — a coverage-matrix report a reviewer signs, a seeded-attack replay bundle a reviewer replays, a harmful-payload storage discipline a reviewer audits. Three disciplines converge here: *coverage reporting* (what did the run cover; where are the gaps), *reproducibility* (can a reviewer replay any red cell exactly), and *harmful-payload storage* (where the attack corpora live, who can read them, and what a legal-review gate looks like for CBRN / cyber-offense payloads). Each is a program-scope commitment; none can be improvised.

The load-bearing insight is that scaled red-teaming's output is *not the attacks*. The output is the *reproducible verdict on the target's behaviour under the attacks* — a verdict a safety case (mod-109) can cite, a disclosure (mod-112) can quote, a containment engineer (mod-107) can act on. A run whose attacks succeeded but whose replay bundle is incomplete is a run whose verdict cannot be defended; it is an *anecdote at scale*. A run whose replay bundle is complete but whose harmful payloads are stored in the source repository is a run whose program cannot pass a legal review. The coverage-matrix report, the reproducibility bundle, and the harmful-payload discipline are what make the program *defensible*.

This chapter walks each of the three disciplines and closes the CMC contract this module opened in chapter 01.

## Primary sources

- **[UK AISI — Inspect logs and reproducibility documentation](https://inspect.aisi.org.uk/log-viewer.html)** — the Inspect log-store's reproducibility model. <!-- needs-research: confirm the current canonical URL for Inspect log-store documentation. -->
- **[NIST AI RMF 1.0 — Manage function, MANAGE-1.4](https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook/Manage/Manage_1)** — the reference posture for AI-risk management artefact reproducibility. <!-- needs-research: verify the specific subcategory citation and URL. -->
- **[MLCommons — AILuminate benchmark methodology](https://mlcommons.org/benchmarks/ai-luminate/)** — the methodology on evaluation-set hosting outside the source repository is one reference for the discipline this chapter codifies. <!-- needs-research: pin the AILuminate benchmark version and its methodology-hosting doc. -->
- **[Anthropic — Responsible Disclosure Policy for AI Vulnerabilities](https://www.anthropic.com/responsible-disclosure-policy)** — the reference posture on harmful-payload handling in vulnerability writeups. <!-- needs-research: confirm the current title and URL. -->
- **[EU AI Act — Article 55 (general-purpose AI models with systemic risk)](https://artificialintelligenceact.eu/article/55/)** — the regulatory shape for evaluation and red-team documentation the CMC ultimately feeds. <!-- needs-research: pin the current published article text; the Act has been amended. -->

Version-pin these when they are cited in a CMC or a replay bundle.

## Coverage-matrix reporting

The coverage matrix report is the CMC's primary output. It is what a reviewer opens.

### Report shape

A defensible report has, per cell:

- **Cell key.** `(attack_technique, behaviour_category, model_version, decoding_config, guardrail_configuration, scaffold_variant)` — every axis the CMC's section-2 enumerated.
- **Sample size.** The number of attack attempts; the seed count; the best-of-N configuration.
- **ASR + confidence interval.** Point estimate + a documented CI method. Wilson score interval on a proportion is a defensible default; the CI is *not* an afterthought.
- **Diversity metric.** The chapter-03 metric per the CMC's section-4 diversity contract; the number of unique semantic clusters at threshold, or the equivalent.
- **Judge identity and calibration snapshot.** Judge model + rubric + calibration statistics at the time of the run.
- **Cost.** The dollar-cost of the cell; the query-count; the wall-clock.
- **Verdict.** `red` (ASR + diversity above thresholds), `amber` (one but not both above thresholds), `green` (below both thresholds), `undetermined` (budget-exhausted or judge-drift-quarantined).
- **Delta vs previous rev.** ASR delta, diversity delta, cost delta. This is what a reviewer's eye actually goes to on a per-rev report.

At the matrix level, the report includes:

- **Coverage completeness.** What fraction of the axes' enumeration actually ran; which cells were skipped, and why (budget, dependency, opt-out).
- **New failure modes.** Cells that transitioned from green / amber to red since the last rev.
- **Closed failure modes.** Cells that transitioned from red to green — a mitigation landed, a rev fixed a regression.
- **Undetermined cells.** Cells the reviewer must accept as *not covered this run* and follow up on.
- **Cost-budget vs actual.** The matrix run's aggregate cost.

### Report anti-patterns

- **A single global ASR.** *"The matrix run scored 12% ASR overall"* is an uninterpretable number — it hides high-ASR behaviour categories under low-ASR ones. Every reader must ignore it; some readers will not, and then it will land in a disclosure.
- **ASR without diversity.** Discussed in chapter 03; a repetitive successful attack corpus produces a lie.
- **ASR without CI.** *"We saw 12% ASR"* on n=10 seeds is not the same finding as 12% on n=1 000. The CI is what carries the message.
- **Verdict without judge-calibration snapshot.** The verdict inherits the judge's calibration; without the snapshot, a reviewer six months later cannot know whether the verdict was on a well-calibrated judge or a drifted one.
- **Report without cost.** Cost drives cadence; a report without cost is a report a program-owner cannot use to plan the next run.

### Reporting cadence

- **Per-run report.** Every matrix run produces a report; the report lands in the CMC's section-7 consumer contract's target locations.
- **Per-rev delta report.** The rev-to-rev delta is a distinct artefact — smaller, sharper, focused on the deltas. This is what the tier-decision letter references.
- **Quarterly program report.** The aggregation of runs across a quarter — coverage-completeness trend, cost trend, new-technique count, closed-failure-mode count. This is what an organisation-scope safety review reads.

## Seeded-attack replay bundle

The replay bundle is what makes any cell's finding *reproducible*. Without it, a red cell is an assertion; with it, a red cell is a *demonstrated* fact a reviewer can replay months later.

### Bundle contents, per cell

The bundle carries the specific hashes and artefacts that pin the run:

- **Decoding config hash.** Temperature, top-p, top-k, presence / frequency penalties, seed if set, sampling implementation identifier. Frontier API-served targets often expose a subset; the bundle records what was set and flags what could not be pinned.
- **Model version.** Weights hash for weights-available models; provider version tag + provider build ID for API-served models. The provider build ID is what distinguishes a *"gpt-4o"* on day 1 from *"gpt-4o"* on day 90 when the provider has silently rev'd.
- **Prompt hash.** The exact prompt bytes, or a hash and a pointer into the harmful-payload store (below) if the prompt is a harmful payload.
- **Tool-response hashes.** For agentic runs, every tool-response the target consumed is hashed; the hashes are recorded so a replay can substitute an identical tool response.
- **Judge identity.** Judge model weights hash (or provider version tag), rubric prompt hash, decoding config.
- **Attacker identity.** For LLM-vs-LLM runs, the attacker checkpoint hash and its rubric prompt hash.
- **Framework identity.** Inspect version, PyRIT version, garak version + probe-list hash, Promptfoo version — whichever ran the cell.
- **Seed.** The RNG seed if the loop uses one; the sample-selection seed if the corpus is sampled.
- **Environment identity.** Container image digest for the runner; hardware class if it matters (some sampling implementations are hardware-non-deterministic).

### What a reviewer does with the bundle

- **Verifies a specific ASR claim.** The reviewer picks a red cell, downloads the bundle, replays the attacks against the pinned model version, and checks whether ASR matches within the reported CI.
- **Extends the finding.** The reviewer runs the same cell with a *different* judge (e.g., a human panel) to check whether the LLM-judge verdict was calibrated on this cell.
- **Reproduces the finding under a different scaffold.** The reviewer runs the same attacks against a different tool-scaffold to check whether the finding is scaffold-specific.

The bundle is not a code artefact; it is *evidence*. The mod-109 safety-case's inability-leg citation of a matrix cell is only defensible if the reviewer can, on request, replay the cell from the bundle.

### Bundle storage

- **Inspect log store.** For Inspect-runner cells, the primary bundle is the Inspect log — Inspect's log format is designed for reproducibility. Pin the log ID; the log itself lives in the eval-plumbing peer role's trace store.
- **A supplementary bundle store** for the non-Inspect frameworks (PyRIT's own memory, garak's JSON reports, Promptfoo's run store). The CMC section 6 names these.
- **Harmful-payload references, not payloads.** The bundle carries *hashes and pointers* to harmful payloads; it does not carry the payloads themselves in the bundle store. The payloads live in the harmful-payload store (below).

### What is not in the bundle

- **Nothing that reveals the operator's internal infrastructure beyond the run.** The bundle carries hashes and pointers, not credentials or secrets.
- **Nothing that identifies real users** whose interactions seeded the corpus. The corpus provenance (chapter 04) carries the traceability internally; the bundle does not expose it.

## Harmful-payload storage discipline

The final leg. The harmful-payload store is where the attack corpora — the seed prompts, the fine-tuning corpora, the CBRN / cyber-offense payloads — live. Getting this discipline right is what separates a red-team program that can pass a legal review from one that cannot.

### The four rules

- **The store lives outside the source repository.** A private HuggingFace organisation, an S3 bucket with a per-role IAM policy, an on-prem object store with equivalent controls. The rule: `git log` on the operator's source repository must never reveal a harmful payload, ever. Not in a fixture file, not in a test, not in a docstring.
- **Access is per-role.** The corpus's IAM permits the red-team engineer, the fine-tuning-pipeline principal, and the CMC-report signing principal to read it. The general engineering population does not have read access. A payload the operator's SWE population cannot see is a payload that cannot leak through a misplaced screenshot in a bug tracker.
- **Redaction in issue trackers and logs.** When a harmful payload is *discussed* in an issue, a PR, a Slack channel, a bug tracker — it is *referenced by hash*, not by text. Automated redaction is preferable (a pre-commit / pre-comment hook that catches payload strings); manual discipline is a fallback.
- **A legal-review gate for CBRN / cyber-offense payloads.** Before a payload of this class enters the corpus, a legal review has signed off. The review checks jurisdictional considerations, licence status, and the operator's authorised-red-team-scope. The signed-off status is recorded in the corpus provenance.

### The storage contract, concretely

- **Hosting.** Private HuggingFace organisation with restricted-access datasets; S3 bucket with `s3:GetObject` scoped to specific principals; equivalent GCS or Azure Blob controls. The CMC section 5 names the specific mechanism.
- **Access control.** A per-role IAM policy the peer role (`ai-infra-security`) authors and reviews. Access is *not* through personal credentials; access is through workload-identity-authenticated service principals with short-lived tokens (the mod-107 chapter-01 PoLA pattern applied here).
- **Provenance metadata per payload.** The record of how the payload entered the corpus — red-team-win, public-corpus rev, augmentation — with the reviewer sign-off.
- **Retention and deletion.** Payloads are retained under the CMC's disclosure-window; after the window closes, deletion is a documented procedure, not a `rm` in an S3 console. The peer role's WORM / lifecycle rules apply.
- **Audit logging.** Access to the store is logged and monitored; anomalous access (a service principal reading materially more than its normal quota, a personal principal accessing at all) fires an alert. This is the mod-107 chapter-03 audit-log pattern applied to the harmful-payload store.

### CBRN / cyber-offense payloads — the legal-review gate

- **Trigger.** A payload's harm-category is CBRN-uplift, cyber-offense-with-operational-tradecraft, or another jurisdictionally sensitive category. The categorisation happens *at ingest* — the ingest workflow tags the payload's category and routes accordingly.
- **Reviewers.** Internal legal counsel; the operator's responsible-disclosure lead; the safety-review body's designated reviewer. The specific reviewer list is a CMC section-5 field.
- **Review artefacts.** A short brief describing the payload's provenance, the intended use (red-team training / evaluation / disclosure), the jurisdictions the operator operates in, and the operator's authorised-red-team-scope commitments.
- **Outcome.** Approve for corpus inclusion (with any content restrictions); approve for evaluation only (do not enter training corpus); reject (payload is not admitted to the store). The outcome is recorded in the payload's provenance and is reviewable.
- **Cadence.** The gate is per-payload for the sensitive categories; a batch review is acceptable for lower-sensitivity payloads. The CMC section 5 records the cadence.

### Anti-patterns

- **Corpus in the source repo, gated by `.gitignore`.** `.gitignore` is not a security control. A branch, a fork, a mis-configured CI job leaks it.
- **Corpus in a private repo, accessible to all engineers.** A leaked screenshot, a misplaced PR reviewer, a compromised laptop. Access is *per-role*, not per-organisation-membership.
- **Payload in an issue title.** The issue tracker's search-index carries it forward. Reference payloads by hash.
- **No legal-review gate.** The one time it matters is one time too many.

## The consumer contract — routing to downstream artefacts

The CMC's section 7. Every downstream artefact that consumes the coverage matrix is enumerated with its routing shape and cadence.

- **Safety cases (mod-109).** The inability-leg citation shape — cell key, ASR, CI, diversity, judge version, replay-bundle pointer.
- **Disclosures (mod-112).** The system-card / AISI-report / Article-73-incident-report citation shape — matrix version, aggregated per-behaviour-category ASR, method-summary.
- **Containment updates (mod-107).** The tool-abuse cells' verdicts; the wrapper-testing cells' verdicts; findings routed to the mod-107 engineer for EACC updates.
- **Guardrail retraining (mod-108).** The guardrail-effectiveness cells' verdicts; the training-set curation of successful attacks under the CMC's diversity contract; the guardrail engineer's retraining cadence.
- **Tier decisions (mod-101).** The tier-decision letter's citation of the matrix — matrix version, tier-relevant cells' verdicts, elicitation-gap notes.
- **Attacker training (chapter 04).** New successful attacks feed the attacker's corpus; the routing is bidirectional.

Each routing carries a signed handoff: the CMC's report reaches the consumer, the consumer signs receipt, the CMC records the sign-off. A silent routing is a finding.

## Closing the CMC — the seven sections signed

Chapter 01 opened the CMC with seven sections. This chapter closes them:

- **Section 1 (scope).** Opened in chapter 01; refined per-run as the matrix runs.
- **Section 2 (axes).** Opened in chapter 01; the specific axis values populated by chapters 02–05.
- **Section 3 (orchestrator inventory).** Chapter 02 populated it; version pins live here.
- **Section 4 (judge contract).** Chapter 05 populated it.
- **Section 5 (attack-corpus contract).** Chapter 04 populated the corpus content; this chapter populates the storage-discipline and legal-review gate.
- **Section 6 (reproducibility contract).** This chapter populates the seeded-replay bundle shape.
- **Section 7 (consumer contract).** This chapter populates the routing.

The signed CMC is exercise 06 and exercise 07's output. Exercise 06 walks the reproducibility bundle end-to-end; exercise 07 walks the harmful-payload storage workflow end-to-end. A learner completing both has authored a defensible CMC for one concrete deployment.

## Cross-boundary handoffs to mod-109 and mod-112

Two specific handoffs close this chapter, mirroring the mod-107 chapter-06 handoff pattern.

### To mod-109 (safety cases)

- **Evidence shape.** The safety-case's inability leg cites a matrix cell as: cell key, CMC version, matrix run ID, ASR + CI, diversity metric, judge version + calibration snapshot, replay-bundle pointer.
- **Evidence provenance.** The safety-case author validates the cited cell was in-scope, judged by a calibrated judge, and replayed successfully by a reviewer. mod-109's chapter-N (evidence-provenance) walks the check.
- **Cadence.** Safety cases are authored per material deployment; the CMC's per-rev delta report is the trigger for a safety-case rev.

### To mod-112 (disclosure)

- **Evidence shape.** The system card / AISI report cites the matrix's *aggregated* verdicts — per-behaviour-category ASR, coverage-completeness statement, method summary. Per-cell detail lives in the CMC; the disclosure summarises.
- **Harmful-payload disclosure discipline.** The disclosure does *not* reproduce harmful payloads. Payloads are referenced by hash, redacted in the disclosure text, and made available only to authorised reviewers under the harmful-payload store's access control.
- **Cadence.** Disclosures are authored per material deployment change; the CMC's coverage report is the technical backbone.

The pattern is the mod-107 chapter-06 boundary pattern: this module produces the evidence, mod-109 / mod-112 produce the argument / narrative that cites it. Neither can produce a defensible artefact without the other.

## Interfaces

- **Chapter 01 (CMC scope).** Closes here; the seven sections are signed.
- **Chapters 02–05.** Every earlier chapter's output lands in a specific CMC section; this chapter closes the sections.
- **`ai-eval-engineer` (peer, level 30).** The trace store the replay bundle points into; the run-artefact storage; the CI-artefact bundle shape.
- **`ai-infra-security` (peer, level 35).** The harmful-payload store's IAM, the audit-log for store access, the retention lifecycle, the workload-identity for service principals.
- **Legal counsel.** The CBRN / cyber-offense legal-review gate; the disclosure-window review.
- **mod-107 (containment).** Findings from tool-abuse cells route to the containment engineer; the EACC picks up the routing.
- **mod-108 (guardrails).** Guardrail-effectiveness cells feed retraining; the CMC's diversity contract shapes the training-set curation.
- **mod-109 (safety cases).** Evidence citation shape; per-cell replay-bundle pointer.
- **mod-112 (disclosure).** Aggregated evidence citation; harmful-payload redaction discipline; regulator-facing summarisation.
- **`fine-tuning-engineer` (peer, level 30).** The attacker-fine-tuning training-log lives alongside the corpus; the corpus store discipline extends.

## Common misreadings to avoid

- **"The report is the matrix."** No. The matrix is the *runs*; the report is the *interpretation*. A run without a report is uninterpretable data.
- **"The replay bundle is nice-to-have."** No. Without the bundle, every cell's verdict is unfalsifiable. The safety-case and the disclosure cannot defensibly cite an unfalsifiable verdict.
- **"Harmful payloads in a private repo are fine."** No. Access is per-role, not per-organisation. A private repo accessible to the general engineering population is not access-controlled.
- **"The legal-review gate slows the program."** Yes, deliberately. The gate is what makes the program legally defensible. A program without one runs faster and is not defensible.
- **"Disclosure means publishing the payloads."** No. Disclosure summarises; payloads are referenced by hash and made available to authorised reviewers only.
- **"CI can automate the harmful-payload store."** CI can automate the workflow, but not the review. The legal-review gate is a human step; the CI enforces routing to the human, not replacement of the human.

## Summary

- Coverage-matrix reporting is per-cell — ASR + CI + diversity + judge calibration + cost + verdict + delta. Global aggregates are misleading and must be avoided in the report body.
- Seeded-attack replay bundles pin every hash and pointer a reviewer needs to reproduce a red cell. The bundle is *evidence*, not code.
- Harmful-payload storage discipline: outside the source repo, per-role IAM, hash-referenced in issue trackers and logs, legal-review gate for CBRN / cyber-offense.
- The CMC's seven sections are closed here; the signed CMC is the module-level artefact.
- Downstream routing — safety cases (mod-109), disclosures (mod-112), containment updates (mod-107), guardrail retraining (mod-108), tier decisions (mod-101) — is contractual, with signed receipts.
- Cross-boundary handoffs to mod-109 and mod-112 close the module; this module produces the evidence, they produce the argument / narrative.
