# exercise-03: CyberSecEval Cyber-Offense Run

**Estimated effort:** 3 hours

## Objective

Run a **CyberSecEval-anchored cyber-offense capability panel** — capability × autonomy × defensive-delta — against one target model, and author the DCER-shaped panel section that the tier-decision-relevant reviewer would consume. The output is one runnable subset (CyberSecEval 2 or 3) plus a CTF-suite sample under an autonomy-tier axis, plus the paired defensive-delta rows, plus the elicitation-gap accounting and verdict against a pre-registered cyber tier.

This exercise anchors chapter 03 in practice. It is deliberately narrower than a production cyber panel — you are running one or two subsets, not the full suite — but the three-axis reporting shape (capability, autonomy, defensive delta) is exercised end-to-end.

## Prerequisites

- Read chapter 03 (cyber-offense evaluation) end-to-end.
- Read the Meta CyberSecEval 2 paper (Bhatt et al., 2024) — abstract, methodology, and per-subset descriptions. <!-- needs-research: confirm current CyberSecEval 2 release, subset list, and paper URL. -->
- Skim the CyberSecEval 3 technical report for the autonomous-cyber-attack, exploit-generation, and spear-phishing subsets. <!-- needs-research: confirm current CyberSecEval 3 release URL. -->
- Skim at least one public LLM-CTF task set (Cybench, InterCode-CTF, or an equivalent). <!-- needs-research: confirm current CTF-suite releases. -->
- Read the MITRE ATT&CK tactic list (initial-access through impact) so the autonomy-tier × tactic annotation is straightforward. <!-- needs-research: confirm current ATT&CK version. -->
- Skim one recent frontier-model system card's cyber section (Claude / o-series / Gemini) to see how the capability × autonomy × delta reporting looks in production.
- Have a target model accessible: an open-weights model is the recommended default (avoids frontier-provider authorisation for offensive-tool elicitation). If you use a frontier API, obtain the provider's written authorisation first.
- Have the chapter-01-style pre-registration document authored (extend exercise-01 or author from scratch).

## Requirements

The exercise has four parts. All four are required.

### Part 1 — Capability measurement (CyberSecEval subset)

1. **Pin one CyberSecEval subset.** Choose one of CyberSecEval 2's vulnerability-exploitation subsets (memory-safety, web, or command-injection) OR one of CyberSecEval 3's autonomous-cyber-attack / exploit-generation subsets. Document the version tag, subset ID, and case count.
2. **Sweep at least two elicitation axes** from chapter 01:
   - Best-of-N: pass@N at temperature 0.7 for a specified N (16 or 32 recommended for cyber subsets given the low base rate).
   - One of: tool-use elicitation (shell + Python + minimal offensive-tool wrappers), scaffolding elicitation (plan-execute-critique loop), or prompt-variation elicitation.
3. **Report per-configuration pass rate** with a CI (Wilson or peer-role-approved methodology).
4. **Report the composite elicitation ceiling** for the subset and the elicitation-shift over pass@1.

### Part 2 — Autonomy tier measurement (CTF sample)

1. **Sample 3–5 CTF challenges** from a public suite (Cybench, InterCode-CTF, or the CyberSecEval 3 autonomous subset). Difficulty distribution should include at least one that a competent-human CTF player would solve in under an hour and one that would take longer.
2. **Annotate each challenge with its target autonomy tier** — A0 (assistant), A1 (assisted-execution), A2 (autonomous-substep), or A3 (autonomous-end-to-end). Chapter 03 defines the tiers.
3. **Run the target model on each challenge under an agent scaffold** — pin the scaffold (SWE-agent-style, a bespoke ReAct scaffold, or a published cyber scaffold). Provide the scaffold's tool set: shell, Python, network scanner wrapper (nmap or equivalent), a search tool, and an exploit-database lookup if appropriate.
4. **Report per-challenge outcome per autonomy tier** — did the agent solve it, and at which tier? Aggregate to per-tier pass rate.
5. **Annotate each challenge with its implicated MITRE ATT&CK tactic and technique IDs**. Report autonomy-tier × ATT&CK-tactic coverage cells.

### Part 3 — Defensive-delta measurement (paired)

For the same subset (Part 1) and CTF sample (Part 2), run the paired *defensive* task variants:

1. **Vulnerability discovery for defence.** For the CyberSecEval subset's target vulnerability class, present the model with a codebase (or code snippet) and ask it to identify the vulnerability. Report detection rate.
2. **Patch drafting.** For a subset of items, present the vulnerability description and ask the model to draft a patch. Report pass rate under a paired automated grader.
3. **Detection-rule generation.** For at least one CTF challenge's implicated technique, ask the model to draft a Sigma / YARA / Snort / Suricata detection rule. Report qualitative rubric-scored quality.
4. **Compute the per-task-family net delta** — offensive pass rate minus defensive pass rate — and discuss the sign and magnitude.

### Part 4 — DCER cyber-panel section draft

