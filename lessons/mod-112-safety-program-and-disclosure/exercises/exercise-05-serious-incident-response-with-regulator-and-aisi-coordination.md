# exercise-05 — Serious-incident response with regulator and AISI coordination

**Estimated effort:** 3 hours
**Prerequisite chapters:** 05 primary; 04 (Article 73 disclosure the drill triggers); 01 (FSPC section-6 incident-response contract). Interfaces to mod-107 (kill switch, EACC), mod-108 (monitors as detection surface), mod-110 (deception-class detection).

## Objective

Run a full serious-incident-response **tabletop drill** end-to-end for **one** of chapter 05's five incident classes, from T+0 detection through post-mortem, exercising the full coordination roster and issuing handoffs into exercises 04, 06, 03, and 02. The drill produces the incident record, timeline log, coordination log, handoff pack, and drill runbook that a level-40 safety-engineer would carry into a real incident on a statutory clock. No real containment tools fire; no real regulator submissions ship. The artefacts are what land.

## Problem statement

Chapter 05's load-bearing claim is that serious-incident response is the **composition point** for the entire safety-engineering craft — the FSPC (chapter 01), the tier-decision memo (chapter 02), the system card (chapter 03), the Article 73 templates (chapter 04), and the mod-104 / mod-105 / mod-107 / mod-108 / mod-110 downstream surfaces all collapse into a single operating cadence under a statutory clock. Teams that have not drilled the composition discover, mid-incident, that the Article 73 initial-notification template was never pre-authored, that the AISI coordination lead has no runbook, that the RSO's decision authority for tier-memo revision is ambiguous, and that the regression fixture is never back-fed. The statutory clock runs out during internal negotiation. This is the failure mode the drill exists to prevent.

Pick **one** incident class from chapter 05's five: **autonomy exceedance** (class 1), **dangerous-capability disclosure** (class 2), **jailbroken agent tool-abuse** (class 3), **deception detection** (class 4), or **safety-eval regression** (class 5). Author the drill for that class end-to-end. Run every stage of chapter 05's triage timeline — T+0 detection, T+30 initial containment, T+2h class determination and stakeholder notification, T+6-24h Article 73 initial notification, T+24-72h root-cause analysis, T+72h+ full-report / full remediation, post-mortem — with a **named owner**, a **named artefact**, and a **named handoff** at every stage. Exercise the full coordination roster (safety-engineering incident commander, regulatory legal, litigation legal, external affairs, communications, AISI coordination lead, FMF liaison, RSO / Preparedness Committee / Safety Board equivalent). Issue explicit handoffs into exercise 04 (Article 73 template fill), exercise 06 (regression-fixture back-feed), exercise 03 (system-card addendum), and exercise 02 (tier-memo revision where the incident revises the tier).

The drill is a **tabletop**. No real containment fires; no real submissions ship. The artefacts are the incident record, the coordination log, the timeline log, and the handoff pack. The pre-authored Article 73 template (exercise 04's Artefact A) is invoked; the fill-latency the drill achieves is recorded. Payload discipline (chapter 04's shape / payload separation) is not optional — where the incident's evidence would contain a dangerous-capability payload in reality, the drill's log carries a pointer-and-hash placeholder, not the payload.

## Requirements

Produce five artefacts.

### Artefact A — `incident-<id>.md`

The incident record. Named per a stable, scenario-anchored identifier (`incident-2026-tabletop-class3-agent-tool-abuse.md` or similar). Structure:

