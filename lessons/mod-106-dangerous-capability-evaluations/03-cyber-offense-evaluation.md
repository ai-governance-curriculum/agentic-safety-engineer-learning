# 03 — Cyber-Offense Capability Evaluation Engineering

## Motivation

Cyber-offense is the second capability panel every frontier safety framework treats as tier-defining. RSP's cyber CCLs, the Preparedness Framework's cybersecurity category, and the FSF's cyber Critical Capability Levels each ask: **at what level of autonomy can this model conduct offensive-security operations, and by how much does it uplift a would-be attacker over the unassisted baseline?** The answer factors into three axes the DCER reports separately: **capability** (can the model do the operation at all), **autonomy** (with how little human supervision), and **defensive delta** (how much the same model uplifts the defender — because the same model available to attackers is also available to defenders, and the tier decision depends on whether the offensive uplift outpaces the defensive uplift).

This chapter develops the cyber-offense panel. Unlike CBRN, cyber-offense has mature, publicly-available benchmarks — **CyberSecEval 2** and **CyberSecEval 3** from Meta, capture-the-flag suites, exploit-writing task sets, and MITRE ATT&CK-tagged autonomy tasks. Public benchmarks are a mixed blessing: they give the panel numeric grounding but they also drift as models are trained on the benchmarks. The elicitation protocol has to acknowledge both.

## Primary sources for this chapter

- **Bhatt, Manish et al., "CyberSecEval 2: A Wide-Ranging Cybersecurity Evaluation Suite for Large Language Models," Meta AI, 2024.** Extends CyberSecEval 1 with autonomous cyber-offense subsets, prompt-injection risk tests, and vulnerability-exploitation evaluations. <!-- needs-research: confirm the current CyberSecEval 2 release version, subset list, and paper venue. -->
- **CyberSecEval 3 technical report** — autonomous cyber-attack tasks, spear-phishing generation, exploit generation across CVE families, defensive uplift measurements. <!-- needs-research: confirm the current CyberSecEval 3 title, release URL, and subset coverage. -->
- **Meta's CTF (Capture-the-Flag) evaluation suites** and other public LLM-CTF task collections (LLM-CTF benchmark, InterCode-CTF, Cybench, EvilPass, and adjacent releases). <!-- needs-research: confirm current names, versions, and task counts. -->
- **MITRE ATT&CK** framework — tactic and technique taxonomy for annotating autonomy tasks. Version-pin to the release you evaluate against; ATT&CK evolves. <!-- needs-research: confirm the current ATT&CK version. -->
- **MITRE ATLAS** — the ML-adjacent adversarial-tactics catalog; relevant where the cyber-offense task involves ML-specific offensive operations. <!-- needs-research: confirm ATLAS version. -->
- **Anthropic RSP, OpenAI Preparedness Framework, DeepMind FSF cyber CCL sections** — the tier definitions and the paired evaluation-methodology write-ups. mod-101 chapters 02–04 remain the on-ramp; re-read specifically the cyber sections before the elicitation is authored.
- **UK AISI + US AISI (NIST AISIC)** pre-deployment cyber-evaluation reports and the Inspect harness's cyber task modules. <!-- needs-research: confirm the AISI Inspect cyber-eval releases. -->
- **DARPA AI Cyber Challenge (AIxCC)** publications — the AIxCC competition ran ML-driven vulnerability discovery and patching, and the published results inform the state of the art for autonomous cyber-offense against real codebases. <!-- needs-research: confirm AIxCC final-report publication status. -->

## Anatomy of a cyber-offense capability panel

Every cyber-offense panel this module authors has five parts. The first four are the same as CBRN with domain substitution; the fifth is unique to cyber and load-bearing.

1. **The threat model.** Which offensive-security scenario, against what asset class, at what actor tier. "A moderately-skilled independent operator seeking to exploit a memory-safety vulnerability in a widely-deployed open-source dependency to establish persistence on internet-facing servers" is a threat model. "Bad hackers doing bad hacking" is not.
2. **The elicitation protocol.** All four axes from chapter 01. Tool-use elicitation is the load-bearing axis — a cyber-offense agent without shell access, network access, and a code interpreter is not the deployed threat model; the ceiling has to reflect the tooled agent.
3. **The measurement composition.** CyberSecEval 2 + 3 subsets *plus* CTF-suite success under an autonomy axis *plus* MITRE ATT&CK-tagged autonomy tasks *plus* exploit-writing tasks over CVE families.
4. **The security controls.** Elicited exploits, working payloads, and validated attack chains do not leave the safety-team sandbox. Novel vulnerabilities discovered during elicitation route to the mod-112 disclosure workflow.
5. **The defensive delta.** For every offensive-capability row, the paired defensive-capability row: how much the same model uplifts *defenders* against the same task class. The tier decision compares offensive ceiling minus defensive ceiling — an offensive uplift is less concerning if the defensive uplift is larger, and vice versa. The frontier safety frameworks each acknowledge this delta; the DCER makes it explicit.

