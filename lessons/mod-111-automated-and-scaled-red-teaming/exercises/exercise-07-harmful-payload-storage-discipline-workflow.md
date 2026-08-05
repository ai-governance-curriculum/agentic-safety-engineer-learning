# exercise-07 — Harmful-payload storage discipline workflow

**Estimated effort:** 2 hours
**Prerequisite chapters:** 06 (helpful: 01, 04, 05).

## Objective

Author the **org-scope harmful-payload storage discipline workflow** that operationalises chapter 06's four rules — corpus outside the source repository, per-role access, redaction in trackers and logs, legal-review gate for CBRN / cyber-offense — into a set of documents a reviewer can audit and an on-call engineer can follow. The output is the **CMC section 5 (attack-corpus contract) artefact** in signed form, with the section-6 (reproducibility) storage pointer shape and the section-7 (consumer contract) role list stitched in. This exercise's deliverable *is* the discipline; the acceptance criteria audit that the deliverable itself does not violate the discipline.

## Problem statement

Pick **one concrete program scope** — the same target family your other mod-111 exercises used, or a hypothetical *"Program X"* if you are working in the abstract. For that scope, specify how a harmful-payload store is provisioned, who reads it, how it is referenced in issue trackers and chat, how a CBRN / cyber-offense payload gets a legal-review sign-off before it enters the corpus, and how a reviewer verifies six months later that the discipline was followed. Every rule in chapter 06's four-rule set becomes a concrete workflow step with a named role and a named artefact.

The load-bearing insight from chapter 06 is that scaled red-teaming's *output* is the coverage-matrix verdict, not the attack corpus itself — but the verdict is only defensible if the corpus that produced it was handled under a discipline a legal reviewer will accept. A program with a great matrix run and no storage discipline is a program whose next disclosure lawyer will refuse to sign. Chapter 06 lists the anti-patterns explicitly (corpus in the source repo gated by `.gitignore`; corpus in a private repo accessible to all engineers; payload text in issue titles; no legal-review gate); this exercise ships the workflow that makes each anti-pattern *structurally* impossible for your program.

Pick **one** hosting mechanism from chapter 06's menu — private HuggingFace organisation with restricted-access datasets, S3 bucket with a per-role IAM policy, GCS bucket with equivalent controls, or Azure Blob with equivalent controls. Do not paper over the choice; specify the mechanism's access-group shape, the workload-identity pattern (chapter 06 cites the mod-107 chapter-01 PoLA pattern), and the retention / audit-log surface. IAM syntax is peer-role (`ai-infra-security`, level 35) territory; the shapes go in this exercise, the specific policy JSON gets its final review from that peer role.

Payload discipline (chapter 06) is not optional — and it applies to *this exercise's own deliverables*. Nothing you commit may contain example harmful strings, real bucket names, real principal IDs, or real CBRN briefs. Role names, ACL shapes, template scaffolds; that is the altitude.

## Requirements

Produce four artefacts.

### Artefact A — `cmc-<program>-payload-workflow.md`

The end-to-end workflow document — the CMC section 5 body a reviewer signs. Entries:

- `program_scope` — the deployment / model family / evaluation programme this contract covers. One CMC per program per chapter 01; do not conflate.
- `store_mechanism` — one of `hf_private_org | s3 | gcs | azure_blob | on_prem_object_store`. Chapter 06's menu; pick one and commit.
- `store_identifier_shape` — how the store is named (e.g., `hf://<org>/<dataset>`, `s3://<bucket>/<prefix>`) with placeholder tokens only. No real names.
- `ingest_workflow` — the ordered steps from *"a red-team engineer produces a payload"* to *"the payload is in the store with provenance"*. Each step names a role and a produced artefact. The CBRN / cyber-offense branching step is explicit (routes to Artefact D).
- `reference_workflow` — how the payload is *referenced* elsewhere: by `sha256`, by `store_id`, by CMC cell key. Chapter 06's "hash-referenced in issue trackers and logs" rule is the anchor.
- `retention_and_deletion` — retention window (aligned to CMC disclosure window per chapter 06), the deletion procedure, the WORM / lifecycle-rule shape the peer role enforces.
- `audit_log_shape` — the fields the store emits per access (principal, action, object hash, timestamp, source workload identity), the anomaly triggers (personal principal accessing at all; service principal exceeding quota), and the alert routing.
- `periodic_review` — the cadence at which a reviewer walks the access list, the departing-personnel exit-review procedure, and the sign-off record.
- `cmc_section_pointers` — the pointer into CMC section 6 (reproducibility bundle store location) and section 7 (consumer contract role list) so this artefact stitches cleanly into the signed CMC.