- **Header.** `incident_id`, `class` (per chapter 05's taxonomy — 1, 2, 3, 4, or 5), `severity` (per the FSPC's severity scale — reference chapter 01's routing), `drill_kind: tabletop`, `authoring_signer`, `date_of_drill`.
- **Detection signal.** Which surface fired — mod-108 monitor pattern, mod-107 wrapper outcome-verification return, customer report, red-team finding (mod-111), scheduled re-evaluation regression (mod-106 / mod-110). Cite the specific chapter-05 detection-signal shape for the chosen class.
- **Initial-containment decision.** Whether the mod-107 kill switch fires on the affected surface, whether a partial capability-gate tightening is sufficient, or whether the response proceeds under monitored observation. Chapter 05's *"over-containment is fine, and preferable to under-containment"* is the frame. Record the decision, the alternatives considered, and the rationale.
- **Class-determination reasoning.** Why the class is the class it is — the distinguishing signals per chapter 05's per-class definitions. For class 3 specifically, record why the failure is **jailbreak-driven** and not **containment-flaw-driven** (chapter 05's key distinction from class 1).
- **Root-cause analysis.** What the T+24-72h RCA pinned. Evidence sources — mod-107 audit logs, mod-108 monitor logs, mod-106 re-elicitation runs where applicable. The RCA is where the safety-engineer's technical narrative lives.
- **Mitigation actions.** What was done — mod-107 EACC update, capability-gate reconfiguration, rollback executed or not (and why), monitor strengthening (mod-108), safety-case (mod-109) revision needed.
- **Back-feed pointer.** Which suite the regression fixture is back-fed into per chapter 05's per-class back-feed mapping (class 1 → mod-105 / mod-107; class 2 → mod-104 / mod-106; class 3 → mod-104 / mod-105; class 4 → mod-110; class 5 → the regressing suite). Pointer into exercise 06's discipline.
- **Disclosure trigger determination.** Whether Article 73 clears the threshold, whether AISI coordination fires, whether FMF sharing fires. Chapter 05's *"pre-authored templates presuppose triggering; the internal review determines whether the trigger holds"* is the frame.

Payload discipline: no dangerous-capability payload in the RCA or the mitigation-actions section. Where the evidence would carry payload, the record cites a pointer-and-hash placeholder per chapter 04's shape / payload separation.

### Artefact B — `incident-<id>-timeline.yaml`

The per-stage timeline log. One YAML document. Every stage of chapter 05's triage timeline is a row.

Per stage:

