# mod-112-safety-program-and-disclosure: Frontier-Safety Program, Serious-Incident Response, and Disclosure

**Estimated effort:** 16 hours

This module closes the agentic-safety-engineer track by lifting the engineering craft of mod-102 through mod-111 to organisation scope. The safety-engineer authoring at this level owns the *operating rhythm* of a frontier-safety program — the four safety-eval touch-points, the artefact register, the decision register, the sign-off routing — and drives the disclosure engineering that ties evidence to regulators, AISIs, and the public. The module's load-bearing artefact is the **Frontier Safety Program Charter (FSPC)**; every chapter and exercise ships internals against it.

## Learning objectives

- Own a frontier-safety + red-team program at organisation scope — cadence (pre-training / mid-training / pre-deployment / post-deployment safety-eval), artefacts (safety cases + system cards + AISI reports + regulator-facing incident reports), decisions (RSP tier / Preparedness Framework scorecard / FSF critical-capability-level threshold), sign-off routing (Responsible-Scaling Officer / Preparedness Committee / Safety Board).
- Contribute to Responsible Scaling Policy / Preparedness Framework / Frontier Safety Framework tier decisions — pre-registered thresholds, elicitation-methodology defence, rollback contract, deferred-deployment recommendation.
- Author disclosure — Anthropic-shape / OpenAI-shape / Google DeepMind-shape system cards, EU AI Act Article 73 serious-incident reports, EU GPAI Code of Practice safety-and-security disclosure, UK AISI + US AISI pre-deployment report contributions.
- Run serious-incident response — triage of frontier-model incidents (autonomy exceedance, dangerous-capability disclosure, jailbroken agent tool-abuse, deception detection, safety-eval regression), coordination with legal + communications + AISI + regulators, disclosure engineering, permanent regression-fixture back-feed into mod-104 / mod-105 / mod-110 suites.
- Coordinate with `senior-ai-governance-architect` (level 50) on the control-library architecture this program plugs into; hand release-assurance-shape work to `ai-evaluation-engineer` (peer, level 35, Governance family); hand cross-jurisdiction reconciliation and board-level reporting to `head-of-ai-governance` (level 60).
- Position the certifications portfolio — IAPP AIGP (most-cited), ISO/IEC 42001 lead-implementer / lead-auditor, ForHumanity Independent AI Auditor, BABL AI where bias-adjacent — and the frontier-safety hiring signal (RSP / Preparedness / FSF contribution history, published system-card evidence, AISI methodology contributions).

## Chapters

- **[01 — Owning a Frontier-Safety Program at Organisation Scope](01-owning-a-frontier-safety-program-at-organisation-scope.md)** — the FSPC's seven sections, the four safety-eval touch-points, the three frontier-lab frameworks (RSP / Preparedness / FSF), the three named decision bodies.
- **[02 — RSP / Preparedness / FSF Tier Contribution](02-rsp-preparedness-fsf-tier-contribution.md)** — the tier-decision memo genre and its four load-bearing safety-engineer contributions: pre-registered thresholds, elicitation-methodology defence, rollback contract, deferred-deployment recommendation.
- **[03 — Disclosure Engineering and System Cards](03-disclosure-engineering-and-system-cards.md)** — the three current system-card genres (Anthropic / OpenAI / Google DeepMind), the five load-bearing invariants, the audience-separation discipline, and the reduction-from-safety-case problem.
- **[04 — Regulator-Facing Disclosure: EU and AISI](04-regulator-facing-disclosure-eu-and-aisi.md)** — the four regulator-facing surfaces (Article 55, Article 73, GPAI Code of Practice, UK AISI + US AISI pre-deployment reports), the shape/payload redaction discipline, and the pre-authored template approach.
- **[05 — Serious-Incident Response with Regulator and AISI Coordination](05-serious-incident-response-with-regulator-and-aisi-coordination.md)** — the five incident classes, the triage timeline, the coordination roster, the disclosure-engineering shape during an incident, and the permanent regression-fixture back-feed workflow.
- **[06 — Boundaries, Certifications, and Hiring Signal](06-boundaries-certifications-and-hiring-signal.md)** — the four neighbouring roles mod-112 hands off to, the certifications portfolio (AIGP / 42001 LI-LA / ForHumanity IAA / BABL AI), and the five hiring-signal artefacts frontier labs read.

## Exercises

Each exercise is a prompt — the solution lives in the paired [`agentic-safety-engineer-solutions`](https://github.com/ai-governance-curriculum/agentic-safety-engineer-solutions) repo. Payload discipline (chapter 04) applies to every exercise: keep deliverables in personal notes or a private repo; do not commit into the course repo.

- **[exercise-01 — Frontier Safety Program Charter for one org](exercises/exercise-01-frontier-safety-program-charter-for-one-org.md)** — author the FSPC's seven sections end-to-end for one concrete organisation (2 hours).
- **[exercise-02 — RSP / Preparedness / FSF tier contribution drill](exercises/exercise-02-rsp-preparedness-fsf-tier-contribution-drill.md)** — draft a tier-decision memo with the four load-bearing contributions for one model version under one framework (3 hours).
- **[exercise-03 — System card authoring in Anthropic / OpenAI / DeepMind shape](exercises/exercise-03-system-card-authoring-in-anthropic-openai-and-deepmind-shape.md)** — produce a system card in each of the three genres for one model version, with a per-claim reduction-decision log (3 hours).
- **[exercise-04 — EU Article 73 and GPAI Code safety disclosure drill](exercises/exercise-04-eu-article-73-and-gpai-code-safety-disclosure-drill.md)** — author Article 73 initial and full-report templates, fill them for one synthetic incident scenario, and draft the corresponding GPAI Code of Practice disclosure entries (2 hours).
- **[exercise-05 — Serious-incident response with regulator and AISI coordination](exercises/exercise-05-serious-incident-response-with-regulator-and-aisi-coordination.md)** — run a full tabletop incident-response drill for one of chapter 05's five incident classes, with handoffs into exercises 02 / 03 / 04 / 06 (3 hours).
- **[exercise-06 — Regression fixture back-feed workflow](exercises/exercise-06-regression-fixture-back-feed-workflow.md)** — design a permanent regression fixture for one incident and back-feed it into the correct suite per chapter 05's class-to-suite mapping (2 hours).
- **[exercise-07 — Certifications and hiring-signal planner](exercises/exercise-07-certifications-and-hiring-signal-planner.md)** — author your own 12–24 month certifications-and-hiring-signal plan against chapter 06's four-certification and five-signal portfolios (1 hour).

## Structure

- `01-…md` … `06-…md`: lecture chapters.
- `exercises/`: per-exercise prompts.
- `labs/`: long-form hands-on labs (currently placeholder).
- `quizzes/`: knowledge checks (currently placeholder).
- `resources.md`: external references, version-pinned per chapter.
