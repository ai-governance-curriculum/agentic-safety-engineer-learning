# Resources for mod-112-safety-program-and-disclosure (Frontier-Safety Program, Serious-Incident Response, and Disclosure)

Version-pin every reference before you cite it in an FSPC, tier-decision memo, system card, or regulator submission. The frameworks below revise on a regular cadence; a mis-cited version is one of the first findings a reviewer catches.

## Frontier-lab policies and frameworks

- **Anthropic Responsible Scaling Policy (RSP)** — [anthropic.com/rsp](https://www.anthropic.com/rsp). The ASL framing, pre-registered evaluation thresholds, and Responsible-Scaling Officer role. <!-- needs-research: pin the current published RSP version (Anthropic revises regularly) and the URL of the current PDF. -->
- **OpenAI Preparedness Framework** — [openai.com/safety/preparedness](https://openai.com/safety/preparedness). The scorecard, tracked risk categories, and Preparedness Committee routing. <!-- needs-research: pin the current published version of the Preparedness Framework (v2 released 2025) and any subsequent revisions. -->
- **Google DeepMind Frontier Safety Framework (FSF)** — [deepmind.google](https://deepmind.google). Critical Capability Levels, mitigation tiers, and periodic-review commitment. <!-- needs-research: verify the exact URL of the current FSF publication and pin the published version. -->
- **Anthropic Claude system cards** — [anthropic.com/news](https://www.anthropic.com/news). Read the most recent Claude family system card end-to-end before authoring an Anthropic-shape card. <!-- needs-research: pin the current published Claude system-card URL and version. -->
- **OpenAI system cards** — [openai.com/safety](https://openai.com/safety). Read the most recent GPT / o-series card end-to-end before authoring an OpenAI-shape card. <!-- needs-research: pin the current GPT / o-series system-card URL and version. -->
- **Google DeepMind Gemini system cards** — [deepmind.google/technologies/gemini](https://deepmind.google/technologies/gemini/). Read the most recent Gemini system card end-to-end before authoring a DeepMind-shape card. <!-- needs-research: pin the current Gemini system-card URL and version. -->
- **Frontier Model Forum** — [frontiermodelforum.org](https://www.frontiermodelforum.org/). Cross-lab coordination body; read the published working-group outputs on incident-sharing norms and pre-deployment evaluation baselines. <!-- needs-research: pin the specific FMF publications the FSPC and incident-response contract cite. -->

## Regulatory instruments — EU

- **EU AI Act — Regulation (EU) 2024/1689** — [eur-lex.europa.eu/eli/reg/2024/1689/oj](https://eur-lex.europa.eu/eli/reg/2024/1689/oj). Consolidated text. Articles 3 (definitions including serious incident), 51 (systemic-risk classification), 55 (systemic-risk GPAI obligations), 56 (Code of Practice), and 73 (serious-incident reporting) are load-bearing at model-provider scope. <!-- needs-research: verify EUR-Lex URL stability and confirm article numbering for the current consolidated text; verify Article 73's exact scope, timing, and applicability to systemic-risk GPAI providers. -->
- **European Commission — General-Purpose AI Code of Practice** — [digital-strategy.ec.europa.eu/en/policies/ai-code-practice](https://digital-strategy.ec.europa.eu/en/policies/ai-code-practice). The Code of Practice under Article 56, including the safety-and-security chapter. <!-- needs-research: pin the current adopted version and the specific commitments in the safety-and-security chapter. -->
- **European AI Office** — [digital-strategy.ec.europa.eu/en/policies/ai-office](https://digital-strategy.ec.europa.eu/en/policies/ai-office). The AI Office is the enforcement body for GPAI obligations; watch for its implementing guidance and reporting templates. <!-- needs-research: pin the AI Office's published implementing guidance and any templates for Article 55 / 73 submissions. -->

## Regulatory instruments — UK / US / global

- **UK AI Safety Institute (UK AISI)** — [aisi.gov.uk](https://www.aisi.gov.uk/). Evaluation methodology publications, pre-deployment evaluation reports on frontier models. <!-- needs-research: pin the specific UK AISI methodology publications, pre-deployment evaluation reports, and any published MoU arrangements. -->
- **US AI Safety Institute (US AISI, under NIST)** — [nist.gov/aisi](https://www.nist.gov/aisi). Evaluation methodology publications, controlled-access facilities for redacted disclosure. <!-- needs-research: pin the specific US AISI publications and Memoranda of Understanding governing model-access-for-evaluation arrangements. -->
- **NIST AI Risk Management Framework (AI RMF 1.0)** — [nist.gov/itl/ai-risk-management-framework](https://www.nist.gov/itl/ai-risk-management-framework). The framework-level guidance on model documentation, risk management, and transparency. <!-- needs-research: pin the specific NIST publication (AI RMF playbook or a companion publication) that most directly frames system-card content and generative-AI risk management. -->
- **Executive Order 14179 (US, January 2025)** — the executive order that revoked and replaced EO 14110. <!-- needs-research: verify the exact US executive-order landscape as of the current date and pin the most recent superseding orders. -->
- **NIST SP 800-61 — Computer Security Incident Handling Guide** — [csrc.nist.gov/publications/detail/sp/800-61](https://csrc.nist.gov/publications/detail/sp/800-61/). Adapted for AI-safety incident-response in chapter 05. <!-- needs-research: verify current revision status (800-61r3 or similar) and pin the exact revision. -->

## AISI methodology and third-party evaluators

- **METR (Model Evaluation and Threat Research)** — [metr.org](https://metr.org/). One of the most-cited third-party autonomy-elicitation methodologies; a common evidence input to Preparedness scorecard and RSP threshold determinations. <!-- needs-research: pin specific METR publications the tier-decision memos cite. -->
- **Apollo Research** — <!-- needs-research: pin Apollo Research's URL and the specific deception-evaluation publications relevant to mod-110 / mod-112 disclosure. -->
- **UK AISI evaluation methodology publications** — under [aisi.gov.uk](https://www.aisi.gov.uk/). Read for pre-deployment evaluation shape. <!-- needs-research: pin specific methodology publications. -->

## Certifications — governance vocabulary

- **IAPP AI Governance Professional (AIGP)** — [iapp.org/certify/aigp](https://iapp.org/certify/aigp/). The most-cited AI governance certification; Body of Knowledge covers AI foundations, governance frameworks, development / deployment lifecycles, and the AI regulatory landscape (EU AI Act, NIST AI RMF). <!-- needs-research: pin the current AIGP Body of Knowledge version and exam blueprint. -->
- **ISO/IEC 42001:2023 — Artificial Intelligence Management System** — [iso.org/standard/81230.html](https://www.iso.org/standard/81230.html). The international standard for AI management systems, with associated lead-implementer / lead-auditor training programmes (PECB, BSI, TÜV, and others). <!-- needs-research: pin the exact 42001 publication date, amendments, and recognised LI / LA training pathways; pin the Annex A control references that most naturally map to the FSPC. -->
- **ForHumanity Independent AI Auditor (IAA)** — [forhumanity.center](https://forhumanity.center/). Independent-audit-and-assurance programme oriented toward EU AI Act conformity assessment and third-party audit. <!-- needs-research: pin the current IAA programme structure and recognised examinations. -->
- **BABL AI** — [babl.ai](https://babl.ai/). Algorithmic-auditing training oriented toward bias detection and audit; maps to NYC Local Law 144 and similar bias-audit regulatory obligations. <!-- needs-research: pin the current BABL AI programme structure and recognised certifications. -->

## Academic and community sources

- **Model Cards for Model Reporting (Mitchell et al., 2019)** — [dl.acm.org/doi/10.1145/3287560.3287596](https://dl.acm.org/doi/10.1145/3287560.3287596). The academic root of the model / system-card genre. Read once for historical framing.
- **Partnership on AI — ABOUT ML documentation practices** — [partnershiponai.org](https://partnershiponai.org/). Early industry work on model documentation that current cards descend from. <!-- needs-research: pin the specific ABOUT ML publication versions the current card genres reference. -->

## Sibling module cross-references

- **mod-106 — Dangerous-Capability Elicitation and Evaluation.** DCER methodology and evidence input for pre-registered thresholds and elicitation-methodology defence (chapter 02).
- **mod-107 — Excessive-Agency Containment Engineering.** EACC summary as the load-bearing content for deployment-mitigations sections (chapters 03 and 04) and the containment posture the incident-response classifier queries (chapter 05).
- **mod-108 — Guardrails and Monitors.** Monitoring posture and detection surface for post-deployment cadence (chapter 01 touch-point 4) and incident detection (chapter 05).
- **mod-109 — Safety Cases and Structured Argumentation.** Safety-case argument that the system card (chapter 03) reduces from and that the tier-decision memo (chapter 02) references.
- **mod-110 — Control, Deception, and Adversarial-Alignment Evaluation.** Deception-detection findings entering the residual-risk statement (chapter 02) and the class-4 incident-response path (chapter 05).
- **mod-111 — Automated and Scaled Red-Teaming.** Red-team findings back-fed as regression fixtures (chapter 05).
