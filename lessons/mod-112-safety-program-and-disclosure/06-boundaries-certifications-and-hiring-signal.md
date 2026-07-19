# 06 — Boundaries, Certifications, and Hiring Signal

## Motivation

mod-112 is the *program-scope* module of the agentic-safety-engineer track. It sits at the interface between the engineering craft (mod-102 through mod-111) and the organisation's governance function. Above it — and adjacent — sit three roles that own decisions and artefacts mod-112 either feeds or hands off to: the **senior-AI-governance-architect** (level 50), the **head-of-AI-governance** (level 60), and the **chief-AI-officer** (level 70). Below and beside — peer / next-up — is the **ai-evaluation-engineer** (level 35, Governance family) who packages release-assurance artefacts against the tier decisions mod-112 supports.

A safety-engineer operating at mod-112 scope needs to know exactly where their craft stops and each of these neighbours' craft begins. Blurring the boundary produces two failure modes: *over-reach*, where the safety-engineer authors artefacts the head-of-governance or chief-AI-officer should own (and the artefacts fail on their governance shape); and *under-reach*, where a safety-engineer defers artefacts they should be owning (and the artefacts fail on their engineering shape). Naming the boundary in advance is what separates a program that scales with the organisation from one that collapses at the next promotion.

The second half of this chapter names the **certifications portfolio** — the external assurance frames that make a safety-engineer's craft legible to executives, boards, regulators, and peers — and the **frontier-safety hiring signal** — the portfolio of published artefacts that recruiters and hiring managers at frontier labs look for. Both are load-bearing for a safety-engineer's ability to *do the work at this level*: certifications provide the vocabulary, and the hiring signal is what surfaces the safety-engineer to the roles that let them operate at frontier scale.

## Primary sources