## The three axes the cyber-offense panel reports

### Axis 1 — Capability

Can the model, under best-effort elicitation, produce the offensive artefact at all? This is measured on:

- **CyberSecEval 2 vulnerability-exploitation subsets.** Categories include memory-safety exploitation (buffer overflow, use-after-free), web exploitation (SQLi, XSS, SSRF), and command-injection. <!-- needs-research: confirm the current CyberSecEval 2 subset structure. --> Pass@N is measured per subset; the base-model pass rate is often close to zero on real-world-difficulty subsets and rises sharply with fine-tune + scaffold elicitation.
- **CyberSecEval 3 exploit-generation subsets** and the autonomous-cyber-attack subsets. <!-- needs-research: confirm CyberSecEval 3 subset structure. -->
- **CTF task suites** at partial autonomy (agent solves an isolated challenge with a shell and network access). Cybench, InterCode-CTF, and similar suites publish task lists and reference solutions.
- **Exploit-writing tasks against curated CVE families** — the elicitation team defines a CVE-family task set (e.g., a subset of Linux-kernel privilege-escalation CVEs from the last two years) and measures the model's ability to reproduce the exploit given the disclosed vulnerability description. Reproducing a public exploit is a lower-bound capability; drafting a novel exploit against a fresh vulnerability description is a stronger signal.

### Axis 2 — Autonomy

At what level of human supervision does the capability hold? Autonomy tiers, adapted from the shared frontier-safety-framework language:

- **Tier A0 — Assistant.** The model produces individual artefacts on request; the human integrates them into an attack. Baseline autonomy.
- **Tier A1 — Assisted-execution.** The model, given a task description, produces a plan the human executes step-by-step.
- **Tier A2 — Autonomous-substep.** The model, in an agent scaffold with tools, autonomously completes a bounded sub-step of an attack (e.g., discover the vulnerability, write the exploit, validate against a test environment) with human review between sub-steps.
- **Tier A3 — Autonomous-end-to-end.** The model autonomously completes an end-to-end offensive operation against a live-fire test environment: reconnaissance → vulnerability discovery → exploitation → persistence → objective, with human only in a monitoring / termination role.

Every task in the panel is annotated with the autonomy tier at which the model achieved the task; the DCER reports capability curves separately per autonomy tier. Anthropic, OpenAI, and DeepMind each publish autonomy-tier-annotated results in their cyber system-card sections; the exact tier vocabulary drifts across labs, but the shape is consistent. <!-- needs-research: confirm current lab-specific cyber-autonomy tier language. -->

The MITRE ATT&CK annotation attaches to each task: the task's implicated **tactic** (initial-access, execution, persistence, privilege-escalation, defense-evasion, credential-access, discovery, lateral-movement, collection, command-and-control, exfiltration, impact) and **technique** IDs are tagged so the DCER's cyber panel can report autonomy-tier × ATT&CK-tactic coverage cells.

### Axis 3 — Defensive delta

For every offensive-capability row, the panel reports the paired defensive-capability row:

- **Vulnerability discovery for defensive triage.** Same model, same tool set, but tasked with reviewing a codebase for the same vulnerability class. Measures defensive uplift over an unassisted-defender baseline.
- **Patch drafting.** Given a vulnerability description, the model drafts a patch. Measures whether the model closes vulnerabilities faster than it opens them.
- **Detection-rule generation.** Given an attack pattern, the model drafts detection rules (Sigma, YARA, Snort, Suricata) for the pattern. Measures defensive-observability uplift.
- **Incident-response artefact generation.** Given a compromise trace, the model drafts an incident summary, containment steps, and forensic queries.

The delta — offensive ceiling minus defensive ceiling, per task family — is the number the safety-review body treats as the *net* uplift. A positive delta indicates net offensive uplift; a negative or near-zero delta indicates the model is at least as helpful to defenders as to attackers. The DARPA AIxCC results provide a public-domain anchor for what the defensive side of this looks like at the state of the art.

## Elicitation-axis notes for cyber-offense

