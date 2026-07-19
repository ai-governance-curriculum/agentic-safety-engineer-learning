# Resources — mod-109 (Safety Cases and Structured Argumentation)

Curated reading for the module. Version-pin every citation when it lands in an artefact; the mod-109 review-readiness posture requires it.

## Primary sources — the working templates

- **Clymer, Gabrieli, Krueger, Larsen — "Safety Cases: How to Justify the Safety of Advanced AI Systems" (arXiv:2403.10462)** — <https://arxiv.org/abs/2403.10462>. The load-bearing paper for the module; defines the four argument shapes (Inability, Control, Trustworthiness, Deference) chapter 03 walks.
- **Goemans, Buhl, Schuett, Korbak, Wang, Hilton, Irving — "Safety Case Template for Frontier AI"** — <https://arxiv.org/abs/2411.08088> <!-- needs-research: confirm arXiv identifier for Goemans et al.'s safety-case template. --> A worked *Control*-shape safety-case template; chapter 04's authoring workflow scaffolds against it.
- **Greenblatt, Shlegeris, and Redwood Research — "AI Control: Improving Safety Despite Intentional Subversion"** — <https://arxiv.org/abs/2312.06942> <!-- needs-research: confirm arXiv identifier and canonical title for the Redwood AI Control paper. --> Methodological foundation for Control-shape arguments under an adversarially aligned model.

## GSN specification and assurance-case standards

- **GSN Community Standard v3, Assurance Case Working Group** — <https://scsc.uk/gsn>. Canonical specification. <!-- needs-research: pin the exact published revision (v3, minor version) when citing in the safety case artefact. -->
- **Kelly and Weaver — "The Goal Structuring Notation: A Safety Argument Notation"** — <https://www.york.ac.uk/media/tftv/documents/GSN-Kelly-Weaver.pdf> <!-- needs-research: confirm the canonical URL / citation for the Kelly and Weaver GSN introduction (York University hosted PDF is the expected form). --> Historical foundation.
- **ISO/IEC/IEEE 15026-2 — Systems and software assurance — Part 2: Assurance case** — <https://www.iso.org/standard/74396.html> <!-- needs-research: confirm the current published edition and whether the standards-body assurance-case terminology aligns with GSN v3 as cited. --> The standards-body view of assurance cases.
- **Bloomfield and Bishop — "Safety and Assurance Cases: Past, Present, and Possible Future"** — <https://openaccess.city.ac.uk/id/eprint/463/> <!-- needs-research: confirm the canonical citation and URL for Bloomfield & Bishop's assurance-case review. --> Standing critique of assurance-case practice; useful failure-mode inventory.

## Frontier-safety framework contexts

- **Anthropic — Responsible Scaling Policy** — <https://www.anthropic.com/rsp>. ASL tier framework; the primary framework context for cases in the Anthropic review context.
- **OpenAI — Preparedness Framework** — <https://openai.com/safety/preparedness>. Preparedness scorecard structure; primary framework context for cases in the OpenAI review context.
- **Google DeepMind — Frontier Safety Framework** — <https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/> <!-- needs-research: pin the current FSF version and canonical URL. --> Critical Capability Level thresholds; primary framework context for cases in the DeepMind review context.
- **EU AI Act — Articles 55, 56, 73** — <https://artificialintelligenceact.eu/> <!-- needs-research: confirm the authoritative URL for the EU AI Act consolidated text; pin Article 55 / 56 GPAI provisions and Article 73 serious-incident-report language when cited. --> Regulator-facing disclosure surface.
- **EU GPAI Code of Practice — safety and security chapter** — <https://digital-strategy.ec.europa.eu/> <!-- needs-research: confirm the canonical URL for the finalised EU GPAI Code of Practice and pin the safety-and-security chapter specifically. --> Co-regulatory framework alignment.

## Regulator / AISI reference

- **UK AI Safety Institute — pre-deployment evaluation methodology** — <https://www.aisi.gov.uk/> <!-- needs-research: pin the specific AISI methodology publication used for pre-deployment reports; expected artefact family is the pre-deployment / early-access evaluation report series. --> External-evaluator posture chapter 05's rebuttal-handling anticipates.
- **US AI Safety Institute (NIST) — evaluations methodology** — <https://www.nist.gov/aisi> <!-- needs-research: pin the specific US AISI methodology publication for pre-deployment evaluations. --> The US-side external-evaluator posture.
- **Bletchley Declaration (2023) and Seoul AI Summit outcomes (2024)** — <https://www.gov.uk/government/topical-events/ai-safety-summit-2023> <!-- needs-research: confirm the canonical URLs for the Bletchley Declaration text and the Seoul AI Summit outcome documents. --> Multilateral framework context.
- **US Executive Order 14110 — "Safe, Secure, and Trustworthy AI"** — <https://www.whitehouse.gov/briefing-room/presidential-actions/2023/10/30/executive-order-on-the-safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence/> <!-- needs-research: verify the current authoritative URL for EO 14110 and whether it remains in force. --> US framework context.

## Adjacent methodological references

- **Meta CyberSecEval 2 / 3** — <https://ai.meta.com/research/publications/cyberseceval-3-advancing-the-evaluation-of-cybersecurity-risks-and-capabilities-in-large-language-models/> <!-- needs-research: pin the exact publication used as the cyber-offense evidence context. --> Evidence-methodology reference for the cyber-offense sub-goal in Inability-leg decomposition.
- **HarmBench / JailbreakBench / AIR-Bench 2024 / MLCommons AILuminate** — see mod-104 resources. Evidence-methodology reference for the refusal-robustness sub-goal in Trustworthiness-leg decomposition.
- **METR public tasks, RE-Bench, SWE-bench Verified** — see mod-106 resources. Evidence-methodology reference for the autonomy sub-goal in Inability-leg decomposition.
- **WMDP (Weapons of Mass Destruction Proxy)** — <https://www.wmdp.ai/> <!-- needs-research: confirm current WMDP canonical URL and citation. --> CBRN-uplift evidence context.

## Adjacent role and boundary references

- **`ai-evaluation-engineer` (peer / next-up, level 35, Governance family)** — release-assurance methodology; consumes the safety case as evidence.
- **`senior-ai-governance-architect` (level 50, Governance family)** — control-library architecture; the safety case cites the library.
- **`ai-infra-security` (peer, level 35)** — platform-level runtime hardening; the *Control* leg's engineering interfaces sit on this platform (see mod-107 chapter 06).
- **`fine-tuning-engineer` (peer, level 30, ML Engineering family)** — safety-tuning stack; mod-104's refusal-robustness evidence and mod-110's alignment-faking stress-tests depend on this role's artefacts.