The document is a workflow, not a specification of a tool; do not name specific vendor products beyond the storage-mechanism menu above.

### Artefact B — `cmc-<program>-access-matrix.yaml`

The RACI-style access matrix — who can read what, at what altitude. Each row is a role × artefact pairing:

- `role` — a *role name*, not a person and not a real access-group ID. `red-team-engineer`, `attacker-fine-tuning-pipeline-principal`, `cmc-report-signing-principal`, `judge-rationale-reviewer`, `replay-bundle-reviewer`, `redacted-findings-consumer`, `internal-legal-counsel`, `responsible-disclosure-lead`, `safety-review-body-designated-reviewer`, `ai-infra-security-peer`, `general-swe-population`. Extend the list as the program requires; do not shorten it.
- `artefact_class` — the object being accessed: `raw_corpus`, `payload_provenance_metadata`, `judge_rationale_text`, `replay_bundle_hashes`, `replay_bundle_raw_traces`, `coverage_matrix_report_full`, `coverage_matrix_report_redacted`, `legal_review_brief`, `audit_log`, `disclosure_summary`.
- `access` — one of `read | none`; write access is mediated by the ingest workflow, not this matrix.
- `justification` — one-sentence *why*, tied to the role's job. `general-swe-population` must be `none` on `raw_corpus` and `judge_rationale_text` per chapter 06's per-role rule.
- `credential_shape` — the auth pattern: `workload_identity_short_lived_token`, `oidc_federated_service_principal`, `human_sso_break_glass_audited`. No real credentials, no real group IDs, no real principal ARNs.
- `review_cadence` — how often the entry is re-reviewed (quarterly is a defensible default; sensitive rows may be per-rev).

At the matrix level, include:

- `denied_by_default` — the assertion that any role × artefact pair not listed is `access: none`. Chapter 06's "access is per-role, not per-organisation-membership" is enforced here.
- `break_glass_procedure` — the audited path for out-of-band access (an incident-response request, a legal-hold request). Names the audit-log entries it produces.
- `separation_of_duties` — the rule that no single role can both ingest a payload *and* sign its legal-review brief. Chapter 06's gate is a check on the ingest workflow; separation of duties keeps the check meaningful.

### Artefact C — `cmc-<program>-redaction-policy.md`

The redaction policy for issue trackers, PRs, chat channels, and log stores — the enforcement surface for chapter 06's "hash-referenced in issue trackers and logs" rule. Sections:

- `covered_surfaces` — enumerate: GitHub Issues / Jira / Linear, GitHub PR descriptions and comments, Slack / Teams channels (with the specific channel-scope), the runner's stdout log, the judge's rationale log, screenshot-sharing tools. Any surface not enumerated is *out of the discipline* and a finding.
- `never_contains_harmful_content` — the specific fields per surface that must never contain payload text: issue title, issue body, PR title, PR description, PR review comments, commit message subject and body, log lines emitted to a shared aggregator, screenshot captions and image contents. Chapter 06's "payload in an issue title" anti-pattern is the canonical case.
- `reference_shape` — the *approved* way to reference a payload: `payload:<sha256>` or `store_id:<opaque-id>` or `cmc_cell:<key>`. The policy specifies which form each surface uses.
- `enforcement_mechanisms` — the tooling shape (do not implement; specify): the issue-template scaffold that prompts the reporter to reference by hash; the pre-commit hook shape that scans for known payload hashes or high-entropy strings suspected to be payloads; the log-scrubbing sidecar shape that redacts payload text before it reaches the shared aggregator; the chat-bot shape that flags suspected payload strings in monitored channels.
- `example_shapes` — the *shape* of a compliant reference and the *shape* of a non-compliant reference. Chapter 06's discipline: reference shape only; no example harmful strings whatsoever, not even as *"here's what to redact"*. A compliant example is `"reproduces payload:abcd1234... on cell (crescendo, cbrn-uplift, gpt-4o-2024-08-06, greedy, guardrail-v3, tool-scaffold-a)"`; a non-compliant example describes the *shape* of what would appear (a verbatim jailbreak string, a reproduction of a judge rationale that quotes the model's harmful output) without ever quoting one.
- `violation_response` — what happens when a violation is caught: the auto-redact + notify path for a pre-commit / pre-comment hook catch, the manual-cleanup path for a post-hoc find, the incident-record path for a repeated offender. The tracker's search-index is a chapter 06-cited failure mode (payloads persist in search even after the visible field is edited); the response procedure calls this out.
- `training_and_onboarding` — the shape of the training every principal listed in Artefact B receives before they get access. A one-page acknowledged summary is a defensible minimum.

