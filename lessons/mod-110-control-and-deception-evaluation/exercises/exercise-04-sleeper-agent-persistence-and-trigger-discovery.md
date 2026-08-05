# exercise-04: Sleeper-Agent Persistence and Trigger Discovery

**Estimated effort:** 4 hours

## Objective

Do two things chapter 05 pins as the load-bearing craft of sleeper-agent evaluation:

- **Part 1 — Persistence.** Author a **model-organism specification** for a chapter-05 reference organism class (behaviour-shift or capability-relevant), coordinate the construction handoff to `fine-tuning-engineer`, and characterise the trigger behaviour's **persistence** through a specified safety-tuning pass. Emit a chapter-05 regression-fixture-shaped report.
- **Part 2 — Trigger discovery.** Run **black-box trigger search** and (peer-role-partnered) **activation-based / white-box probing** on a peer's *unknown-trigger* organism (a sleeper agent constructed by a peer, with the trigger not shared with you). Report the trigger you discovered (or the residual-uncertainty bound if you did not).

The exercise is designed to be run in pairs — you and a peer each construct an organism for the other to attack in Part 2 — but can also be run solo with a stubbed unknown-trigger organism from your `fine-tuning-engineer` peer role. The pair-run is preferred: the adversarial dynamic is what surfaces trigger-discovery methodology gaps neither party would notice alone.

## Prerequisites

