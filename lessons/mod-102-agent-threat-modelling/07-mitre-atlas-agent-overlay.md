# 07 — MITRE ATLAS Agent-Side Techniques Overlay

## Motivation

**MITRE ATLAS** (Adversarial Threat Landscape for Artificial-Intelligence Systems) is the ATT&CK-shape matrix for adversarial-ML techniques. Reviewers who come from an infosec background — SOC analysts, product-security engineers, external red-team consultants — will read your threat model expecting ATLAS technique IDs on every applicable row. Reviewers who come from an AI-safety background will read your threat model expecting OWASP labels. You are writing for both audiences; ATLAS gives you the infosec-legible spine.

The level-25 prerequisite established general ATLAS fluency — the matrix's tactic categories (reconnaissance, resource development, initial access, ML model access, execution, persistence, privilege escalation, defence evasion, credential access, discovery, collection, ML attack staging, exfiltration, impact) and the technique-ID citation discipline. This chapter's contribution is the **agent-side technique set** and the **surface × ATLAS-technique overlay**.

## Primary source

- **[MITRE ATLAS](https://atlas.mitre.org/)** — the current matrix, technique catalogue, and case-study collection.
- **[MITRE ATLAS Matrix](https://atlas.mitre.org/matrices/ATLAS)** — the tactic × technique grid.
- **[MITRE ATLAS Case Studies](https://atlas.mitre.org/studies/)** — worked adversarial demonstrations tagged to techniques.

<!-- needs-research: ATLAS technique IDs and titles shift between releases. Pin the version of the matrix this chapter is written against and re-verify each cited AML.T#### identifier before publication. -->

## Agent-side techniques the ATMD cites

The technique IDs below are the ones this module treats as *agent-load-bearing*. Each ID must be re-verified against the current ATLAS matrix before you cite it in an ATMD row — MITRE renumbers as the taxonomy evolves. IDs that do not resolve currently should be replaced with the current equivalent and flagged.

<!-- needs-research: Re-verify every AML.T#### ID below against the currently published MITRE ATLAS matrix. Historical IDs and current IDs may differ. Update the table before citing in production ATMD rows. -->

| Technique family | Representative ATLAS technique ID (historical) | Agent-side manifestation |
|---|---|---|
| LLM Prompt Injection (direct + indirect) | AML.T0051 | Every data-input surface channel; core to LLM01 |
| LLM Jailbreak | AML.T0054 | Refusal-erosion attacks (Crescendo, TAP, PAIR, many-shot) |
| LLM Plugin Compromise | AML.T0053 | Tool / plugin / MCP-server compromise; tool-invocation surface |
| LLM Meta-Prompt Extraction / System Prompt Leakage | (varies) | System-prompt extraction; LLM07 correspondent |
| LLM Data Leakage | AML.T0057 | Sensitive-data leakage through tool outputs and completions |
| Publish Poisoned Datasets | AML.T0058 | Upstream corpora poisoning that lands in RAG index / embeddings |
| Erode Dataset Integrity | AML.T0059 | Progressive corruption of a shared corpus |
| LLM Trusted Output Components Manipulation | AML.T0067 | Attacker manipulates trusted output channels (badges, refs, links, cited sources) |
| LLM Prompt Self-Replication | AML.T0068 | Payload propagates itself through the agent graph |
| ML Supply Chain Compromise | (varies) | Model, adapter, tool, or MCP-server supply-chain compromise |
| ML Model Inversion | AML.T0018 | Reconstruct training data from model behaviour |
| ML Membership Inference | AML.T0024 | Determine training-set membership |
| Resource Development — Publish Hallucinated Entities | (varies) | Slopsquatting: register package names / URLs the model hallucinates |
| Reverse Shell (LLM-mediated) | (varies) | Attacker uses an agent's code-execution tool to gain interactive access |
| Discover LLM System Information | (varies) | Probes to discover model, version, tools, memory schema |

Cite the ATLAS technique ID *and* the version-observed date. When MITRE renumbers, historical citations remain valid because the version marker localises them.

## Surface × ATLAS-technique matrix

The matrix below shows how ATLAS techniques land on the six-surface model. This is the overlay you populate in the ATMD.

| Surface | Primary ATLAS techniques (representative IDs — verify current) |
|---|---|
| **Data-input** | AML.T0051 LLM Prompt Injection; AML.T0054 LLM Jailbreak; AML.T0068 LLM Prompt Self-Replication; AML.T0067 Trusted Output Components Manipulation |
| **Tool-invocation** | AML.T0053 LLM Plugin Compromise; Supply Chain Compromise (varies); Reverse Shell (varies); Publish Hallucinated Entities (slopsquatting) |
| **Memory / VS** | AML.T0058 Publish Poisoned Datasets; AML.T0059 Erode Dataset Integrity; AML.T0057 LLM Data Leakage (on retrieval into privileged contexts) |
| **Environment-observation** | AML.T0051 (indirect variant); AML.T0067 Trusted Output Components Manipulation (browser DOM, cited sources) |
| **Human-in-the-loop** | AML.T0067 (misleading trusted outputs); Discover LLM System Information; social-engineering techniques (varies) |
| **Cross-agent** | AML.T0053 LLM Plugin Compromise (peer-agent as plugin); AML.T0068 LLM Prompt Self-Replication; Supply Chain Compromise |

<!-- needs-research: some ATLAS technique IDs above are pinned to historical release cycles. Cross-check each against the currently published matrix before citing in an ATMD row. -->

## Reading ATLAS case studies as evidence

Every ATLAS technique carries a set of tagged **case studies** — worked adversarial demonstrations. When you cite an ATLAS technique in an ATMD row, cite the specific case study that grounds it, not just the technique ID. The case-study reference is what turns "AML.T0051 applies here" into "AML.T0051 applies here, as demonstrated by case study X." That is the difference between a checklist item and a defensible threat-model row.

Case-study patterns worth studying:

- **Indirect prompt injection demonstrations** — often reference Greshake et al. or later academic work; the ATLAS case-study description makes them infosec-legible.
- **Plugin / tool compromise demonstrations** — often reference real-world plugin ecosystems (early ChatGPT plugin era, MCP compromise). <!-- needs-research: pull the current ATLAS case-study set for tool / plugin techniques and pick two to reference in the ATMD template. -->
- **Data-leakage demonstrations** — Bing chat prompt-extraction, Copilot data leakage patterns.
- **Poisoning demonstrations** — Nightshade, targeted embedding-store poisoning.

## Handling the ID-churn caveat

ATLAS renumbers techniques when the taxonomy evolves. A citation with a version marker survives that renumbering; a bare `AML.T0051` without a version marker degrades to unresolvable in a couple of years. The ATMD row template:

```yaml
mitre_atlas:
  - id: AML.T0051
    version_observed: "2025-Q3"    # example
    source_url: https://atlas.mitre.org/techniques/AML.T0051
    case_study_refs:
      - id: AML.CS0016              # example
        title: <case study title>
```

`version_observed` is the version of the matrix you read when writing the row. `source_url` is the durable technique page. `case_study_refs` are the specific case studies that ground the row.

## The ATLAS tactic view

ATLAS techniques are grouped into tactics (columns of the matrix). A thorough ATMD populates the tactic view *in addition* to the technique view:

| Tactic | Agent-relevant? | Where it lands |
|---|---|---|
| Reconnaissance | ✅ | Probing (Discover LLM System Information, prompt-extraction) |
| Resource Development | ✅ | Publish Poisoned Datasets, Publish Hallucinated Entities, Establish Accounts |
| Initial Access | ✅ | Prompt injection (direct and indirect) is the primary agent-side initial access |
| ML Model Access | Adjacent | White-box / grey-box access — mostly a frontier-lab concern (mod-101, mod-112) |
| Execution | ✅ | Tool-invocation; code interpreter; sub-agent spawn |
| Persistence | ✅ | Memory poisoning; RAG-index poisoning |
| Privilege Escalation | ✅ | Cross-tenant leakage via memory; over-broad credentials |
| Defense Evasion | ✅ | Refusal-erosion; classifier-evasion; approval-fatigue |
| Credential Access | ✅ | LLM07 System Prompt Leakage (secrets in system prompt); tool credentials |
| Discovery | ✅ | Discover LLM System Information; enumerate available tools |
| Collection | ✅ | Aggregating data through legitimate tool calls |
| ML Attack Staging | Adjacent | Adversarial-suffix development on open-weight equivalents |
| Exfiltration | ✅ | Tool-mediated exfiltration; peer-agent laundering |
| Impact | ✅ | Every OWASP Agentic threat lands here as its ultimate consequence |

The tactic view is what an infosec reviewer will read first. Populate it before the technique detail.

## Where ATLAS *doesn't* cover the agent surface well

- **Multi-agent-emergent harms** (chapter 09) — ATLAS is technique-centric and does not naturally express emergent behaviour of agent graphs. Cite ATLAS techniques *within* the graph and describe the emergent behaviour separately.
- **Human-in-the-loop bypass by presentation manipulation** — ATLAS covers social-engineering primitives but the agent-specific "misleading diff shown to approver" pattern is thin. Cite AML.T0067 as the closest analogue and note the disanalogy.
- **RAG-index cross-tenant amplification** — publish-poisoned-datasets covers the setup but not the amplification dynamics. Cite plus explain.

## Handoff to `security` / `ai-infra-security` peer (level 35)

ATLAS is one of the standards that most naturally lands on the platform-security peer's desk. Handoff pattern:

- **This role authors** the ATMD row with ATLAS technique IDs, case-study references, and surface × technique matrix.
- **The security peer consumes** the ATMD row into their platform-scale controls: EDR / SIEM tuning, egress policy, DLP, container-hardening for tool sandboxes, network segmentation.
- **This role receives** platform-side telemetry and updated technique observations that feed back into the ATMD.

The interface artefact is the ATMD's `mitre_atlas` block plus a short "operator-side controls needed" section that says what the security peer should build.

## Common misreadings to avoid

- **"ATLAS is a superset of OWASP."** They serve different audiences. ATLAS reads to infosec; OWASP reads to AI-security practitioners. Cite both.
- **"ATLAS technique IDs are stable."** They are stable *within a release* and adjust across releases. Always cite a version marker.
- **"If it's not in ATLAS it's not a real threat."** ATLAS is a curated taxonomy, and it lags the frontier by design. Novel threats belong in the ATMD with `<!-- needs-research: propose ATLAS technique -->` markers; contribute upstream if you can.
- **"Case studies are optional."** They are the difference between a checklist and a defensible threat-model row. Cite them.

## Summary

- MITRE ATLAS is the infosec-legible spine for the agent threat model; cite technique IDs (with version markers) and case studies.
- The load-bearing agent-side techniques cluster around AML.T0051 (prompt injection), AML.T0054 (jailbreak), AML.T0053 (plugin compromise), AML.T0057 (data leakage), AML.T0058 / T0059 (dataset poisoning), AML.T0067 (trusted-output manipulation), AML.T0068 (prompt self-replication) — verify current IDs before citing.
- Populate both the technique view and the tactic view; the tactic view is what a security-reviewer reads first.
- ATLAS undercovers multi-agent-emergent harms and specific HITL patterns; note the gaps and augment.
- The security / ai-infra-security peer (level 35) is the natural consumer of the ATMD's ATLAS block.