### Artefact D — `cmc-<program>-legal-review-gate.md`

The runbook for the CBRN / cyber-offense legal-review gate. **Length: 800–1200 words.** Sections:

- **Trigger.** Which payload categories fire the gate. Chapter 06 names CBRN-uplift and cyber-offense-with-operational-tradecraft; add any jurisdictionally sensitive categories the program handles. The categorisation happens *at ingest*, not after the fact; the ingest workflow (Artefact A) tags the payload's category and routes accordingly. Describe the tagging step and the taxonomy the tagger uses (mod-104 / mod-105 harm taxonomies are the reference; cite the specific taxonomy version).
- **Reviewers.** The role list — internal legal counsel, responsible-disclosure lead, safety-review body's designated reviewer. Chapter 06 names these; the runbook records the role names, not real people. Note the separation-of-duties rule from Artefact B (the payload's producer is not on the review panel).
- **Review artefact — the brief.** The shape of the short brief that goes to reviewers. Chapter 06 lists: provenance, intended use (red-team training / evaluation / disclosure), jurisdictions the operator operates in, the operator's authorised-red-team-scope commitments. The brief does *not* quote the payload; it references by hash. Reviewers get store-read access on the referenced hash under Artefact B's `internal-legal-counsel` row. Give the brief's field list; give the length target (one page); give the templating shape (do not commit a filled brief).
- **Review procedure.** The steps: brief filed → reviewers acknowledged → review window (SLA in business days, per your jurisdiction and program cadence — `<!-- needs-research: -->` if you cannot pin the specific SLA a comparable program uses) → reviewer questions and clarifications routed through the responsible-disclosure lead → decision recorded. The decision is one of: `approve_for_corpus_inclusion` (optionally with content restrictions), `approve_for_evaluation_only` (payload may be used to evaluate but not enter any training corpus), `reject` (payload is not admitted to the store). Chapter 06's outcomes are the reference.
- **Cadence.** Per-payload for the sensitive categories; batch review acceptable for lower-sensitivity categories. Give the batching rules: batch size, batch composition (no cross-category batching for the sensitive ones), review cadence. Reference chapter 06's *"the gate is per-payload for the sensitive categories"* rule.
- **Provenance recording.** The specific fields the payload's provenance record acquires after review: `legal_review_id`, `reviewers`, `decision`, `restrictions`, `review_date`, `next_review_trigger`. The provenance record lives in the store per Artefact A; this section names the fields the review adds.
- **Audit and revisitation.** How the review is re-examined — when a jurisdiction changes, when the operator's authorised-red-team scope narrows, when a downstream consumer (mod-112 disclosure, mod-109 safety case) escalates a concern. The revisitation trigger is a first-class workflow step, not a *"we'll re-look if someone asks"*.
- **Regulatory alignment.** Reference EU AI Act Article 55 (GPAI systemic-risk red-team documentation) and Article 73 (serious-incident reports) obligations the review supports — this gate is a load-bearing input to both. Mark the specific article wordings `<!-- needs-research: -->` unless you can cite the published text. NIST AI RMF Manage function reference is appropriate here as well. Anthropic Responsible Disclosure Policy and OpenAI Coordinated Vulnerability Disclosure Policy are useful posture references for the disclosure-side handoff.
- **Anti-patterns.** Enumerate chapter 06's *"no legal-review gate"* misreading (*"the one time it matters is one time too many"*), the *"gate after ingest"* misreading (categorisation happens at ingest), the *"gate applied to lower-sensitivity only"* misreading, and the *"legal review is a rubber-stamp"* misreading. Explicitly name each and how the runbook prevents it.

The runbook does not contain any filled brief, any specific CBRN content, any real reviewer names, or any specific legal-jurisdiction advice. It is a *process* document.

## Starter guidance