- `stage` — one of `t0_detection`, `t30_initial_containment`, `t2h_class_determination`, `t6_24h_article73_initial_notification`, `t24_72h_root_cause_analysis`, `t72h_plus_full_remediation`, `post_mortem`.
- `t_plus` — the relative timestamp (chapter 05's cadence is the default; deviations are noted with reasons).
- `owner` — the specific named role from the coordination roster who owns the stage. The safety-engineering incident commander owns most stages; specific stages route to legal, external affairs, communications, or the RSO / Safety Board.
- `artefact_produced` — the artefact the stage lands. `incident_record_opened`, `containment_decision_recorded`, `class_determination_memo`, `article73_initial_notification_filed` (drill: template-filled), `rca_report`, `remediation_bundle`, `post_mortem_report`.
- `handoff_to_next_stage` — what the next-stage owner receives, and via what channel (FSPC's incident-response tracker is the default).
- `notes` — deviations, blockers, tabletop-specific caveats (e.g. *"in a real incident this stage triggers a real submission; drill records template-fill latency only"*).

The timeline covers T+0 through post-mortem per chapter 05's cadence. Every stage has an owner, an artefact, and a handoff; a missing field is a finding.

### Artefact C — `incident-<id>-coordination.yaml`

The coordination log. One YAML document. Every named role in chapter 05's coordination roster is a row, even if the role was not actively engaged (in which case the row is `engaged: false` with the reason).

Per role:

- `role` — `safety_engineering_incident_commander`, `regulatory_legal_counsel`, `litigation_legal_counsel`, `external_affairs`, `communications`, `aisi_coordination_lead`, `fmf_liaison`, `rso_or_safety_board`. Chapter 05's roster naming is the authority.
- `notified_at` — `t_plus` timestamp of first notification.
- `notification_channel` — the FSPC-defined channel (incident-response tracker page, direct escalation, war-room-open notification).
- `artefacts_co_authored_or_reviewed` — enumerated. E.g. regulatory legal co-authors the Article 73 initial notification and the AISI companion; litigation legal reviews the incident record for retention constraints; communications authors the system-card-addendum draft; the RSO / Safety Board authorises the tier-memo revision.
- `decisions_made` — the specific decisions the role was accountable for. Chapter 05's *"each named role has a specific decision authority during the incident and specific artefacts they own"* is the frame — the log records those decisions explicitly.
- `involvement_closed_at` — `t_plus` timestamp when the role's active engagement closed, or `open_at_drill_end` if the tabletop stopped before closure.
- `engaged` — `true | false`; a `false` row explains why the role was not engaged for this class (e.g. FMF liaison not engaged for a class-5 regression that is not cross-industry-relevant).

Silence per role during a real incident damages the working relationship — chapter 05 pins this explicitly for the AISI coordination lead. The log makes engagement (or non-engagement) an explicit, defensible artefact.

### Artefact D — `incident-<id>-handoffs.md`

The handoff pack. This drill stops at *"handoff issued"*; downstream exercises pick up specific artefacts. Enumerate every handoff:

- **Handoff to exercise 04 (Article 73 template fill).** Which template is invoked, what the drill records as the fill-latency achieved against the T+6-24h statutory initial-notification window, and what the exercise-04 discipline picks up from here.
- **Handoff to exercise 06 (regression-fixture back-feed).** Which suite the fixture is back-fed into per chapter 05's per-class mapping, the fixture-specification pointer, the fixture-provenance pointer back into this incident record, and the fixture-sensitivity classification per chapter 04's payload discipline.
- **Handoff to exercise 03 (system-card addendum).** Whether a system-card addendum is required for this class, the disclosure-timing constraint (parallel to or after the regulator submissions per chapter 05), and the addendum's incident-summary / mitigation / tier-determination-status shape.
- **Handoff to exercise 02 (tier-memo revision).** Whether the incident revises the tier determination — chapter 05 pins that class 4 (deception) almost always does, class 1 (autonomy exceedance) frequently does, and classes 2 / 3 / 5 sometimes do. If the tier is revised, the memo revision is owned by the RSO / Preparedness Committee / Safety Board per chapter 02.
- **Handoff to mod-107 (EACC update).** The kill-switch fire status, the EACC configuration change (capability-gate tightening, blast-radius reduction), and the wrapper's audit-log pointer for the RCA.
- **Handoff to mod-108 (monitor strengthening).** The monitor pattern strengthened based on the incident's detection signal — this is how the class is caught earlier next time.
- **Handoff to mod-109 (safety-case revision).** Whether the safety case is invalidated by the incident (chapter 05: *"the safety case is potentially invalidated by the incident. Post-incident, the case is revised or a superseding case is authored"*).
- **Handoff to `ai-infra-security` peer.** The audit-log integrity chain — the signing service, the WORM store, the workload-identity fabric — that anchors the RCA. Chapter 05 pins the peer routing explicitly.

Every handoff names a receiving discipline, the artefact received, and the acceptance criterion the receiving discipline will apply.

### Artefact E — `incident-<id>-runbook.md`

A ~800–1200 word drill runbook covering:

- **Incident-class choice.** Which of chapter 05's five classes you drilled and why (the class-specific response shape you wanted to exercise). Chapter 05's per-class definitions are the frame.
- **Detection-signal choice.** Which of the class's chapter-05-enumerated detection signals fired in the scenario, and why that signal is plausible for the class.
- **Initial-containment rationale.** The T+30-minute decision and its justification. Chapter 05's *"reversible; over-containment is fine, and preferable to under-containment"* is the discipline.
- **Coordination-roster invocation discipline.** How you notified each role, at what `t_plus`, and against which artefact. Why silence-per-role is a working-relationship failure per chapter 05's AISI-specific note.
- **Pre-authored-template usage.** Which Article 73 template was invoked (exercise 04's Artefact A), the fill-latency achieved against the statutory clock, and whether the template's pre-cleared review paths held or degraded under drill conditions. Chapter 05's *"pre-authored templates and pre-cleared review paths are what make co-authorship achievable on the statutory clock"* is the frame.
- **Threats to validity for tabletop-vs-live drills.** Chapter 05's cadence is defensible in prose; a tabletop cannot exercise (a) real API-side latency to the regulator submission channel, (b) real legal review under time pressure with counsel unavailable, (c) real customer-facing communications review under press-cycle pressure, (d) real evidence-collection latency from the mod-107 audit-log system, (e) the psychology of a war room during a live incident. Name each explicitly.
- **Interfaces to the four downstream exercises.** Which handoffs the drill issued into exercise 04, exercise 06, exercise 03, and exercise 02. What the receiving exercise picks up. The drill stops at *"handoff issued"* — the runbook makes that boundary explicit.
- **The FSPC (exercise 01) is where the incident-response contract lives.** Chapter 01's FSPC section-6 pins the per-organisation cadence, the class taxonomy, the coordination roster, and the sign-off routing. The drill exercises the contract; the runbook cites the FSPC as the authority.

## Starter guidance

- **Read chapter 05 with a highlighter on the five classes.** The class-specific response shape is what the drill is teaching. A drill that runs the same generic response regardless of class has silently defeated the exercise. Class 3 (jailbreak-driven tool-abuse) and class 4 (deception detection) have the sharpest distinctions from class 1 (autonomy exceedance); if you drill one of the sharper classes, the response-shape differences will be visible in the artefacts.
- **Pick a plausible-surrogate scenario, not a real incident.** Do not narrate a real product incident. Author a scenario with the plausible detection signal, the plausible affected surface, and the plausible mitigation — enough specificity for the artefacts to be defensible, no specificity that mis-represents a real event. Where a required detail is unverifiable, mark `<!-- needs-research: ... -->`.
- **The FSPC (chapter 01) is the authority for the roster and the cadence.** The FSPC's section-6 is where per-organisation incident-response lives. The drill exercises the contract; the runbook cites it. If your FSPC does not yet enumerate the coordination roster, the drill is where the gap surfaces.
- **The Article 73 template (exercise 04) is the pre-authored artefact.** Do not draft a new template inside the drill; invoke exercise 04's Artefact A and record the fill-latency. If exercise 04 is unfinished, the drill's handoff still points at the template's slot.
- **Every stage has an owner, an artefact, and a handoff.** Chapter 05's *"every stage has a named owner, a named artefact, and a named handoff"* is the discipline. A stage with a missing field is a finding.
- **The coordination log is the roster's proof-of-invocation.** A roster row with no notification, no artefact, and no decision is a role that was not exercised — which is fine for the classes where the role does not apply, but the row must record *why* it did not apply.
- **The back-feed handoff (exercise 06) is not optional.** Chapter 05 pins that skipping the back-feed is one of the most common program-degradation modes. Even in a tabletop, the fixture-specification pointer and the fixture-suite target are recorded.
- **Payload discipline (chapter 04) is not optional.** No dangerous-capability payload text in any committed artefact — not in the incident record, not in the RCA, not in the runbook. Pointer-and-hash placeholders per chapter 04's shape / payload separation.
- **Stretch to a second class if time permits.** Running a second class from a different family (chapter 05's class 3 tool-abuse vs class 4 deception-detection is the sharpest contrast) makes the response-shape differences visible in the artefacts side-by-side. Two shorter drills teach more than one over-long drill.
- **The RSO / Preparedness Committee / Safety Board equivalent authorises tier revision.** Chapter 05 is explicit; a drill where the safety-engineer unilaterally revises the tier has skipped the accountability body. The coordination log records the authorisation, even in a tabletop.
- **Legal review has two rows, not one.** Regulatory legal and litigation legal are distinct roles per chapter 05's roster with different decision authorities. Collapsing them into a single row silently defeats the discipline.
- **The drill's fill-latency against the T+6-24h Article 73 clock is the load-bearing measurement.** Chapter 05 pins the statutory initial-notification window; if the drill's pre-authored-template fill takes longer than the window, the FSPC has a program-degradation finding — and the runbook records it.
- **Do not simulate the regulator.** The drill stops at *"template filled, ready for filing"*. Simulating a regulator response is out of scope; exercise 04's discipline covers submission mechanics.

## Acceptance criteria

- ✅ `incident-<id>.md` records exactly one of chapter 05's five incident classes, with the detection signal, initial-containment decision, class-determination reasoning, root-cause analysis, mitigation actions, back-feed pointer, and disclosure-trigger determination populated per chapter 05's per-class shape.
- ✅ `incident-<id>-timeline.yaml` covers every stage of chapter 05's triage timeline — T+0 detection, T+30 initial containment, T+2h class determination, T+6-24h Article 73 initial notification, T+24-72h RCA, T+72h+ full remediation, post-mortem — with `owner`, `artefact_produced`, and `handoff_to_next_stage` populated per stage. A missing field is a hard fail.
- ✅ `incident-<id>-coordination.yaml` names every role from chapter 05's coordination roster (safety-engineering incident commander, regulatory legal, litigation legal, external affairs, communications, AISI coordination lead, FMF liaison, RSO / Safety Board equivalent). Roles not engaged carry `engaged: false` with a reason; engaged roles carry `notified_at`, `artefacts_co_authored_or_reviewed`, `decisions_made`, and `involvement_closed_at`.
- ✅ `incident-<id>-handoffs.md` issues explicit handoffs into exercise 04 (Article 73 template fill with recorded fill-latency), exercise 06 (regression-fixture back-feed with fixture-specification pointer and per-class suite target), exercise 03 (system-card addendum where applicable), and exercise 02 (tier-memo revision where the incident revises the tier). Handoffs into mod-107, mod-108, mod-109, and the `ai-infra-security` peer are also named.
- ✅ `incident-<id>-runbook.md` (~800–1200 words) covers incident-class choice, detection-signal choice, initial-containment rationale, coordination-roster invocation discipline, pre-authored-template usage with fill-latency, threats to validity for tabletop-vs-live drills, and the interfaces to the four downstream exercises. Cites the FSPC (chapter 01) as the incident-response contract.
- ✅ **Payload discipline (chapter 04) satisfied.** No dangerous-capability payload text appears in any committed artefact — the incident record, the RCA, the runbook, the handoff pack all carry pointer-and-hash placeholders where a real incident's evidence would carry payload.
- ✅ **Tabletop discipline satisfied.** No real containment tool fires; no real regulator submission is filed; no real customer-facing communication is shipped. The drill stops at *"handoff issued"*.
- ✅ **Scenario discipline satisfied.** The incident is a plausible-surrogate scenario, not a narration of a real product incident. Unverifiable details are marked `<!-- needs-research: ... -->`.
- ✅ The RSO / Preparedness Committee / Safety Board equivalent's tier-revision authorisation is recorded in the coordination log where the incident revises the tier. Chapter 05's *"named accountable decision body per the FSPC's sign-off routing"* is enforced.
- ✅ Legal review is recorded as two distinct roles (regulatory, litigation) with distinct decision authorities per chapter 05's roster.
- ✅ Article 73 template-fill latency against the T+6-24h statutory initial-notification window is recorded. If the fill exceeds the window, the runbook names the FSPC program-degradation finding.

## Stretch goals

- **Run a second class from a different family.** Chapter 05's class 3 (jailbroken agent tool-abuse) vs class 4 (deception detection) is the sharpest contrast; running both surfaces the response-shape differences (two-legged containment-plus-back-feed vs highest-severity rollback-plus-tier-revision) side-by-side. Author a second incident record + timeline + coordination log; keep the runbook single and comparative.
- **Author the FMF incident-sharing shape.** Chapter 05 pins that FMF sharing is optional but load-bearing for cross-industry safety. Draft the shape-not-payload notification the FMF liaison would send for the drilled class, and record it as an appendix to the handoff pack. Chapter 04's shape / payload separation is the discipline.
- **Author the post-mortem shape.** The T+72h+ post-mortem is chapter 05's final stage; draft the shape the post-mortem takes for the drilled class (what evidence is presented, what decisions the Safety Board equivalent takes, how the post-mortem appends to the FSPC's incident-response log). One page is enough; the point is that the shape exists before the incident arrives.
- **Stress the drill with a compressed clock.** Re-run the drill with the T+6-24h Article 73 window compressed to T+2h (a defensible worst-case for a high-visibility incident); record which coordination roster rows still fire on time and which degrade. The stress-test output is a program-degradation finding the FSPC's next revision addresses.
- **Author the tier-memo revision handoff into exercise 02.** For classes where the drill revises the tier (class 4 almost always; class 1 frequently), draft the specific revision-request the RSO / Safety Board receives from the drill. Exercise 02's discipline picks up the drafting; the handoff is the interface.

## Deliverable location

Personal notes or private repo. Do **not** commit the incident record, the timeline log, the coordination log, the handoff pack, or the drill runbook into this course repo. The exercise is **scenario-based, not history-based** — any incident record that references a specific real product incident must be scrubbed before storage; use plausible-surrogate framings throughout. Pointer-and-hash payload discipline (chapter 04) applies to every committed drill artefact even in personal notes; the fixture-specification the drill hands off to exercise 06 lives in the payload store per chapter 04 and exercise 06 discipline, not in the drill's own files. The paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo carries reference-shape drill artefacts for the five incident classes; consult after your own drill, not before.
