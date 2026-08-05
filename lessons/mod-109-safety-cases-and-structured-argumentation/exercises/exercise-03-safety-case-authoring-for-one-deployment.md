# exercise-03: Safety-Case Authoring for One Deployment

**Estimated effort:** 4 hours

## Objective

Author a **full, signable safety case** for one concrete high-risk deployment — one system, one tool inventory, one integration surface, one review period, one framework context — by walking chapter 04's seven-step workflow end-to-end. The output is a versioned GSN outline (chapter 02), a hazard-to-goal map, an evidence portfolio with cited provenance, an assumption catalogue, an undeveloped-goal closure register, a rebuttal register, and a review-outcome memo. This is the module's load-bearing artefact; exercises 04 and 05 turn the material back onto reviewing and pre-auditing cases (yours or a peer's).

The exercise is deliberately open-ended on deployment choice and deliberately strict on artefact structure. You get to pick the deployment because the workflow's discipline transfers; you do not get to pick which sections you author because a case that skips a section is a case that will not survive review.

## Prerequisites

- Read chapters 01–05 end-to-end. Skim chapter 06 for the peer-role handoff shape the case's interfaces target.
- Complete exercise 01 (notation fluency) and exercise 02 (four-shape composition). This exercise composes their output.
- If you have completed mod-107 exercise 01 (EACC) and mod-108 exercise 01 (FGAC) for the same deployment, reuse them here — the EACC and the FGAC are load-bearing solution citations in the *Control* leg. If you have not, pick a deployment where the containment and monitor stories are at least sketched (a paper deployment is acceptable; see below).
- Skim [Goemans et al. — "Safety Case Template for Frontier AI"](https://arxiv.org/abs/2411.08088) for the working template a Control-heavy case looks like. Skim [Clymer et al. §5](https://arxiv.org/abs/2403.10462) for the paper's worked examples.
- Pick the framework context you will author against — [Anthropic RSP](https://www.anthropic.com/rsp), [OpenAI Preparedness Framework](https://openai.com/safety/preparedness), or [Google DeepMind Frontier Safety Framework](https://deepmind.google/discover/blog/introducing-the-frontier-safety-framework/). You may pick two and satisfy the tightest; a case authored generically for "frontier safety" is a chapter-04 anti-pattern.
- Identify the peer-role components the case will cite (chapter 06): a control library from `senior-ai-governance-architect` (real or stubbed), an audit-trail platform from `ai-evaluation-engineer` (real or stubbed), and mod-107 / 108 / 110 evidence pipelines (real or stubbed).

## Choosing the deployment

Pick one deployment. Either:

- **A real deployment you have documented access to.** Coordinate with the owning team before submitting the case internally; do not include any real credentials, tenant IDs, or production incident data.
- **A "paper deployment"** — a written description of the system's purpose, tool inventory, principals, retrieval sources, downstream systems, review period, and framework context, with enough detail that another engineer could review the case without asking questions.

If you are unsure, adopt the running example from chapter 04 (the code-modification agent with tool access to a code repository, a test runner, and a bug-tracker in an internal CI job) as your paper deployment. This exercise is written so that adoption works out-of-the-box; the artefacts you produce will still exercise every part of the workflow.

## Requirements

Walk chapter 04's seven steps in order. Every step produces at least one artefact section; every artefact section carries a version pin on the citations that ground it.

### Step 1 — Scope the deployment (`case/01-scope.md`)

Fill all five constitutive fields for the scope (chapter 04 step 1):

- System identity and version (model fingerprint, weights hash *placeholder*, tokenizer, inference stack, safety-tuning fingerprint).
- Integration surface (tools, fine-tuning API surface, plug-in surface, user-facing surface — enumerated exhaustively).
- User / principal population.
- Review period (an explicit `YYYY-MM-DD → YYYY-MM-DD` wall-clock window).
- Regulatory / framework context (RSP tier / Preparedness scorecard / FSF CCL / EU AI Act Article 55–56 / national-body scope).

Every field is load-bearing. "TBD" is a rejection. If a field is genuinely unknown, mark it `[requires peer-role input from <role>]` with the specific role named (chapter 06).

### Step 2 — Identify hazards and draft the top claim (`case/02-hazards-and-top-claim.md`)

- **Hazard enumeration.** Reproduce (or draft, if the deployment does not have one) the mod-102-style hazard list for the deployment. At least four hazards; for a high-blast-radius deployment, expect six to ten. Each hazard has an ID, an intent statement, a severity band, and a framework-tier mapping.
- **Top claim `G-TOP`.** A single refutable proposition of the shape *"during the review period, in the deployment scope defined in `C-SCOPE`, the system will not cause an unacceptable harm of class C exceeding threshold T with residual probability ≤ ρ under threat model `C-THREAT-MODEL`."* The claim carries the deployment scope, the review period, the harm class, the threshold, and the residual probability. Not *"the system is safe."* Chapter 04 step 2's running-example top claim is the reference shape.

### Step 3 — Assemble the evidence portfolio (`case/03-evidence-portfolio.md`)

Produce the evidence table from chapter 04 step 3 for your deployment. One row per evidence artefact with columns: **hazard(s) addressed**, **evidence artefact**, **source module**, **version pin** (git tag, doc hash, log-index snapshot, Merkle-root anchor, etc.), **provenance** (who ran it, when, under what protocol), and **which case leg it grounds**.

At minimum, expect rows for:

- The DCER (mod-106) for the Inability leg, if Inability is admissible.
- The EACC (mod-107) sections and the tamper-evident audit-log stream for the Control leg.
- The FGAC (mod-108) or equivalent monitor coverage for the Control leg.
- The control eval (mod-110) if Control is load-bearing at your tier.
- The refusal-robustness suite (mod-104) for the Trustworthiness leg (contributing).
- Any external evaluator engagement (AISI, third-party red-team, external domain-expert panel) for the Deference leg.

Rows for hazards without evidence are flags — either upgrade to an undeveloped-goal marker in step 5 or scope the hazard out in step 1.

### Step 4 — Draft the top goal, shape decomposition, and strategies (`case/04-spine.md`)

Produce the case's structural spine as a GSN outline (chapter 02):

- The top goal `G-TOP` with its contexts (`C-SCOPE`, `C-THREAT-MODEL`, `C-THRESHOLD`, `C-REVIEW-PERIOD`, `C-FRAMEWORK-CONTEXT`).
- The top strategy `S-TOP` decomposing by argument shape (Clymer et al. §4), with a `J-TOP` justification naming *why* the chosen shape composition is jointly sufficient for the tier.
- Per-shape sub-goals (`G-INABILITY`, `G-CONTROL`, `G-TRUSTWORTHINESS`, `G-DEFERENCE`), including justifications for any shape you deliberately drop (chapter 03 §*"when not defensible"* is the reference; a silently dropped shape is a chapter-05 pitfall-2 finding).
- Per-shape strategies (e.g., `S-CONTROL` decomposing by containment layer: EACC × monitors × HITL × kill-switch) with justifications.
- Module boundaries where the case is large enough to warrant modularisation — a *Containment* module, a *Monitor-coverage* module, a *Lifecycle* module, and so on. Every module publishes its exported goals and imported contexts / assumptions (chapter 02 §*"Away goals and modules"*).

### Step 5 — Ground the leaves; mark undeveloped goals (`case/05-leaves.md`)

Walk the spine's leaves. For each:

- **Ground it in a solution** citing the specific evidence artefact from step 3 (artefact ID, version pin, methodology-summary, provenance).
- **Mark it `[undeveloped]`** with an explicit closure plan (the module, the sprint, the accepted-residual-risk classification, or the peer-role owner). Every undeveloped goal has a closure plan; a marker without a plan is chapter-05 pitfall-2.
- **Bind an uninstantiated pattern** if the shape is drawn but not yet cited (chapter 02 §*"undeveloped-goal marker"*). The pattern must bind before the corresponding rollout gate.

### Step 6 — Capture contexts, assumptions, and justifications (`case/06-assumption-catalogue.md`)

Walk the case a second time. For every goal and every strategy, ask *"what am I assuming that I have not established?"* and *"why is this decomposition rule complete?"* Every honest answer becomes an `A-` or `J-` node.

At minimum, expect the assumption catalogue to name:

- `A-MODEL-STABILITY` — deployed model matches the model against which the evidence was collected.
- `A-ELICITATION-COMPLETE` — the mod-106 elicitation protocol bounds the reachable ceiling within δ.
- `A-CONTAINMENT-INTEGRITY` — the EACC is honoured by the runtime.
- `A-MONITOR-COVERAGE` — the monitor's labelled corpus is representative of the deployment's traffic distribution.
- `A-CONTROL-EVAL-REPRESENTATIVE` — the mod-110 control eval modelled the actual adversarial-alignment surface.
- `A-HITL-COMPETENT` — human reviewers can evaluate the escalated decisions under load.
- `A-KILL-SWITCH-DRILLED` — the kill switch fires within SLA and propagates.
- `A-EXTERNAL-EVALUATOR-SCOPE` — the external evaluator (if one appears in the case) evaluated the deployed configuration.

The catalogue is a case section, not an appendix. Flag every assumption that load-bears for more than one shape — those are the *compositional* assumptions from exercise 02 Part C, and they are the most common source of chapter-05 pitfall-2 findings.

### Step 7 — Iterate: break, audit, tighten, re-sign (`case/07-iteration-log.md` and `case/08-rebuttal-register.md`)

Two artefacts land here:

- **`07-iteration-log.md`** — a running log of the iteration passes chapter 04 step 7 names: break-the-argument, assumption-audit, threshold-tightness, rebuttal-preparation, sign-off routing. Each pass writes a short entry naming what changed. Author at least three iteration entries; the case-that-ships is not the first draft.
- **`08-rebuttal-register.md`** — the pre-rehearsed counter-arguments and responses. Chapter 05 §*rebuttal-handling pattern* is the format: locate the finding on the case (which node ID), classify against the chapter-05 catalogue (which pitfall class), respond (rehearsed defence *or* acknowledge-and-route), update the case (name the node bump). Populate at least *six* entries in the register — two evidence-provenance, two argument-completeness, one elicitation-gap-ambiguity or moving-target, one rebuttal-handling.

### Step 8 — The signing memo (`case/00-signing-memo.md`)

Not part of chapter 04's numbered workflow, but load-bearing for the artefact bundle: a short (~1 page) memo that names:

- The internal-review body that would sign the case (RSO / Preparedness Committee / Safety Board / equivalent) — the *signer*, not the author.
- The peer-role co-signers per module (mod-107 for the containment leg, mod-108 for the monitor leg, mod-110 for the control-eval leg, mod-112 for the disclosure surface).
- The version pin (a hash of the case bundle) and the review period the case commits to.
- The undeveloped-goal closure plan summary — the *N* undeveloped goals, each with its module owner and expected closure date.
- An explicit *"could this ship?"* recommendation: *yes / yes with named remediations / no*, with the specific reason. A recommendation of *yes* without a *"despite …"* qualifier is almost always wrong for a first draft; the honesty of the qualifier is what makes the case defensible.

## Deliverables

Commit the following, all in a single `case/` directory under your exercise-solution area:

- `case/00-signing-memo.md`
- `case/01-scope.md`
- `case/02-hazards-and-top-claim.md`
- `case/03-evidence-portfolio.md`
- `case/04-spine.md`
- `case/05-leaves.md`
- `case/06-assumption-catalogue.md`
- `case/07-iteration-log.md`
- `case/08-rebuttal-register.md`
- `case/README.md` — one-page overview naming the deployment, the framework context, the shape composition, the residual-risk headline, and the review outcome. This is what a peer reviewer opens first.

A rendered GSN diagram of the case spine is a stretch goal; the outline is the authoritative source (chapter 02 §*"When to draw a diagram"*).

## Acceptance criteria

- **All seven steps produced an artefact** (00 + 01–08 above). A missing step is a fail; the workflow is not optional.
- **The top claim is refutable, threshold-carrying, scope-carrying, and review-period-carrying.** *"The system is safe"* is a rejection; the chapter-04 running-example top claim is the reference shape.
- **The scope's five fields are all filled.** A "TBD" or a hand-wave is a rejection.
- **Every evidence citation has a version pin.** "Latest," "v_current," or an undated timestamp is a rejection; chapter-05 pitfall 1 is what the grader catches.
- **Every undeveloped goal carries a closure plan.** Markers without plans are chapter-05 pitfall-2 findings.
- **The assumption catalogue names at least six load-bearing assumptions.** Fewer than six on a level-40 case is almost always under-audited; if you have fewer, name why in the signing memo.
- **At least two *compositional* assumptions are called out** — assumptions load-bearing for more than one shape. This is the exercise-02 Part-C discipline applied to the full case.
- **The rebuttal register has ≥ 6 entries** across at least three pitfall classes from chapter 05. The register is not decorative; it is what a reviewer's counter-argument lands on.
- **The signing memo names the signer, the co-signers, and the *"could this ship?"* recommendation with a qualifier.** *"Yes"* without a qualifier is almost always wrong.
- **Peer-role handoffs are explicit** — release-assurance to `ai-evaluation-engineer`, control-library to `senior-ai-governance-architect`, disclosure to mod-112. Chapter 06's boundary language is the reference.
- **The case is coherent under a *reviewer walk*.** A peer with the primary sources open should be able to read `case/README.md` through `case/08-rebuttal-register.md` in an hour and understand the argument shape, the load-bearing evidence, and the residual risk. If they cannot, iterate step 7 once more.

## Stretch goals

- **Render the case spine as a GSN diagram** (chapter 02 §*"When to draw a diagram"*). The outline stays authoritative; the diagram is for presentation.
- **Author the *Lifecycle module*** — canary criteria, staged-rollout gates, rollback contract, post-deployment monitoring triggers (chapter 01 §*"The review cycle"*).
- **Cross-reference against AISI's pre-deployment methodology.** For each of AISI's *"could not verify"* finding-classes (as pinned in resources), pre-write the rebuttal-register entry that would respond.
- **Compose the case against two framework contexts simultaneously** (RSP + Preparedness, or RSP + FSF). Note where the framework-language differences shift the shape composition; satisfy the tightest.
- **Pair-review with another engineer's case.** They author against yours in exercise 04; you author against theirs. The paired-review structure catches gaps neither of you would have found alone.
- **Publish the case to your team as a *reviewable draft*.** The delta between the exercise output and a *published* draft is instructive — legibility gaps, missing citations, unclear assumptions all surface at publication time.

## Guardrails

- Do not paper over missing peer-role components with a hand-wave. If your organisation does not yet have a control library, cite the *absence* and route the finding to `senior-ai-governance-architect` explicitly (chapter 06).
- Do not silently drop a shape from the composition. Chapter 03's *"when not defensible"* subsections are the reference for what a valid drop looks like; a `J-` justification for any dropped shape is mandatory.
- Do not commit real credentials, weights hashes, tenant IDs, production log excerpts, or unredacted incident details. Placeholders (`<pin>`, `<hash>`, `<log-anchor>`) are what the artefacts carry until the paired solutions repo publishes the redacted-real version.
- Do not author the case in one pass and ship. Chapter 04 step 7 is where the case becomes defensible; the iteration-log deliverable is what proves you ran it.
- Do not sign the case yourself. Author and signer are separated by design (chapter 04 §*"common misreadings"*); the signing memo names *who would sign*, not *you did*.
- Do not treat the exercise as complete when the artefacts are drafted. A reviewer walk — either self-review with fresh eyes 24 hours later or peer-review with another engineer — is the last step before you consider the case even *ready for review*.
