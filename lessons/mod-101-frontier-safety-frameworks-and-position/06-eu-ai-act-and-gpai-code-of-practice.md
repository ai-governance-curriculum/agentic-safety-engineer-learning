# 06 — EU AI Act Articles 55 / 56 / 73 and the GPAI Code of Practice Safety Chapter

## Motivation

The EU AI Act (Regulation (EU) 2024/1689) is the first major cross-jurisdiction regulation of frontier / general-purpose AI. For a safety engineer, three articles are load-bearing:

- **Article 55** — obligations for providers of general-purpose AI models with **systemic risk** (systemic-risk GPAI models). This is where adversarial testing, evaluation, incident tracking, and cybersecurity obligations attach to the frontier-lab tier.
- **Article 56** — the **codes of practice** mechanism. This is the Article under which the EU AI Office facilitated the **General-Purpose AI Code of Practice**, whose safety-and-security chapter is the operational shape frontier labs sign to (until the harmonised standards land).
- **Article 73** — **reporting of serious incidents**. This applies primarily to high-risk AI systems, but a safety engineer feeds evidence into a program that has to reason about serious-incident classification and reporting under Article 73 for high-risk AI systems and under Article 55 for GPAI-SR models.

If you work on models offered on the EU market, this is the regulatory shape your evidence flows into. If you work outside the EU market, this is still the shape most other jurisdictions are converging toward.

## Primary source