- **Best-of-N** is essential. CTF success on hard tasks often has pass@1 near zero and pass@64 substantially higher. The N budget is generally larger for cyber than for CBRN because CI derivation on rare-event pass rates requires it.
- **Fine-tune elicitation** on CyberSecEval and CTF suites has been reported to produce large uplifts. The fine-tune weights stay behind the sandbox boundary per chapter 01's harmful-payload discipline.
- **Tool-use elicitation** is the load-bearing axis. The tool set typically includes: shell (bash), Python interpreter with common security libraries (pwntools, ropper, angr), a browser, a network scanner or wrapper (nmap), a disassembler / decompiler wrapper (radare2, Ghidra scripting), and a search tool over public exploit databases. The sandbox posture bounds network egress to the test environment.
- **Scaffolding elicitation** — plan-execute-critique loops, Reflexion-style self-critique on failed exploits, sub-agent decomposition (recon-agent, exploit-agent, post-exploit-agent). METR-shaped autonomy scaffolds are relevant here.

## Threat models that anchor the cyber-offense panel

A cyber-offense panel that measures "any offensive-security capability" without threat-model anchoring produces a large but decision-unlinked measurement set. The framing to bring the panel to decision-relevant coverage: for each RSP / Preparedness / FSF cyber tier, name the threat model the tier is defined against, then instantiate the measurement composition for that threat model. Common shapes:

- **Novice-uplift attacker.** A minimally-skilled operator seeking to compromise commodity infrastructure. The uplift question is whether the model raises the operator from *cannot execute the CVE-2XXX exploit* to *can execute the exploit against a live target*.
- **Skilled-independent attacker.** A moderately-skilled independent operator seeking specific-target compromise. The uplift question is whether the model materially shortens the operator's time-to-compromise on a target with realistic defensive posture.
- **Advanced-persistent-threat-class attacker.** A resourced actor with existing offensive capability seeking marginal uplift. The uplift question is whether the model closes a specific gap in the actor's capability chain (zero-day discovery, evasion-rule generation, defence-informed exploit tuning).

Each threat model implies a different measurement composition. Novice-uplift maps onto CyberSecEval 2/3 exploit-generation on well-known CVEs. Skilled-independent maps onto CTF autonomy A2. Advanced-persistent-threat maps onto novel-exploit-generation and autonomous-end-to-end tasks against realistic environments. The DCER reports each threat model's ceiling separately; the tier decision references the tier-defining threat model.

## Security controls for the cyber-offense panel

Escalated from the general harmful-payload discipline in chapter 01:

- **Elicited exploits stay in the sandbox.** Working exploit chains — including validated exploits against real CVEs, novel proofs-of-concept, and CTF flag captures against production-similar environments — do not leave the safety-team sandbox. The DCER reports the *fact of the exploit's success* and the *task ID*, not the exploit chain.
- **Novel vulnerabilities disclosed through mod-112.** If elicitation discovers a novel real-world vulnerability against production software, the mod-112 coordinated-disclosure workflow governs the disclosure — CVE numbering, vendor notification, embargo scheduling, downstream release. This is not an ad-hoc process.
- **Live-fire test environments are isolated.** Any autonomous-end-to-end (A3) evaluation runs against a controlled test environment (a lab range) with no path to production infrastructure. Egress is default-deny.
- **Malware, C2 frameworks, and post-exploitation tooling are audit-logged.** The elicitation tool set includes offensive tools (pwntools, msf-like frameworks); their use is logged and audit-reviewed.
- **Fine-tune weights stay behind the sandbox boundary** per chapter 01.
- **Frontier-provider authorisation** required for production-API elicitation, same as chapter 02.

## Threading the measurement composition — an illustrative panel skeleton

Defanged shape, not a working panel:

```yaml
cyber_offense_panel:
  threat_model:
    tier_read_against: fsf_cyber_ccl_X   # or rsp / preparedness equivalent
    actor_archetype: skilled_independent
    target_class: internet-facing commodity server, realistic defensive posture
    horizon: 7_days
  measurement_composition:
    cyberseceval_2:
      version: <pin>
      subsets:
        - vulnerability_exploitation.memory_safety
        - vulnerability_exploitation.web
        - autonomous_cyber_offense
      pass_at_n:
        n: 64
        temperature: 0.7
      elicitation_axes_swept: [best_of_n, fine_tune, tool_use, scaffolding]
    cyberseceval_3:
      version: <pin>
      subsets:
        - autonomous_cyber_attack
        - spear_phishing
        - exploit_generation
      pass_at_n: {n: 64, temperature: 0.7}
    ctf_suite:
      suite_id: cybench_or_intercode_ctf     # pin the suite
      version: <pin>
      autonomy_tier_reported: [A1, A2]
      per_tier_pass_rate: <fraction>
    mitre_attack_tagged_tasks:
      task_set_id: <handle>
      tactic_coverage:
        - initial_access
        - execution
        - persistence
        - privilege_escalation
        - defense_evasion
        - credential_access
        - discovery
        - lateral_movement
        - exfiltration
      per_technique_pass_rate: <table>
    exploit_writing_tasks:
      cve_family_set_id: <handle>
      task_kind: reproduce_public_exploit | draft_novel_exploit
      per_family_pass_rate: <table>
  defensive_delta:
    vulnerability_discovery_for_defence:
      pass_rate: <fraction>
    patch_drafting:
      pass_rate: <fraction>
    detection_rule_generation:
      pass_rate: <fraction>
    net_delta_by_task_family: <table: offensive − defensive per family>
  security_controls:
    exploit_disposition: sandbox_retained; not distributed
    live_fire_environment_id: <lab-range handle>
    novel_vuln_disclosure_workflow_ref: <mod-112 workflow ID>
    frontier_provider_authorisation_ref: <ticket>
  threshold:
    framework: <rsp | preparedness | fsf>
    tier: <ccl or equivalent>
    metric: composite (subset pass@N, autonomy tier reached, net delta)
    threshold_direction: cleared_below
  elicitation_gap_accounting:
    statistical: <cite peer role>
    scaffold_parity_vs_public: <claim + comparison table>
    tool_set_completeness: <claim>
    fine_tune_depth: <claim>
    human_effort_hours: <n>
```

## Common misreadings to avoid

- **"We passed CyberSecEval 2, so the cyber tier is cleared."** No. CyberSecEval 2 is one measurement anchor for one autonomy tier. The tier decision requires autonomy A2 / A3 measurement, ATT&CK-coverage measurement, and the defensive delta.
- **"Public benchmark scores are the ceiling."** Public benchmarks drift; models can be trained on them. Elicitation-gap accounting has to include *benchmark contamination* as a component — held-out or novel task sets are the check.
- **"Any exploit-writing capability is a fail."** No. Exploit-writing is a spectrum from reproducing a public CVE exploit (low bar; most competent developers can do this) to drafting a novel zero-day (very high bar). The tier definition names the level; the panel measures against it.
- **"The defensive delta is a nice-to-have."** No. Every published frontier safety framework acknowledges the delta because the tier decision depends on net uplift, not raw offensive capability.
- **"CTF success is the same as real-world offensive capability."** No. CTFs are curated puzzles with known solutions; real-world offensive operations require target-selection, defence-informed persistence, and ops-security discipline that CTFs do not test. The panel notes the gap and reports the CTF result as a lower-bound proxy for the corresponding real-world capability.
- **"The Cyber-A3 result is what the deployment sees."** Only if the deployment ships an autonomous end-to-end agent scaffold with equivalent tools. Most deployments do not; the deployed ceiling is lower. The DCER reports both.

## Boundary — this module does not do

- **Application-security engineering.** SAST / DAST, CI/CD security controls, secure-development-lifecycle work — the `ai-infra-security` peer role, `security-engineer` roles, and application-security teams own that engineering. This module *measures the model's capability against those defences*; it does not build the defences.
- **Offensive-security engagements.** Red-team engagements against real production infrastructure are the offensive-security team's territory. This module runs elicitation against controlled test environments only.
- **Novel exploit-development research at the offensive-security-researcher depth.** Weaponisation, novel evasion techniques against production defences — this module measures capability at working depth for tier decisions; it does not compete on the offensive-security-researcher ladder.
- **SOC / DFIR.** When elicitation surfaces evidence of a real intrusion (unlikely but possible if the elicitation environment is misconfigured), SecOps runs the incident-handling playbook.

## Summary

- Cyber-offense evaluation reports on three axes: **capability, autonomy, and defensive delta**.
- **CyberSecEval 2 + 3, CTF suites, MITRE ATT&CK-tagged autonomy tasks, and exploit-writing tasks over CVE families** compose the measurement.
- Every task is annotated with an **autonomy tier (A0–A3)**; the DCER reports capability curves per tier.
- The **defensive delta** — offensive ceiling minus defensive ceiling per task family — is the tier-decision-relevant net-uplift number.
- Elicited exploits, novel vulnerability discoveries, and fine-tune weights stay behind the safety-team sandbox boundary; **mod-112 governs any external disclosure**.
- The panel's threat-model anchoring — novice, skilled-independent, APT-class — determines which measurement rows are decision-relevant for a given tier.
- Public-benchmark drift and contamination are components of the elicitation-gap accounting; held-out or novel task sets are the check.
