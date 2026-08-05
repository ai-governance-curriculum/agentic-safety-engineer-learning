# exercise-02: Inability × Control × Trustworthiness × Deference Drill

**Estimated effort:** 2 hours

## Objective

Drill the four Clymer-et-al. argument shapes by (a) classifying a supplied evidence portfolio into the shape each item load-bears in, (b) composing the shapes into a top-claim decomposition for two contrasting deployment profiles at different tiers, and (c) auditing the *compositional assumptions* the two compositions inherit. The output is a short worked artefact (`shape-composition.md`) plus an *assumption-audit table* that surfaces which assumptions load-bear on more than one shape and which shape is the load-bearing owner of each.

The exercise is deliberately narrow: no evidence generation, no threat-model authorship, no full case. It exists so that when you sit down to exercise 03 (the full-case capstone) you already know which evidence belongs on which leg and where the compositional-assumption traps are. Chapter 03 named the shapes; this exercise makes shape-selection reflex.

## Prerequisites

- Read chapter 03 (Inability × Control × Trustworthiness × Deference) end-to-end. The four *When defensible* / *When not defensible* subsections per shape are the material this exercise leans on hardest.
- Skim chapter 01 §*"The composition — where mod-104 through mod-110 land in the case"* for the evidence-to-shape mapping this exercise assumes.
- Complete exercise 01 (GSN notation fluency). This exercise writes its composition in the same structured outline format.
- Skim [Clymer et al. §4](https://arxiv.org/abs/2403.10462) for the shape-composition worked examples the paper walks. The Redwood-style AI Control paper is a useful *Control*-shape reference; the Goemans safety-case template is the reference for what a Control-heavy case looks like end-to-end.
- Skim the tier language of at least two of [Anthropic RSP](https://www.anthropic.com/rsp), [OpenAI Preparedness Framework](https://openai.com/safety/preparedness), and [Google DeepMind Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/) so that shape prescription per tier is not a surprise when Part B asks you to pick a shape composition.

## Requirements

### Part A — Classify the evidence portfolio into the four shapes

Given the supplied evidence portfolio below, produce a table (`evidence-classification.md`) with one row per evidence item and columns:

- **Item ID** — as listed below.
- **Primary shape it load-bears in** — exactly one of Inability, Control, Trustworthiness, Deference.
- **Secondary shapes it contributes to** (optional) — zero or more of the other three, if the same evidence supplies non-load-bearing signal to another leg. Note chapter 03's *"do not double-count evidence across shapes"* rule: the same item cannot be *load-bearing* in two shapes at once.
- **Justification** — one or two sentences naming why the primary shape is right (which claim shape the evidence supports, which assumption it depends on, which chapter-03 *when-defensible* condition it satisfies).
- **Which chapter-03 assumption it *depends on***— name the assumption using chapter 03's `A-...` labels (`A-ELICITATION-COMPLETE`, `A-MODEL-STABILITY`, `A-CONTAINMENT-INTEGRITY`, etc.).

Evidence portfolio:

- **E-1** — mod-106 DCER against the WMDP-Chem hazard set at BON=64, adversarial-fine-tune-elicited, δ bounded to ≤ 1.5×, external chemistry-domain-expert grader panel.
- **E-2** — mod-107 EACC v3.7 §egress (allow-listed hosts, PoLA credential broker, tamper-evident audit log) with 30-day retrospective wrapper-log stream showing zero policy violations.
- **E-3** — mod-108 fine-tuned refusal-classifier eval — precision 98.5% / recall 96.8% on a labelled corpus, adaptive-attack-survival curve degrading from recall 96.8% to 88% at 200 red-team hours.
- **E-4** — mod-104 jailbreak-suite regression run against HarmBench + JailbreakBench + AIR-Bench, StrongREJECT judge v2.1, aggregate ASR 3.1% [1.8, 5.0] 95% CI.
- **E-5** — mod-110 Redwood-style control-eval: red-team-blue-team game under a sandbagging + alignment-faking threat model, blue-team protocol contained 94% of adversarial attempts over 500 trials.
- **E-6** — mod-110 Apollo-Research-style deception evaluation: honesty probes, hidden-scratchpad tests; model did not misrepresent its own behaviour in 96% of test cases.
- **E-7** — mod-107 chapter-05 kill-switch drill runbook: drill executed 2026-02-01, drill-fire latency 42 s, post-fire propagation to all deployment shards confirmed in 87 s.
- **E-8** — UK AISI pre-deployment engagement report, docket `AISI-2026-Q1-<pin>`: AISI's independent evaluation flagged no capabilities above threshold on their evaluation set; report also notes *"we could not verify the elicitation-gap protocol was applied uniformly across the CBRN sub-categories."*
- **E-9** — Anthropic-methodology alignment-faking test: model showed no evidence of deliberate underperformance-when-observed / overperformance-when-not-observed on the tests exercised.
- **E-10** — mod-107 chapter-04 HITL escalation contract: escalation rules, bypass-prevention posture, 30-day retro of escalation timings (p50 = 4 min, p95 = 22 min, no bypasses).
- **E-11** — Third-party red-team engagement report: external cyber-offense red-team ran a 2-week engagement against the deployed stack, no successful cyber-uplift trajectories found.
- **E-12** — Academic peer-reviewed publication describing the elicitation-gap methodology mod-106's DCER protocol is grounded in.

Deliverables for Part A:

- `evidence-classification.md` — the table above.
- A short paragraph naming the *one* evidence item you found hardest to classify and why. This is the fluency signal Part A is testing.

### Part B — Compose the shapes on two contrasting deployments

For each of the two deployment profiles below, produce a **top-claim decomposition outline** (in the structured markdown outline format from exercise 01) that:

- States the top claim `G-TOP` explicitly, with the deployment scope, review period, threat model, and threshold as `C-` context nodes.
- Applies a top-level strategy `S-TOP` decomposing by argument shape (Clymer's §4 shape composition).
- Names each shape's sub-goal (`G-INABILITY`, `G-CONTROL`, `G-TRUSTWORTHINESS`, `G-DEFERENCE`) and grounds it in at least one evidence item from Part A's portfolio.
- Marks any shape you deliberately *drop* from the composition with a `J-` justification explaining why (per chapter 03's *"when not defensible"* subsections).
- Marks any load-bearing sub-goal for which no supplied evidence suffices with `[undeveloped]` and a closure plan.

**Deployment α — Low-tier chatbot.**

> Internal-support chatbot, text-in / text-out, no tools, authenticated internal users only, ASL-2-equivalent review context, hazard class of concern = CBRN synthesis-instruction uplift. Framework tier: RSP ASL-2 (or Preparedness `Medium` for CBRN). Threat model: determined jailbreaker, no fine-tuning, ≤ 6 hours per session, single-user (from mod-102 TM profile B).

**Deployment β — High-blast-radius code-modification agent.**

> Code-modification agent with tool access to `git_clone`, `git_commit`, `pytest_run`, and `bugtracker_status_update` against a service-account principal in an internal CI job; ASL-3-equivalent review context (autonomy-adjacent capabilities in scope), hazard classes of concern = malicious-code merge, source-code exfiltration, cross-repo blast radius. Framework tier: RSP ASL-3 or Preparedness `High` for autonomy. Threat model: adversarial-inputs-embedded-in-pull-requests + a *sandbagging / alignment-faking* stress-test posture.

For each deployment, name explicitly which shape is **load-bearing**, which are **contributing**, and which — if any — are **inadmissible**. Chapter 03 §*"When Inability is not defensible"* is the reference for deployment β; chapter 03 §*"When Deference is defensible"* is the reference for shaping deployment α's Deference contribution.

### Part C — Compositional-assumption audit

Now unify the two compositions and audit their assumption sets. Produce `assumption-audit.md` with:

- A **union table** of all `A-` assumption nodes across the two composed cases, one row per unique assumption.
- Per assumption: **which shape(s) it load-bears for** (a single row can list more than one — that is exactly what makes it *compositional*).
- Per assumption: **which upstream module owns the evidence that would strengthen or refute it** (mod-106 for elicitation-gap, mod-107 for containment-integrity, mod-108 for monitor-coverage, mod-110 for adversarial-alignment, and so on).
- A short **rebuttal-preview paragraph** for each of the two or three most load-bearing compositional assumptions — one sentence stating the counter-argument a reviewer would bring, one sentence stating the case's response (either a rehearsed defence or an acknowledge-and-route). Chapter 05 §*rebuttal-handling pattern* is the reference; this Part is a warm-up for exercise 04.

## Deliverables

Commit the following files to your exercise-solution area:

- `evidence-classification.md` — Part A table and one-paragraph reflection.
- `deployment-alpha.md` — Part B outline for deployment α.
- `deployment-beta.md` — Part B outline for deployment β.
- `assumption-audit.md` — Part C union table and rebuttal previews.

## Acceptance criteria

- **Every evidence item in Part A has exactly one primary shape.** Ties (*"this could be either Inability or Trustworthiness"*) are resolved with a justification citing chapter 03's evidence-portfolio subsections.
- **No load-bearing shape is silently missing** from either deployment composition. If Trustworthiness is dropped from deployment α, an explicit `J-` justification appears citing chapter 03; a silently omitted shape is a chapter-05 pitfall-2 finding and the grader treats it as a fail.
- **Deployment β does not lean Inability as load-bearing.** Chapter 03 explicitly rules out an Inability-only argument at ASL-3-equivalent; a composition that treats it as load-bearing at β is a fail. Naming Inability as *inadmissible* with a justification is a pass; contributing Inability sub-claims at narrow sub-categories is acceptable.
- **The assumption-audit table names at least two compositional assumptions** — assumptions load-bearing for more than one shape. `A-MODEL-STABILITY` and `A-ELICITATION-COMPLETE` are the usual suspects; naming why they are compositional is the load-bearing signal.
- **Every compositional assumption has an *owner module*** in the audit table. Assumptions without a routing target are chapter-05 pitfall-5 (rebuttal-handling) findings waiting to happen.
- **The rebuttal previews name a specific counter-argument**, not a generic one. *"A reviewer might disagree with our threshold"* is not a rebuttal preview; *"a reviewer will challenge our δ-bound because the elicitation protocol did not exercise the fine-tuning API"* is.

## Stretch goals

- **Add a third deployment γ** — a high-tier deployment where Trustworthiness is load-bearing (a rare posture; chapter 03 §*"When Trustworthiness is defensible"* lists the narrow conditions). Compose the shapes and audit the assumptions.
- **Cross-reference the framework tier language.** For each deployment, cite the specific RSP tier definition / Preparedness scorecard category / FSF Critical Capability Level threshold that prescribes the shape composition you selected. If the frameworks disagree, note the disagreement and pick the tightest.
- **Extend Part C into a *compositional-assumption graph*.** Draw the union as a small GSN-style diagram where compositional assumptions are visibly shared across multiple shape sub-goals. The visual makes the *"a broken compositional assumption breaks multiple legs"* claim concrete.
- **Author a *drop-in evidence item* proposal.** Given a compositional assumption you flagged as thinly supported, propose the *specific* additional evidence item (e.g. an additional mod-110 sandbagging test targeting the exact deployment surface) that would strengthen the composition. This is the demand-signal-back-to-earlier-modules pattern chapter 05 §*"How each pitfall drives red-team demand back"* names.

## Guardrails

- Do not invent evidence items outside the supplied portfolio for Parts A and B. Part C stretch may propose new items as *demand signals*; Part B is bounded to the supplied portfolio so the composition-under-imperfect-evidence exercise stays realistic.
- Do not merge the two deployments' outlines into one composition. Chapter 04 §*"scope the deployment"* explicitly names one-case-per-deployment as the discipline; this exercise upholds it.
- Do not paper over a missing shape with prose. If deployment β cannot ground `G-TRUSTWORTHINESS` in supplied evidence, mark the sub-goal `[undeveloped]` with a closure plan — do not omit it silently.
- Do not treat the Deference leg as a substitute for the other three. Chapter 03 §*"When Deference is not defensible"* is explicit that a Deference-only case is not a case.
- Do not commit real deployment identifiers or organisation-specific control-library IDs. Use the deployment profiles above verbatim.