- **[EU AI Act — Regulation (EU) 2024/1689 (EUR-Lex)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj)** — the official Journal text. Article 55, Article 56, and Article 73 are the specific ones this chapter references.
- **[EU AI Office — General-Purpose AI Code of Practice](https://digital-strategy.ec.europa.eu/en/policies/ai-code-practice)** — the Commission's Code of Practice hub. Published in July 2025, with a Safety and Security chapter for providers of GPAI models with systemic risk.
- **[EU AI Office](https://digital-strategy.ec.europa.eu/en/policies/ai-office)** — the Commission body that runs Code of Practice, GPAI enforcement, and coordinates evaluation.
- **[European AI Board](https://digital-strategy.ec.europa.eu/en/policies/ai-board)** — the coordinating body across Member States.

<!-- needs-research: cross-check the current signatory list for the GPAI Code of Practice and the exact chapter / commitment / measure structure of the Safety and Security chapter against the currently published version. Sync the summary here to any amendments. -->

## Core concepts

### Article 55 — obligations for providers of GPAI models with systemic risk

A **general-purpose AI model with systemic risk** is a GPAI model that either meets a compute-based presumption or is designated as such by the Commission based on impact criteria.

<!-- needs-research: confirm the exact language and compute threshold for systemic-risk GPAI designation in the current Act text (initial threshold references 10^25 FLOP for training compute; verify current text and delegated-act updates). -->

Article 55 places obligations on providers of these models that a safety engineer feeds evidence into. The obligations broadly include:

- **Model evaluation** — including adversarial testing (this is where the safety engineer's red-team evidence lands).
- **Systemic-risk assessment and mitigation** — the safety-case-shaped reasoning about the specific systemic risks the model presents.
- **Serious-incident tracking and reporting** — logging, tracking, and reporting relevant incidents (a specific reporting lane exists for GPAI-SR incidents in addition to the Article 73 lane for high-risk AI systems).
- **Cybersecurity protection** — of the model itself and its physical infrastructure (this aligns with the security-safeguard side of the RSP / Preparedness / FSF).

For an engineer, this is the article whose obligations *your* dangerous-capability elicitation, red-team, and monitoring evidence directly satisfies.

### Article 56 — codes of practice

Article 56 establishes the Commission mechanism for **codes of practice** that operationalise the AI Act's obligations, particularly for GPAI models. The AI Office was mandated to facilitate the drawing-up of codes of practice, and the resulting **General-Purpose AI Code of Practice** was published in **July 2025**.

The Code of Practice is a **voluntary** implementation lane — signatory providers commit to the specific commitments and measures in the Code as evidence they meet the corresponding Act obligations, and non-signatories retain the obligation to demonstrate compliance through another means until harmonised standards under Article 40 are available.

The Code is structured into chapters:

- A **Transparency chapter** — applicable to all GPAI providers.
- A **Copyright chapter** — applicable to all GPAI providers.
- A **Safety and Security chapter** — applicable only to providers of GPAI models with **systemic risk**.

The Safety and Security chapter is the chapter this course lives inside.

<!-- needs-research: confirm the current chapter list, the mapping of chapters to Article 53 / 55 obligations, and the current signatory list (a public signing round occurred in July–September 2025). -->

### The Safety and Security chapter — engineer's reading

The Safety and Security chapter operationalises Article 55 obligations for GPAI-SR providers into a hierarchy of **commitments** and, under each commitment, concrete **measures**. As an engineer you should be able to place each commitment against your artifact catalog.

Broadly, the chapter covers:

- **Systemic-risk identification and analysis** — how the provider identifies which systemic risks are relevant to its model. This ties directly to the RSP capability threshold / Preparedness category / FSF CCL machinery.
- **Systemic-risk assessment** — evaluations against the identified risks, at defined cadence, with defined methodology. This is where mod-106 (dangerous-capability elicitation) and mod-110 (control and deception evaluation) flow.
- **Systemic-risk mitigation** — the safeguards required at the tier. This ties to mod-107 (containment) and mod-108 (guardrails).
- **Governance around the framework** — a **safety and security framework** the provider maintains and publishes, plus internal governance around it. This is your analogue to the RSP / Preparedness / FSF document.
- **Reporting and post-market monitoring** — including reporting to the AI Office and cooperation with regulators.
- **Serious incident tracking and reporting** — the incident-response lane.
- **Transparency to the AI Office** — model reports and safety-case-shaped documentation submitted to the Office.

<!-- needs-research: pull the exact commitment / measure structure from the currently published Safety and Security chapter and map each measure to the module(s) in this track that cover its engineering craft. -->

### Article 73 — reporting of serious incidents

Article 73 requires providers of **high-risk AI systems** to report **serious incidents** to the market surveillance authorities of the Member States where the incident occurred. The article defines timelines and information requirements.

For a safety engineer at a frontier lab that also provides high-risk AI systems (e.g., a foundation model integrated into a high-risk application in Annex III), Article 73 is directly relevant. Even where the frontier model itself is not a high-risk AI system, the provider may face parallel serious-incident reporting obligations under Article 55 for the GPAI-SR side, and these two lanes need to be coordinated.

<!-- needs-research: pull the specific serious-incident definition, notification timelines, and downstream information-flow requirements from the current Act text and reflect them here exactly. Do not paraphrase timelines. -->

Concretely, for the engineer, Article 73 shapes:

- **What counts as a serious incident** — the classification decision your incident-response program must make against Article 3(49) (definitions) and the Commission guidance.
- **The notification timeline** — you have to be able to detect, classify, and notify inside the required window.
- **The information package** — what facts, evidence, and analysis are attached to a serious-incident report.
- **The cross-reporting** — coordination with the AI Office for GPAI-SR incidents, market surveillance authorities for high-risk AI system incidents, and (in parallel) with data protection authorities, ENISA, and product safety authorities where applicable.

### Interaction with the RSP / Preparedness / FSF

The frontier-lab frameworks and the EU regulatory shape are *complementary*, not competing:

- The frontier-lab framework is the **provider's internal governance** — the RSP / Preparedness / FSF plays the role the AI Act calls a "safety and security framework" for the GPAI-SR provider.
- The Code of Practice Safety and Security chapter names the **shape** the internal governance framework has to have, the **cadence** of evaluations, and the **reporting** to the Office.
- Article 73 and Article 55 name the **serious-incident** reporting lanes.

An engineer's evidence artifact (elicitation report, safety case, system card entry) is the shared substrate that populates *both* internal governance and EU regulatory filings — you author it once and route it into both.

## Reading the EU shape as an engineer — a worked example

Assume you have completed a dangerous-capability elicitation on a new frontier model, produced a Preparedness scorecard entry, and are ready to file evidence.

For the EU shape, you would:

1. **Classify the model.** Is this a GPAI model? Is it a GPAI-SR model (compute-based presumption or Commission designation)? Answer determines which obligations attach.
2. **Map the elicitation report to the Safety and Security chapter.** Which commitment / measure does it evidence? File it under that measure.
3. **Update the safety and security framework.** If this eval changed the tier decision, update the internal framework document accordingly (the AI Act commits GPAI-SR providers to maintain and publish this framework).
4. **Update the model report.** The Code contemplates providers submitting model reports to the AI Office; your evidence flows into the current model report.
5. **Wire the incident lanes.** If your evaluation surfaced a *pattern* of behaviour that could constitute a serious incident (e.g., in-the-wild abuse), route through the internal incident-response lane and evaluate against Article 55 GPAI-SR reporting and, if applicable, Article 73 high-risk AI system reporting.
6. **Cross-audience the artifact.** The same eval report may need to be cited in a system card (public), a Code of Practice model report (Office), a Frontier Model Forum working paper (industry), and an AISI submission (UK / US). Note the audiences at the top of the artifact.

## Engineering-artifact map

| Artifact | Purpose | Where authored |
|---|---|---|
| Model classification note | GPAI vs. GPAI-SR vs. high-risk AI system decision; drives which obligations attach. | mod-101 depth. |
| Safety and Security framework document | Provider's internal governance framework, mapped to Code Safety and Security chapter. | mod-112 depth. |
| Model report | Structured report per Code measure, filed with the AI Office. | mod-112. |
| Serious incident report | Article 73 (high-risk AI systems) or Article 55 GPAI-SR incident report. | mod-112. |
| Elicitation and red-team evidence packet | Evidence populated into the Safety and Security chapter measures. | mod-106, mod-108, mod-110, mod-111. |
| Systemic-risk assessment | The provider's structured argument that identified systemic risks are adequately mitigated. | mod-109 (safety cases) + mod-112. |

## Common misreadings to avoid

- **Assuming Article 73 covers all AI-safety incidents.** Article 73 is specifically for high-risk AI systems. GPAI-SR serious-incident tracking sits under Article 55. Coordinate both lanes.
- **Assuming the Code of Practice is mandatory.** It is a voluntary implementation lane. Non-signatories retain the obligation to demonstrate compliance through another means; the *obligation* is the Act, the Code is one accepted way to meet it.
- **Assuming the Code substitutes for internal governance.** It does not — it names the shape internal governance has to have.
- **Treating GPAI and GPAI-SR as the same thing.** Only GPAI-SR triggers Article 55 systemic-risk obligations and the Safety and Security chapter.
- **Paraphrasing the timelines.** Article 73 timelines are precise; do not restate them from memory in an artifact. Cite the Article directly.

## Summary

- Article 55 places systemic-risk obligations (evaluation, adversarial testing, mitigation, cybersecurity, serious-incident tracking) on providers of GPAI models with systemic risk.
- Article 56 establishes codes of practice; the **General-Purpose AI Code of Practice** (2025) is the resulting voluntary implementation lane, with a **Safety and Security chapter** for GPAI-SR providers.
- Article 73 requires serious-incident reporting for high-risk AI systems, with defined timelines and information requirements.
- An engineer produces evidence artifacts once and routes them into (i) internal governance (RSP / Preparedness / FSF-shape), (ii) EU Code of Practice model reports, (iii) serious-incident reporting where triggered, and (iv) parallel public disclosure (system cards).