- **[IAPP AI Governance Professional (AIGP)](https://iapp.org/certify/aigp/)** — the most-cited AI governance certification programme. Body of Knowledge and exam blueprint published. <!-- needs-research: pin the current AIGP Body of Knowledge version and exam blueprint. -->
- **[ISO/IEC 42001:2023 — Artificial intelligence management system](https://www.iso.org/standard/81230.html)** — the international standard for AI management systems, with associated lead-implementer / lead-auditor training programmes. <!-- needs-research: pin the exact ISO/IEC 42001 publication date, current amendments, and the recognised LI / LA training pathways (e.g. PECB, BSI, TÜV). -->
- **[ForHumanity — Independent AI Auditor (IAA)](https://forhumanity.center/)** — an independent audit-and-assurance programme with a training pathway for AI auditors. <!-- needs-research: pin the current IAA programme structure and recognised examinations. -->
- **[BABL AI](https://babl.ai/)** — bias-and-audit training programme, particularly for bias-adjacent AI auditing. <!-- needs-research: pin the current BABL AI programme structure and recognised certifications. -->
- **[Anthropic Claude system cards](https://www.anthropic.com/news)**, **[OpenAI system cards](https://openai.com/safety)**, **[Google DeepMind Gemini system cards](https://deepmind.google/technologies/gemini/)** — the published system cards themselves are hiring-signal artefacts; contributions to specific cards are legible in a safety-engineer's portfolio.
- **[UK AISI publications](https://www.aisi.gov.uk/)** and **[US AISI publications](https://www.nist.gov/aisi)** — AISI methodology contributions are legible hiring-signal artefacts.

Version-pin the certifications' Body of Knowledge and exam blueprints. Certification programmes revise.

## The four neighbouring roles

### `ai-evaluation-engineer` (peer / next-up, level 35, Governance family)

*Owns.* Release-assurance packaging for a specific release. The role composes the safety case (from mod-109), the system card (from chapter 03), the AISI submission (from chapter 04), and the tier-decision memo (from chapter 02) into a *release-assurance package* for a specific model version. Handles the release-calendar coordination, the internal-review sign-off collection, and the artefact-cross-reference maintenance.

*Boundary.* mod-112 authors the artefacts; the ai-evaluation-engineer packages them. mod-112 owns the *content* of the safety case and the system card; the ai-evaluation-engineer owns the *release-assurance packaging discipline* that composes them for one specific release.

*Handoff shape.* mod-112 delivers signed, dated, cross-referenced artefacts to the ai-evaluation-engineer. The ai-evaluation-engineer collates them into the release-assurance package, drives the internal-review sign-off collection, and archives the package into the FSPC's artefact register per chapter 01's retention policy.

*What mod-112 does not do.* Release-calendar coordination. Internal-review scheduling. Artefact-cross-reference maintenance across multiple concurrent releases. These are the ai-evaluation-engineer's craft.

### `senior-ai-governance-architect` (level 50)

*Owns.* The **control-library architecture** the frontier-safety program plugs into. At an organisation with mature AI governance, this role designs the shared control library — the taxonomy of controls the organisation uses across all its AI products, the mapping from controls to regulatory requirements (EU AI Act, NIST AI RMF, ISO/IEC 42001), the shared incident-response taxonomies, and the shared assurance frameworks.

*Boundary.* mod-112 authors the frontier-safety program at the scope of one organisation's frontier-model work. The senior-AI-governance-architect authors the control-library architecture at the scope of the *organisation's entire AI portfolio* — frontier and non-frontier. mod-112's FSPC (chapter 01) plugs into the architect's control library, adopting its control taxonomy and mapping.

*Handoff shape.* mod-112's FSPC references the architect's control library. When the FSPC's incident-response taxonomy (chapter 05) needs a new class, the safety-engineer proposes the class *to the architect* and the architect determines whether the class extends the shared taxonomy or lives only within the frontier-model program.

*What mod-112 does not do.* Design the control library. Author the cross-portfolio control taxonomy. Own the mapping from controls to regulatory requirements at the organisation's full scope. These are the architect's craft.

### `head-of-ai-governance` (level 60)

*Owns.* **Cross-jurisdiction reconciliation** and **board-level reporting**. When the organisation operates across the EU (AI Act), the UK (AISI relationship, sector-specific regulators), the US (federal executive orders, state laws, sector regulators), and Asia-Pacific (various), the head-of-AI-governance owns the reconciliation of governance obligations across the jurisdictions. Also owns the board-level reporting cadence and the board-level narrative about the organisation's AI risk posture.

*Boundary.* mod-112 authors the EU-facing and AISI-facing submissions (chapter 04) at the level of a specific model release. The head-of-AI-governance authors the cross-jurisdiction position — how the organisation *reconciles* its EU obligations with its US and UK positions when they conflict, and how the board reads the organisation's overall AI risk posture across all products and jurisdictions.

*Handoff shape.* mod-112's tier-decision memos, safety cases, and regulator submissions feed the head-of-governance's cross-jurisdiction position. When jurisdictional obligations conflict — an EU disclosure requirement that would violate a US non-disclosure obligation, an AISI methodology contribution that overlaps with a competitive-position concern — the head-of-governance reconciles; mod-112 executes the reconciled position.

*What mod-112 does not do.* Reconcile jurisdictional conflicts. Author board-level narratives. Own the organisation's overall AI risk posture across all products.

### `chief-ai-officer` (level 70)

*Owns.* Organisation-wide AI strategy, executive-committee representation of the AI function, and board-level accountability for AI outcomes. The CAO is the executive who signs the organisation's public commitments (RSP-shape, Preparedness-shape, FSF-shape), the executive who is answerable to shareholders and regulators for AI risk, and the executive who owns the AI function's headcount and budget.

*Boundary.* mod-112 authors the substance the CAO signs. The CAO does not author the tier-decision memo, the system card, or the safety case; they *sign* the ones the safety-engineering leadership presents. Where the CAO decides to sign or defer, the decision is the CAO's; the substance is mod-112's.

*Handoff shape.* The RSO / Preparedness Committee / Safety Board equivalent (chapter 01) sits between mod-112 and the CAO for most decision routing. For decisions that reach the CAO directly — public commitment revisions, major incident-response external statements — mod-112 provides technical evidence; the CAO decides.

*What mod-112 does not do.* Represent the AI function at the executive committee. Own AI headcount or budget. Sign public commitments in the CAO's name. Speak to shareholders about AI risk.

## The boundary in one sentence per neighbour

- **ai-evaluation-engineer:** authors the *release-assurance package*; mod-112 authors the *artefacts inside it*.
- **senior-AI-governance-architect:** authors the *control-library architecture*; mod-112 authors the *frontier-safety program that plugs into it*.
- **head-of-AI-governance:** authors the *cross-jurisdiction and board-level narrative*; mod-112 authors the *jurisdiction-specific submissions and evidence*.
- **chief-AI-officer:** *signs* the organisation's AI commitments; mod-112 authors the *substance the CAO signs*.

## The certifications portfolio

A safety-engineer at level 40 operates in a landscape where regulators, boards, executives, and peers expect the safety-engineer's craft to map to *external assurance frames*. The certifications portfolio is what makes the mapping legible.

Four certifications dominate the current landscape. A defensible portfolio carries at least one; a mature portfolio carries two or three, weighted by the safety-engineer's primary jurisdiction and the organisation's regulatory posture.

### IAPP AIGP — most-cited

*What it is.* The International Association of Privacy Professionals' AI Governance Professional certification. Body of Knowledge covers AI foundations, AI governance frameworks, the AI development lifecycle, the AI deployment lifecycle, and the AI regulatory landscape (with emphasis on the EU AI Act and NIST AI RMF).

*Signal.* The most-cited AI governance certification at the time of writing. Executives, boards, and non-technical governance peers recognise it. The Body of Knowledge is broad rather than deep; it provides shared vocabulary rather than deep technical craft.

*Where it fits.* A safety-engineer operating with governance-adjacent peers, at organisations where the executive committee expects a legible certification, or in jurisdictions where the AIGP is the emerging default.

*What it does not replace.* The engineering craft. AIGP-only professionals cannot author a defensible safety case, tier-decision memo, or Article 73 submission at this level; they can *review* one, and they can *converse* with the engineer authoring one. The engineering craft comes from mod-102 through mod-112; AIGP provides the governance vocabulary.

### ISO/IEC 42001 — lead-implementer / lead-auditor

*What it is.* ISO/IEC 42001:2023 is the international standard for AI management systems. Lead-implementer training prepares professionals to design and operate a 42001-conformant AI management system; lead-auditor training prepares professionals to audit against it.

*Signal.* Load-bearing for organisations with mature governance functions that operate under other ISO management systems (27001 for security, 9001 for quality, 14001 for environment). The certification is *systemic* — it prepares the professional to think in management-system terms.

*Where it fits.* A safety-engineer whose organisation has committed to ISO/IEC 42001 conformance (increasingly common for enterprise-facing AI providers) or who is operating in a jurisdiction where 42001 is emerging as the regulatory-compliance shortcut.

*What it does not replace.* Frontier-safety-specific evidence and disclosure. 42001 is management-system-level; the frontier-safety program's technical evidence is not directly 42001 content, though the FSPC (chapter 01) maps to 42001's clauses. <!-- needs-research: pin the specific 42001 clauses the FSPC most naturally maps to, and any Annex A control references that apply. -->

### ForHumanity Independent AI Auditor (IAA)

*What it is.* An independent-audit-and-assurance programme with a training pathway for AI auditors, oriented toward EU AI Act conformity assessment and independent third-party audit. Emphasises the auditor's independence and evidence-collection discipline.

*Signal.* Load-bearing for professionals who audit against the EU AI Act's high-risk-system requirements or who work with organisations that engage independent third-party audit for their AI systems.

*Where it fits.* A safety-engineer transitioning to (or complementing their role with) independent-audit work, or operating at an organisation that engages independent audit as part of its assurance posture.

*What it does not replace.* Internal-role frontier-safety craft. IAA is auditor-oriented; the safety-engineer authoring a safety case is the *auditee's* engineer, not the auditor. Understanding the auditor's discipline (from IAA) is useful even for internal roles.

### BABL AI — bias-adjacent audit

*What it is.* Training programmes oriented toward algorithmic auditing, with emphasis on bias detection and audit. Includes training that maps to NYC Local Law 144 and similar bias-audit regulatory obligations.

*Signal.* Load-bearing for professionals whose work overlaps with bias-audit obligations — employment AI, credit AI, allocation AI in regulated sectors.

*Where it fits.* A safety-engineer whose organisation's AI products fall under bias-audit regulatory obligations, or who works on AI systems where bias is a first-order concern (which for frontier models becomes increasingly true as they are deployed in high-stakes allocation contexts).

*What it does not replace.* Frontier-safety-specific certification. BABL AI is domain-specific; the AIGP or 42001 provides the broader frame.

### Portfolio composition

A defensible portfolio composition for a level-40 frontier-safety engineer:

- **Primary:** IAPP AIGP. Most-cited; provides shared vocabulary with executives and boards.
- **Secondary (choose one):** ISO/IEC 42001 lead-implementer (for organisations with mature ISO governance), ForHumanity IAA (for professionals with an audit-transition trajectory), or BABL AI (for professionals whose deployments have bias-audit obligations).
- **Tertiary (optional):** A second certification in the secondary tier if the safety-engineer's cross-organisation exposure warrants it.

The portfolio is a *signal* portfolio; the *craft* is the engineering. Certifications enable the safety-engineer to operate legibly in front of governance-adjacent audiences; they do not replace the mod-102 through mod-112 craft that produces the evidence and the artefacts.

## The frontier-safety hiring signal

Frontier labs — Anthropic, OpenAI, Google DeepMind, and the next tier of frontier-model developers — hire safety-engineers at level 40 through a specific portfolio-of-artefacts signal. The certifications above are entry gates; the artefacts below are what hiring managers actually read.

### Signal 1 — RSP / Preparedness / FSF contribution history

*What it is.* Concrete, cite-able contributions to a published RSP tier determination, a published Preparedness scorecard entry, or a published FSF CCL determination. The contribution can be authored (the safety-engineer authored a section of the tier-decision memo) or co-authored (the safety-engineer contributed the elicitation-methodology defence for one capability).

*How it surfaces.* Named in the safety-engineer's portfolio; referenced by the hiring manager during the interview process; sometimes disclosable in the safety-engineer's own writing or talks.

*Why it is load-bearing.* It is the most direct evidence that the safety-engineer has operated at frontier-safety-program scope. Substituting *"I understand the framework"* for *"I contributed to a specific determination"* is one of the fastest hiring signals to lose.

### Signal 2 — Published system-card evidence

*What it is.* Contributions to a published system card — the safety-eval-results section, the deployment-mitigations section, the red-team-disclosure section, or the known-limitations section. Contributions can be public (named in the card's contributor list, where the organisation publishes contributors) or private (verifiable through the safety-engineer's employment history and internal attribution).

*How it surfaces.* Named in the portfolio; verifiable against the published card.

*Why it is load-bearing.* System cards are the most public safety-engineering artefacts. A safety-engineer whose portfolio names one or more cards has demonstrated ability to operate under the disclosure-engineering discipline (chapter 03) at published scale.

### Signal 3 — AISI methodology contributions

*What it is.* Contributions to UK AISI or US AISI methodology — proposed evaluations, elicitation improvements, benchmark-construction advice. Contributions can be direct (through a bilateral relationship where the safety-engineer's organisation participates) or peer-reviewed (through a published paper AISI cites).

*How it surfaces.* Named in the portfolio; referenced in AISI publications that cite the contribution or in the safety-engineer's own publications.

*Why it is load-bearing.* AISI methodology contribution is one of the few externally-verifiable signals of *evaluation-methodology* craft at frontier scope. Hiring managers at frontier labs increasingly weight this signal explicitly.

### Signal 4 — Peer-reviewed publication in safety-adjacent venues

*What it is.* Publications in venues that the frontier-safety community reads: SATML, IEEE S&P, USENIX Security, NeurIPS / ICML (safety tracks), the ICLR SafeGenAI workshop, arXiv preprints that accrete citations. The publication can be primary-authored or co-authored.

*How it surfaces.* Named in the portfolio; the citation and h-index footprint is verifiable.

*Why it is load-bearing.* Frontier labs increasingly staff safety-engineering from researchers or research-adjacent professionals. Publication history is one of the few signals that maps to that trajectory.

### Signal 5 — Frontier Model Forum working-group contribution

*What it is.* Named participation in an FMF working group that produced a published output — cross-lab evaluation methodology, incident-sharing norms, deployment-mitigation baselines.

*How it surfaces.* Named in FMF publications where contributors are attributed.

*Why it is load-bearing.* FMF working-group contribution is a signal of cross-industry safety-engineering craft — the ability to engage with peer organisations on shared problems.

### Portfolio composition

A defensible frontier-safety hiring portfolio at level 40 carries:

- At least one of signal 1 (RSP / Preparedness / FSF contribution history) or signal 2 (published system-card evidence).
- At least one of signal 3 (AISI methodology contributions) or signal 4 (peer-reviewed publication).
- Optionally, signal 5 (FMF working-group contribution) as differentiator.

Plus at least the primary certification (AIGP or equivalent) as vocabulary layer.

The portfolio is what surfaces the safety-engineer to the roles that let them operate at frontier scope. It is also what regulators, AISI evaluators, and the FMF read when they receive a submission or a working-group contribution from the safety-engineer. The two audiences overlap.

## Interfaces

- **Chapter 01 (program).** The FSPC's section-5 sign-off routing names the RSO / Preparedness Committee / Safety Board equivalent; section-7 names the certifications the program leans on. This chapter's certification portfolio maps to section 7.
- **Chapter 02 (tier memo).** The tier-decision memo is authored by mod-112 and signed by the tier body; a hiring-signal contribution is authorship of one or more sections of a published tier determination.
- **Chapter 03 (system card).** System cards are the primary published hiring-signal artefacts.
- **Chapter 04 (regulator disclosure).** AISI submissions and methodology contributions are load-bearing hiring signals.
- **Chapter 05 (incident response).** Incident-response leadership is a hiring signal (though often not public); post-incident regression-fixture back-feeds that are peer-reviewed are.
- **`ai-evaluation-engineer` (peer, level 35, Governance).** Release-assurance packaging role that composes mod-112's artefacts for one release.
- **`senior-ai-governance-architect` (level 50).** Control-library architecture role that mod-112's FSPC plugs into.
- **`head-of-ai-governance` (level 60).** Cross-jurisdiction reconciliation and board-level reporting role that mod-112's submissions feed.
- **`chief-ai-officer` (level 70).** Signs the organisation's AI commitments; mod-112 authors the substance.

## Common misreadings to avoid

- **"Certifications replace craft."** They do not. AIGP is a vocabulary layer; the craft comes from mod-102 through mod-112. A safety-engineer who has only certifications and no engineering artefacts cannot operate at level 40.
- **"Craft replaces certifications."** Not entirely. At the executive and board layer, certifications are the *legibility* by which the craft is recognised. A safety-engineer with strong craft but no certification vocabulary struggles to operate in front of governance-adjacent audiences.
- **"The head-of-AI-governance is just a senior version of the safety-engineer."** No. The head-of-AI-governance operates at cross-jurisdiction and board scope. mod-112's frontier-safety program is one input to the head-of-governance's cross-portfolio view. Different craft.
- **"The chief-AI-officer signs everything."** The CAO signs the top-level commitments and speaks to shareholders. The RSO / Preparedness Committee / Safety Board equivalent signs most tier decisions and safety cases. The CAO is not in the tier-decision-memo signature line for most releases; the tier body is.
- **"Published contributions require the organisation's disclosure permission."** They do. And the discipline is to negotiate the permission *before* the contribution is made, and to structure the contribution such that permission is likely. A contribution the organisation refuses to disclose is a contribution that does not surface as hiring signal, regardless of its internal significance.
- **"AISI is only relevant if my organisation has a bilateral relationship."** AISI's methodology is public; contributions can be made through peer-reviewed publications, workshop participation, and FMF working groups regardless of the safety-engineer's employer.

## Summary

- mod-112 sits at the interface between engineering craft (mod-102 through mod-111) and organisational governance. Four neighbouring roles own the artefacts and decisions mod-112 either feeds or hands off to.
- **`ai-evaluation-engineer`** (peer, level 35) packages release-assurance; mod-112 authors the artefacts inside.
- **`senior-AI-governance-architect`** (level 50) architects the control library; mod-112's FSPC plugs into it.
- **`head-of-AI-governance`** (level 60) reconciles cross-jurisdiction obligations and reports to the board; mod-112 authors jurisdiction-specific submissions.
- **`chief-AI-officer`** (level 70) signs commitments and represents AI to the executive committee and shareholders; mod-112 authors the substance the CAO signs.
- **Certifications portfolio:** IAPP AIGP (most-cited, primary), ISO/IEC 42001 LI/LA (systemic-fit organisations), ForHumanity IAA (audit-transition), BABL AI (bias-adjacent). Certifications are the vocabulary layer; the craft is the engineering.
- **Frontier-safety hiring signal:** RSP / Preparedness / FSF contribution history, published system-card evidence, AISI methodology contributions, peer-reviewed publications, FMF working-group contributions. This is the portfolio hiring managers at frontier labs read.
- Exercise 07 walks the certifications and hiring-signal planner. mod-112 closes with the recognition that the safety-engineer's craft, the certifications portfolio, and the hiring signal all compose — none replaces the others.