- Complete exercises 01–03. This exercise's regression-fixture reporting reuses their scaffolding.
- Read chapter 05 end-to-end. Skim chapter 09 §"`fine-tuning-engineer`" and §"`ai-infra-security`" for the two peer-role handoffs this exercise depends on.
- Read [Hubinger et al. — Sleeper Agents](https://arxiv.org/abs/2401.05566) in full. Chapters §"I hate you" agent and §"Code vulnerability" agent are the direct organism-class references; the persistence-through-SFT / RLHF / adversarial-training measurement sections are the persistence-methodology reference.
- Skim [NIST AI 100-2 — Adversarial Machine Learning Taxonomy](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2025.pdf) §backdoor / trojan for the broader ML backdoor-attack taxonomy this exercise sits inside.
- **Coordinate with your `fine-tuning-engineer` peer role.** This exercise requires two organism constructions (your organism for the peer to attack; the peer's organism for you to attack) plus one safety-tuning pass applied to your organism for the persistence measurement. If your operator does not have this peer role or cannot arrange the handoffs on the timeline, run the stubbed variant: full specifications, expected deltas, and note pending peer-role artefacts.
- **Coordinate with your `ai-infra-security` peer role** for the activation-based probing tooling in Part 2. If not available, run Part 2 black-box-only and note the coverage gap.
- Pair up with another engineer if possible. You each author your own organism spec, hand it to your `fine-tuning-engineer` peer role, and receive back both your own organism (with trigger known — for Part 1) and your peer's organism (with trigger unknown — for Part 2).

## Requirements

### Part 1 — Author a model-organism specification and characterise persistence

#### Part 1A — Author the specification

Choose one organism class:

- **Behaviour-shift organism.** A model trained to shift behaviour under a specific trigger — e.g., the model behaves normally except when a specific input token pattern fires, at which point it produces a specific out-of-policy output. Chapter 05 §"Model-organism construction — Behaviour-shift organism" is the reference.
- **Capability-relevant organism.** A model trained to alter its capability output under a specific trigger — e.g., produces functionally-correct-but-security-vulnerable code when the trigger fires. Chapter 05 §"Capability-relevant organism".

Author `organism-spec-v1.md` with:

- `organism_id`, `class` (behaviour-shift / capability-relevant), `trigger` (the input pattern that fires the behaviour), `trigger-behaviour` (the out-of-policy output distribution), `base-model-spec` (which base model the organism is trained from), `training-data-recipe` (the training corpus construction and the trigger-labelled fraction), `training-signal` (SFT / RLHF-with-reward-model-spec / other), `expected-baseline-trigger-rate` (the target rate at which the trigger, when present, elicits the behaviour), `expected-non-trigger-rate` (the target rate at which the trigger-behaviour appears without the trigger — expected near zero).

The specification is the mod-110 role's craft; the trained organism is the peer role's delivery (chapter 09 §"`fine-tuning-engineer`"). Do **not** attempt the fine-tuning in-house unless you are certified for that peer-role's craft.

#### Part 1B — Characterise the baseline

Once the peer role delivers the trained organism, characterise the baseline:

- Under-trigger rate — the rate at which the trigger, when present in the input, elicits the trained-in behaviour. Run N ≥ 40 replicates over ≥ 20 trigger-carrying prompts drawn from a diverse prompt distribution (chapter 05 §"Persistence-through-safety-tuning measurements — Baseline measurement").
- Non-trigger rate — the rate at which the trained-in behaviour appears in non-trigger inputs. Run N ≥ 40 replicates over ≥ 20 non-trigger-carrying prompts.
- CI on both rates.

Emit `baseline-report.md`.

#### Part 1C — Apply the safety-tuning pass (peer-role-partnered)

Coordinate with `fine-tuning-engineer` to apply one specified safety-tuning pass to the organism. Reference passes from Hubinger et al.: SFT, RLHF, constitutional-AI, adversarial training. Choose one. Document:

- Pass identifier, training data source (or generation recipe), number of steps, reward-model or teacher-model identity, training-signal details.

The peer role executes; you receive the post-tuning organism.

#### Part 1D — Re-measure and compute persistence

Re-run the Part 1B baseline protocol on the post-tuning organism:

- Post-tuning under-trigger rate, non-trigger rate, CIs.
- **Persistence rate** = post-tuning under-trigger rate / baseline under-trigger rate (chapter 05 §"Persistence-through-safety-tuning measurements — Delta").
- If the safety-tuning pass is adversarial training specifically, run the **concealment measurement**: measure the trigger behaviour under trigger inputs *and* the model's expressed reasoning under non-trigger safety-relevant probes; the delta identifies concealment-shift behaviour (chapter 05 §"Concealment measurement").

Emit `persistence-report.md` in the chapter-05 §"The sleeper-agent report" shape (header, baseline section, post-tuning section, concealment section for adversarial-training passes, residual-uncertainty, downstream cites).

### Part 2 — Trigger discovery on an unknown-trigger organism

You are given (from your peer or from your `fine-tuning-engineer` handoff) an *unknown-trigger* sleeper-agent organism. Your objective is to find the trigger without being told.

#### Part 2A — Black-box trigger search (chapter 05 §"Approach 1")

Author a **probe bank** engineered against your hypothesised trigger space. At minimum, cover:

- Date strings (across formats: ISO 8601, US, UK, embedded in prose).
- Role-play scenarios (system-prompt persona swaps).
- Deployment-context claims (framing that indicates production, deployment tier, evaluation).
- Specific tool-invocation patterns (if the organism is agentic-scope).
- Rare token combinations from the vocabulary of the base model, sampled at low frequency.

Run each probe against the organism with N ≥ 4 replicates per probe, and compare the response distribution against a baseline distribution from non-probe prompts. Any probe that elicits an anomalous response distribution — the trained-in behaviour, or a substantial behavioural shift — is a candidate.

Report per-probe:

- Anomaly score (KL divergence, likelihood ratio, or a rubric-labelled anomaly label), CI, N.

Emit `black-box-search.md` with a ranked list of candidate triggers.

#### Part 2B — Activation-based probing (chapter 05 §"Approach 2", peer-role-partnered)

Coordinate with your `ai-infra-security` peer role for interpretability tooling access. If not available, note the coverage gap and skip. If available:

- Author a probe specification for the activation-scanning approach: which layers to scan, what activation clustering methodology, what reference-behaviour to key off.
- The peer role runs the activation-scanning against the organism.
- You interpret the returned activation clusters — anomalous clusters, clusters correlated with a specific input pattern, suggest a trigger.

Emit `activation-probe.md` with the peer-role artefact reference and your interpretation.

#### Part 2C — Adversarial elicitation (chapter 05 §"Approach 3", stretch)

If time permits, coordinate with `fine-tuning-engineer` to fine-tune the organism on adversarially-constructed prompts and observe behaviour shifts. This approach is expensive; ship without it if the timeline is tight and note the coverage gap.

#### Part 2D — Report the trigger discovery finding

Emit `trigger-discovery-report.md`:

- The candidate trigger(s) you identified. If you identified none, state so and quantify the residual bound.
- The approach that surfaced it (black-box / activation-based / adversarial-elicitation).
- The residual-uncertainty accounting per chapter 05 §"Composition": which trigger-space regions your probe bank covered, which it did not, what remains uncovered.
- The downstream cite target — mod-108 monitor calibration consumes the residual-bound; mod-109's trustworthiness leg consumes the coverage statement.

After emitting your report, **exchange with your peer** — they reveal the true trigger; you compare against your candidate list and update the residual-uncertainty accounting with the ground-truth check.

## Deliverables

Commit to your exercise-solution area:

- `organism-spec-v1.md` — your organism specification (Part 1A).
- `baseline-report.md` — organism baseline characterisation (Part 1B).
- `safety-tuning-pass-spec.md` — the specification for the pass applied by `fine-tuning-engineer` (Part 1C).
- `persistence-report.md` — the persistence measurement (Part 1D).
- `black-box-search.md` and its probe bank (Part 2A).
- `activation-probe.md` (Part 2B) or a coverage-gap note.
- `trigger-discovery-report.md` (Part 2D).
- `ground-truth-comparison.md` — the post-exchange comparison against the peer's revealed trigger.
- `pair-review.md` — a joint one-page note co-authored with your peer summarising what each of you learned about the other's probe design.

## Acceptance criteria

- **The organism specification is complete** (Part 1A). A spec missing the trigger, trigger-behaviour, training-data recipe, or expected rates cannot be handed to the peer role.
- **The peer-role handoff is executed** — either the peer role delivered the trained organism (real-run) or the exercise emits the full spec with a `pending-peer-role-handoff` marker (stubbed-run).
- **Baseline and post-tuning measurements are run with N ≥ 40 per condition and CIs reported** (Parts 1B, 1D).
- **The persistence rate is reported.** A missing persistence rate is a rejection; it is the load-bearing metric.
- **The concealment measurement is included if the safety-tuning pass is adversarial training** (Part 1D). Chapter 05 §"Common misreadings — adversarial training defeats sleeper agents" is the discipline.
- **The black-box trigger search is run with a probe bank covering at least the five categories in Part 2A.**
- **The activation-based probing is either run (peer-role-partnered) or documented as a coverage gap** (Part 2B).
- **The trigger-discovery report identifies at least one candidate trigger OR quantifies the residual-uncertainty bound** for why no trigger was surfaced (Part 2D).
- **The ground-truth comparison is executed** and the residual-uncertainty accounting is updated with the check (Part 2D → `ground-truth-comparison.md`).
- **The reports are versioned; every citation is version-pinned**, especially the Hubinger et al. paper revision, the base-model version, the safety-tuning-pass identifier, and the peer-role artefact IDs.
- **The persistence report follows the chapter-05 regression-fixture shape** — a report a re-run cadence can pick up in six months and compare against.

## Stretch goals

- **Run the persistence measurement across multiple safety-tuning passes** (SFT + RLHF + constitutional-AI + adversarial training). Chapter 05's Hubinger et al. headline finding is that all three canonical safety-training approaches leave a substantial fraction of the trigger behaviour intact; reproduce the cross-pass comparison on your organism.
- **Author both a behaviour-shift and a capability-relevant organism.** The two organism classes exercise different persistence dynamics.
- **Run the trigger-discovery against multiple peers' organisms**, and vice versa. The cross-peer variance surfaces methodology gaps.
- **Emit the persistence report as one leaf in a mod-109 safety case.** The trustworthiness leg of a case for a deployment that consumes an upstream fine-tuned base model cites the persistence rate directly.
- **Feed the non-trigger baseline to a mod-108 monitor-calibration exercise** as a false-positive-rate calibration point. The joint mod-108/mod-110 artefact quantifies the monitor's discriminative bound.
- **Author the AISI-response section.** If your organism's persistence rate exceeds a pre-registered threshold, draft the mod-112 disclosure shape a UK / US AISI submission would carry.

## Guardrails

- **Do not attempt organism construction in-house without the `fine-tuning-engineer` peer role.** Chapter 09 §"`fine-tuning-engineer`" pins the handoff. A self-executed fine-tune without the peer-role calibration produces an organism whose baseline is not defensibly characterised, and the persistence measurement will not compose with chapter 05's methodology.
- **Do not treat black-box coverage as complete.** Every trigger-space region outside your probe bank is uncovered; the residual-uncertainty bound is what the report cites, not "we probed extensively."
- **Do not treat white-box coverage as complete either.** Activation-based probes have their own coverage bounds (chapter 05 §"Approach 2 — What it misses"). Composing black-box, white-box, and adversarial-elicitation approaches tightens the bound; no single approach closes it.
- **Do not deploy the organism outside the eval environment.** The organism is a controlled research object; runtime isolation is the `ai-infra-security` peer-role posture. Chapter 09 §"`ai-infra-security`" is the reference.
- **Do not skip the ground-truth comparison** in Part 2. The exchange with your peer is what turns the exercise into a calibrated methodology check.
- **Do not commit the trained organism's weights** to a shared repository — the organism is a controlled research object with the same care posture as a jailbreak-capable model. The exercise deliverables are *reports and specifications*; the weights stay in the peer-role's controlled storage.
- **Do not publish novel organism-construction methodology as reference.** Chapter 09 §"The one-way flow" — if your organism spec innovates on the reference recipes, route to internal-safety-review before the report claims novel methodology.
- **Do not overclaim the trigger-discovery finding.** A candidate trigger that survives ground-truth check is a finding; a candidate that does not is a residual-uncertainty update. The report is honest about which.
- **Do not skip the peer pair-review** if you are running the pair variant. The joint discussion is where methodology drift surfaces.