Author the DCER Section 4 subsection for the cyber-offense panel (chapter 06 shape). Include:

- Threat-model recap (novice / skilled-independent / APT-class — pick and defend).
- Measurement composition run (Parts 1–3 above with version pins).
- Elicitation-axis results with CIs.
- Autonomy tier × ATT&CK tactic coverage table.
- Per-task-family defensive-delta table.
- Elicitation-gap accounting (statistical + capability-specific — scaffold parity, tool-set completeness, contamination check).
- Threshold comparison against a pre-registered cyber tier (RSP CCL, Preparedness High, or FSF cyber CCL — pick and cite the framework document version).
- Verdict — cleared / approached / crossed — with the rollback trigger named if approached or crossed.

## Deliverables

Commit to your exercise-solution area (the paired `agentic-safety-engineer-solutions` repo, per the module's discipline):

- `cyberseceval-run/` — runnable code, per-run configuration files, per-run score outputs. No working exploits or elicited attack chains committed; scores and rubric verdicts only.
- `ctf-run/` — CTF challenge run configurations, per-challenge outcome logs (defanged — flag captures referenced by challenge ID, not by flag value), autonomy-tier annotations, ATT&CK tactic tags.
- `defensive-run/` — vulnerability-detection, patch-drafting, and detection-rule-generation runs and grading.
- `pre-registration.yaml` — the elicitation-protocol pre-registration.
- `dcer-cyber-panel.md` — the Part 4 DCER-shaped panel section.
- `security-controls.md` — sandbox handle, live-fire environment isolation, retention window, novel-vuln-disclosure workflow reference.
- `README.md` — target model, CyberSecEval version, CTF suite, and a one-paragraph verdict summary.

## Acceptance criteria

- **CyberSecEval subset pass rate reported for at least two elicitation configurations** with CIs.
- **At least three CTF challenges** run and annotated with autonomy tier and ATT&CK tactic.
- **Paired defensive-delta rows** — vulnerability discovery, patch drafting, and one detection-rule sample — reported with a per-task-family net-delta table.
- **Autonomy-tier × ATT&CK-tactic coverage table** present in the DCER draft.
- **Elicitation-gap accounting** covers scaffold parity, tool-set completeness, and benchmark-contamination check.
- **Threshold comparison and verdict** against a specific framework-tier cite (framework document version pinned).
- **No working exploits, CTF flag values, or elicited attack chains committed to the repo.** References by ID; payloads live in the sandbox.
- **The isolation environment for any CTF or autonomous run is default-deny egress** and documented.

## Stretch goals

- **CyberSecEval 3 autonomous-cyber-attack subset.** Run the CyberSecEval 3 autonomous-attack subset (rather than a CyberSecEval 2 static subset) and report the autonomy-tier reached per task, plus the shape shift relative to any prior model release you have access to.
- **Fine-tune elicitation.** On an open-weights target, fine-tune on 20–100 cyber-focused examples (public write-ups, disclosed exploit walkthroughs — nothing novel) and re-run the CyberSecEval subset. Report the fine-tune-elicitation shift and note the weight-disposition discipline.
- **DARPA AIxCC comparison.** Compare your defensive-delta results against DARPA AIxCC published results for the closest task family. Note where your delta is above or below the AIxCC-anchored baseline. <!-- needs-research: confirm current AIxCC results are published in a citable form. -->
- **Contamination probe.** Construct a small held-out task set (e.g., novel wrappers around known-CVE structures with modified variable names and shifted line numbers) and re-run one CyberSecEval subset. Report the contamination-adjusted pass rate and discuss the elicitation-gap implication.
- **Cross-model comparison.** Run the same protocol against two model sizes / releases and produce a scaling table. Discuss the capability, autonomy, and defensive-delta shifts as a function of model capability.

## Guardrails

- **No working exploits, CTF flag values, novel vulnerability disclosures, or elicited attack chains committed to any repo.** References by ID only; payloads live in the sandbox. The chapter 03 harmful-payload discipline applies.
- **Live-fire runs are isolated with default-deny egress.** The CTF environment does not have any path to production infrastructure. Personal AWS accounts, home network, or laptop wifi are not isolation.
- **If your elicitation surfaces a novel real-world vulnerability against production software**, halt further elicitation on that target, do not disclose publicly, and route the finding through the mod-112 coordinated-disclosure workflow (CVE numbering, vendor notification, embargo). The chapter 03 discipline is explicit on this.
- **If your target is a frontier-provider API**, obtain the provider's written authorisation before running offensive-tool elicitation. Provider safety teams publish channels.
- **Fine-tune weights (if any)** stay behind the safety-team sandbox boundary. Do not share.
- **Legal / policy counsel gate.** If your elicitation touches export-controlled or jurisdictionally-sensitive offensive-security material, route through in-house counsel before external engagement.
- Do not run the exercise against any infrastructure you do not own or have explicit written authorisation to test.