- **Read chapter 06 twice before writing anything.** The four-rule set on line 101–106 is the anchor; the anti-patterns on line 126–129 are the acceptance-criteria mirror. If your workflow does not structurally prevent each anti-pattern, it is not the workflow.
- **Pick the hosting mechanism early and commit.** Chapter 06's menu is a menu; picking a private HF org and later half-migrating to S3 is what produces a workflow with contradictory access-control shapes. Pick one, name the peer role that runs it (`ai-infra-security`, level 35), stop.
- **Roles, not people; shapes, not credentials.** The access matrix (Artefact B) is a *role* × *artefact-class* matrix. If you catch yourself typing an ARN, a group ID, a personal email, or an actual IAM policy JSON, stop and rewrite as a shape. This exercise's discipline is the exercise.
- **Reference-by-hash is the workflow, not a suggestion.** Chapter 06's issue-tracker rule is enforced by the redaction-policy artefact's reference-shape section. If a compliant reference looks like `payload:<sha256>` in your policy, every workflow step in Artefact A that touches the tracker must also produce a `payload:<sha256>` reference.
- **The pre-commit and log-scrubbing hooks are *shapes*, not implementations.** Specify what the hook scans for, what it does on a match, and where the match is routed. The peer role (`ai-infra-security`) implements; you specify.
- **Separation of duties is not optional.** The payload's producer cannot be on the payload's legal-review panel. Encode this in Artefact B (separation-of-duties note) and Artefact D (reviewer role list).
- **Legal review is a *human* step; the workflow routes to it, does not replace it.** Chapter 06's *"CI can automate the workflow, but not the review"* misreading is the specific failure to avoid. The runbook (Artefact D) describes how CI routes to the human; the human decision is recorded, not synthesised.
- **The CBRN gate's cadence is per-payload for sensitive categories.** Batching for sensitive categories is chapter 06's specifically-called-out anti-pattern; do not batch these.
- **The audit-log surface is the retrospective verification.** A reviewer six months later verifies the discipline was followed by reading the audit log, not by asking the team. If the log shape in Artefact A does not carry the fields a reviewer needs (principal, action, object hash, timestamp, source workload identity), the discipline is not auditable.
- **The exit-review is where the discipline breaks in practice.** A departing engineer with lingering read access is chapter 06's *"per-organisation-membership"* failure mode after the fact. The periodic-review cadence in Artefact A includes the exit-review procedure explicitly; a role's revocation is same-business-day, not *"we'll get to it"*.
- **Regulatory pins need `<!-- needs-research: -->`.** EU AI Act Article 55 and Article 73 have been amended; NIST AI RMF's Manage function has specific subcategories the reference cites. Do not paraphrase from memory; either cite the primary source or mark the claim for research.
- **Cite mod-104 / mod-105 / mod-106 as producers of the payloads this workflow governs.** The workflow does not care which framework (chapter 02) produced the payload; it cares about the payload's category (which mod produced it and under what taxonomy) and its sensitivity class (which routes the legal-review gate).
- **Cite mod-112 as the downstream consumer.** Chapter 06's *"disclosure summarises; payloads are referenced by hash"* rule is what mod-112 depends on; this exercise's workflow is what makes that rule enforceable.
- **Cite `ai-infra-security` (level 35 peer) for the IAM / bucket-policy / KMS work.** Your workflow specifies the *shape*; the peer role reviews and implements the concrete policy. Do not write the policy in this exercise.
- **Nothing you author here contains a payload.** Not as an example, not as a *"here's what a violation looks like"*, not as a redacted-with-asterisks placeholder. The deliverable is the discipline; the discipline forbids the payload.

## Acceptance criteria

- ✅ `cmc-<program>-payload-workflow.md` picks one `store_mechanism` from chapter 06's menu and specifies the ingest workflow, reference workflow, retention / deletion, audit-log shape, periodic review, and pointers into CMC sections 6 and 7. The CBRN / cyber-offense ingest branch routes to Artefact D explicitly.
- ✅ `cmc-<program>-access-matrix.yaml` lists at least the roles in the Artefact B requirements list, with `read | none` per artefact class, a `credential_shape` per row, a `denied_by_default` assertion, a `break_glass_procedure`, and a `separation_of_duties` rule. `general-swe-population` is `none` on `raw_corpus` and `judge_rationale_text`.
- ✅ `cmc-<program>-redaction-policy.md` enumerates the covered tracker / chat / log surfaces, names the fields per surface that must never contain payload text, specifies the `payload:<sha256>` reference shape, and specifies the enforcement mechanisms (issue-template, pre-commit hook shape, log-scrubbing sidecar) as *shapes*.
- ✅ `cmc-<program>-legal-review-gate.md` is 800–1200 words, covers trigger / reviewers / brief shape / review procedure / cadence / provenance recording / audit-and-revisitation / regulatory alignment / anti-patterns, and specifies per-payload cadence for CBRN and cyber-offense-with-operational-tradecraft categories.
- ✅ **No harmful content in any committed artefact.** No example jailbreak strings, no example CBRN content, no example judge rationale text, no reproduced payload of any kind — not even as a *"here's what to redact"* illustration. Reference shapes only.
- ✅ **No real credentials, principals, or infrastructure identifiers.** No real bucket names, no real ARNs, no real HuggingFace org handles, no real principal IDs, no real Slack channel IDs, no personal emails. Placeholder tokens (`<org>`, `<bucket>`, `<prefix>`) only.
- ✅ **No filled legal-review brief.** Artefact D specifies the brief's field list and length target; it does not contain a filled brief for any payload category.
- ✅ Regulatory citations (EU AI Act Article 55, Article 73; NIST AI RMF Manage function subcategories; Anthropic Responsible Disclosure; OpenAI Coordinated Vulnerability Disclosure) are cited by name; specific wordings and version-specific claims are marked `<!-- needs-research: ... -->` unless verified from primary source.
- ✅ Handoffs at the end of Artefact A name the downstream artefacts this workflow enables: mod-109 safety-case evidence provenance (per chapter 06's *"To mod-109"* section), mod-112 coordinated-disclosure workflow (per chapter 06's *"To mod-112"* section), and the `ai-infra-security` peer role for the concrete IAM implementation.
- ✅ Artefact B's `separation_of_duties` rule is mirrored in Artefact D's reviewer list — the payload's producer is not on the panel.
- ✅ The workflow structurally prevents every anti-pattern in chapter 06's lines 126–129 (corpus in source repo gated by `.gitignore`; corpus in private repo accessible to all engineers; payload in issue title; no legal-review gate). Each is named in Artefact A or Artefact C with the specific workflow step that prevents it.
- ✅ Every artefact carries a `program_scope` header so the CMC ties back to a specific deployment per chapter 01.

## Stretch goals

- **Draft the peer-role handoff brief to `ai-infra-security`.** A one-page brief that translates Artefact A's `store_mechanism`, `access_control`, `audit_log_shape`, and `retention_and_deletion` fields into the concrete asks for the peer role (bucket / KMS-key / IAM-role / lifecycle-rule / audit-sink shapes). Do not write the IAM policy; write the ask.
- **Author the periodic-access-review checklist as a first-class artefact.** A checklist a reviewer walks quarterly against Artefact B: every role's current membership, the exit-review log since the last review, any break-glass invocations since the last review, any denied-by-default violations from the audit log. The checklist is what makes the periodic-review cadence enforceable.
- **Draft the CMC section-7 (consumer contract) handoff for the mod-112 disclosure workflow.** The specific fields a mod-112 coordinated-disclosure package pulls from this exercise's workflow — payload-hash reference shape, provenance summary shape, aggregated-category ASR shape — with the signed-receipt pattern chapter 06's *"consumer contract"* section describes.
- **Add a chapter-05 (mod-104) composition-tag section to the redaction policy.** Composed payloads (Crescendo × low-resource, persona × cipher) carry composition tags in their provenance; the redaction policy's reference shape carries the composition tag alongside the hash so a downstream mod-108 guardrail workstream can filter by composition without ever reading the payload.
- **Draft the *deletion-under-disclosure-window-close* runbook.** Chapter 06 names the WORM / lifecycle rules the peer role enforces; the runbook is the human-side procedure — sign-offs required, provenance-record final state, the specific audit-log entries the deletion emits. Do not include any real payload identifiers.

## Deliverable location

Personal notes or private repo. Do **not** commit the deliverable into this course repo. The workflow itself, once authored, is a candidate for your org's internal-only red-team documentation repository — but the *exercise deliverable* stays private for the learning cycle so the reviewer of your exercise cannot see any deployment specifics you filled in. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries a reference workflow with the same artefact shapes and the same anti-payload-content discipline. Working payloads, filled briefs, and real credentials live nowhere in either repo per chapter 06.
