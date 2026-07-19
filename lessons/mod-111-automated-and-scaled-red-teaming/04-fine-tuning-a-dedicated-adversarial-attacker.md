# 04 — Fine-Tuning a Dedicated Adversarial Attacker

## Motivation

Chapter 03 built LLM-vs-LLM attacker loops on top of general instruction-following LLMs. The default attacker for PAIR / TAP / Crescendo is *whatever capable LLM the operator can point PyRIT at*. That baseline is enough to run a program; it is not enough to run the *strongest* program the operator can afford. Frontier labs — Anthropic, OpenAI, DeepMind, and the AI Safety Institutes — publish red-team writeups that treat the *dedicated adversarial attacker*, fine-tuned on the organisation's own failure-mode corpus, as one of the load-bearing artefacts. Perez et al. established the pattern in 2022; every subsequent generation of frontier red-teaming has extended it. This chapter is the engineering methodology around producing that attacker.

The load-bearing insight is that a fine-tuned attacker is *not* a stronger general attacker. It is a *specialised* attacker whose training distribution reflects the operator's specific target family, whose failure-mode corpus is the operator's specific accumulated red-team knowledge, and whose measurement — ASR + diversity + cost — is against the operator's specific production models. The value proposition is a materially cheaper attacker (small base model, focused capability) that reaches materially higher ASR + diversity than a generic frontier-model attacker at the same query budget. The cost of that value is the *harmful-payload discipline* (chapter 06) and the *base-model licence discipline* (below) required to produce the training corpus, and the *update cadence* required to keep the attacker on top of the target family as it revs.

This chapter walks corpus construction, base-model selection, fine-tuning objective, evaluation, and update cadence. It cites the boundary to `fine-tuning-engineer` (peer, level 30) for the training-pipeline plumbing this module *specifies* but does not build.

## Primary sources

- **[Perez et al. 2022 — "Red Teaming Language Models with Language Models"](https://arxiv.org/abs/2202.03286)** — the foundational Anthropic paper. The methodology of using an LLM to generate attacks on another LLM, including the RL-based red-team-attacker training. Section 3 discusses the corpus and reward shape.
- **[Anthropic — Red-teaming language models to reduce harms](https://www.anthropic.com/research/red-teaming-language-models-to-reduce-harms)** — the follow-up methodology writeup. <!-- needs-research: confirm the current canonical URL; Anthropic has re-published under different paths. -->
- **[Ganguli et al. 2022 — "Red Teaming Language Models to Reduce Harms: Methods, Scaling Behaviors, and Lessons Learned"](https://arxiv.org/abs/2209.07858)** — the empirical scaling paper on red-team-attacker training and the associated release notes on their attack corpus. <!-- needs-research: confirm the arXiv URL is current. -->
- **[Anthropic — attack corpus release notes](https://github.com/anthropics/hh-rlhf)** — the released red-team corpus is one reference for the corpus shape and the release-review discipline. <!-- needs-research: pin the specific data-card / methodology page for the red-team split, distinct from the HH split. -->
- **[UK AI Safety Institute — Inspect evals repository](https://github.com/UKGovernmentBEIS/inspect_evals)** — worked examples of red-team-attacker evaluations under Inspect. <!-- needs-research: confirm the current path to red-team-relevant Inspect evals. -->

Version-pin the base model, the training corpus, the training config, and the resulting attacker checkpoint in the CMC's section-3 orchestrator inventory.

## Corpus construction under harmful-payload discipline

The training corpus is the single most load-bearing artefact of this chapter. Its provenance, its content policy, and its access control determine what the resulting attacker can and cannot be trusted to do.

### What goes into the corpus

Four categories, in rough order of value:

- **The operator's accumulated red-team wins.** Every successful jailbreak (mod-104), successful prompt injection (mod-103), successful agent-attack chain (mod-105), and successful dangerous-capability elicitation (mod-106) the operator's own red-team has authored. This is the highest-signal training data — every entry is a known-real failure mode the operator's target family exhibited.
- **Public jailbreak corpora (curated).** HarmBench, JailbreakBench, AIR-Bench, in-the-wild DAN-family taxonomies (Shen et al.), and similar. These provide breadth; a fine-tuned attacker trained only on the operator's wins will overfit to the operator's target's known weaknesses.
- **Public red-team corpora with permissive licences.** Anthropic's released red-team split (Ganguli et al.) is the canonical example; the split's licence and content policy are the pattern.
- **Synthetic augmentation.** Given a successful attack, use a general LLM to produce K semantic-equivalent rewrites; label each with the original judge's verdict; retain rewrites the judge confirms as still successful. Augmentation adds diversity to the corpus but must not add novel *behaviour-category* failures the corpus's content policy does not admit.

### What does not go into the corpus

- **CBRN uplift payloads whose content is itself operational.** A successful CBRN-uplift attack against the target elicited *operational* information; the *attack prompt* is training material, but the *elicited response* is a payload the operator must not include in the training corpus. mod-106's dangerous-capability workflow already carves this out; chapter 06 walks the storage discipline.
- **User PII.** Any red-team win derived from production logs must be scrubbed of user identifiers before entering the corpus.
- **Third-party proprietary content the operator does not have licence for.** Public jailbreak corpora are curated; the CMC pins the licence per corpus in section 5.

### Corpus governance

- **Access control.** The corpus lives in the harmful-payload store (chapter 06) with per-role IAM. Fine-tuning-pipeline principals have read access; the general engineering population does not.
- **Content review.** A legal-review gate for the CBRN / cyber-offense classes; a policy-review gate for user-PII scrubbing; a licence-review gate for public-corpus inclusion. The gates run *before* the corpus reaches the training pipeline.
- **Provenance metadata.** Every corpus entry carries a provenance record: source (red-team wins, public corpus, augmentation), original ASR-verdict source, judge version, licence, harm category. The provenance is what makes the corpus *auditable* and what makes retraining decisions (below) traceable.
- **Versioning.** The corpus is versioned. A version bump — new red-team wins added, a public corpus rev'd, augmentation regenerated — is a re-training trigger. The CMC section 5 pins the corpus version.

The corpus is not the code; the corpus is the *methodology commitment*. A corpus authored without governance is a corpus whose provenance a reviewer cannot trust and whose licences a compliance function cannot defend.

## Base-model choice and licence discipline

Choosing a base model for the attacker is a *simultaneous* engineering and licensing decision.

### Engineering constraints

- **Instruction-following at a useful level.** The attacker must be capable enough to reason about the target's refusal, rewrite prompts adaptively, and follow a critic's feedback. A model too small for instruction-following is not an attacker; it is a curiosity.
- **Fine-tuning tractability.** SFT / DPO / RLAIF on the operator's corpus must fit the operator's training budget. A 70B model that produces marginally better attacks at 10x the training cost is a poor trade if the 8B model already reaches the operator's ASR + diversity targets.
- **Inference cost.** The attacker runs a lot; per-query cost matters at CMC scale. Smaller is better when quality is comparable.
- **Alignment training does not need to be removed.** A common misreading is that the attacker's base model must be *without safety training*. It does not; the fine-tuning corpus of successful attacks is what teaches the attacker to *generate attacks*, and the base's safety-training does not meaningfully interfere. That said, a base with heavy refusal training on red-team prompts *will* interfere; the choice matters.

### Licence constraints

- **Weight-availability.** The base model must be weights-available under a licence that permits the operator's use. Meta Llama, Mistral, Qwen, Gemma-family are common candidates. <!-- needs-research: pin the specific licence terms of the chosen base per rev; the terms of the open-weight licences change across versions. -->
- **Acceptable-use policies.** Even weights-available models carry AUPs. Meta's Llama Acceptable Use Policy, for example, includes clauses about generating harmful content and about red-teaming. The operator's red-team programme must map its use to the AUP explicitly; the CMC section 5 records the mapping.
- **No frontier API models as attackers.** Using an API-served frontier model as a fine-tuned attacker is not a live path — no frontier provider currently sells fine-tuning of their model for adversarial red-teaming at the depth this chapter requires. Even if such a path existed, its terms-of-service would materially constrain the corpus. The pragmatic choice is a weights-available base fine-tuned on the operator's own infra.
- **The choice is a level-40 responsibility, not a peer-role responsibility.** `fine-tuning-engineer` (peer, level 30) fine-tunes what they are given; the *choice* of base — including the licence review — is a mod-111 decision the level-40 role signs.

## Fine-tuning objective and pipeline

The fine-tuning objective depends on the corpus shape.

- **Supervised fine-tuning (SFT).** The straightforward starting point. Corpus entries are `(context, attack_prompt)` pairs; the attacker learns to generate attack prompts conditioned on the context (behaviour category, target family, refusal history). SFT is what most operator-scale attackers start with; the resulting attacker is a *strong seed generator*.
- **Direct-preference optimisation (DPO) or similar.** Given `(context, attack_A, attack_B, preferred)` triples where the preference is *which attack succeeded against the target*, DPO teaches the attacker to prefer successful attack shapes. The preferences come from the same judge (chapter 05) that grades the CMC.
- **Reinforcement-learning from a target-judge reward.** The Perez et al. shape: a rollout is `attacker → target → judge`, the judge's verdict is the reward. This produces a *stronger* attacker than SFT / DPO but is expensive and reward-hackable. The chapter-03 discussion of RL-based attackers applies: diversity collapses, judge exploits amplify. Entropy regularisation and judge-panel diversification (chapter 05) are the standard remedies.

The pipeline itself — the SFT loop, the DPO loop, the RL rollout infrastructure, the evaluation harness during training, the checkpoint store, the guardrails around the training data — is `fine-tuning-engineer` (peer, level 30) craft. This module *specifies* the pipeline contract: the input corpus schema, the base-model checkpoint hash, the training-config values, the output-checkpoint reproducibility guarantees. The peer role delivers the plumbing.

The peer-role handoff shape:

- *"The mod-111 attacker training requires SFT on corpus C@v_5 with base B@v_1, batch size N, LR schedule S, for E epochs, evaluated every K steps against the mod-111 held-out eval set. Output checkpoint is signed against the training-log Merkle root. Please implement in the shared training platform."*
- The peer role delivers; the safety-engineering role reviews that the output checkpoint matches the specification.

## Diversity and ASR measurement against production models

A fine-tuned attacker is only useful if it *materially* beats the general-attacker baseline (chapter 03) on the same cell. The evaluation methodology:

- **Baseline: general-LLM attacker + PAIR / TAP / Crescendo.** Run the chapter-03 loop with a general instruction-following LLM as the attacker; measure (ASR, diversity, cost) per cell. This is the *baseline the fine-tuned attacker must beat*.
- **Fine-tuned attacker: same loop.** Replace the general attacker with the fine-tuned attacker; run against the same targets, same seeds, same budget. Measure (ASR, diversity, cost).
- **Report the delta.** Per cell, the improvement in ASR, the change in diversity, the change in cost. A cell where the fine-tuned attacker is *worse* on any of these axes is a cell where the fine-tuned attacker is not deployed; the CMC section 3 records this per cell.
- **Diversity is the trap.** A fine-tuned attacker will typically raise ASR and *lower* diversity — it has learned a mode. The training-methodology countermeasures (entropy-regularisation, diverse-seed sampling, ensemble-attacker) apply here. The CMC section 4 diversity thresholds are what catch a mode-collapsed attacker before it ships.

### Held-out sets and honest measurement

- **Held-out corpus.** A slice of the corpus never seen during training, used to evaluate the attacker. If the held-out ASR is materially higher than the training ASR, the attacker has memorised, not generalised.
- **Held-out targets.** Targets the attacker was not trained against. If the held-out-target ASR collapses to baseline, the attacker has overfit to the training-target family; the corpus needs broader targets.
- **Held-out behaviours.** Behaviour categories the attacker was not trained on. If held-out-behaviour ASR is at baseline, the attacker is *specialised*, not *general*; that is fine, as long as the CMC section 3 records the specialisation. A specialised CBRN-attacker is legitimate; a specialised CBRN-attacker deployed against cyber-offense cells is a false-coverage claim.

## Update cadence as production models rev

The fine-tuned attacker is not a build-once-and-forget artefact. Every material rev of the target family degrades its ASR — the target's safety-tuning has moved, the attacker's learned exploits are patched, the corpus has become stale. The CMC section 3 records the update cadence.

Typical cadences:

- **Quarterly re-fine-tune** on the accumulated red-team wins since the last cycle plus any refreshed public corpora. A defensible baseline for operators whose target family revs on a quarterly cadence.
- **Per-target-rev re-fine-tune** for the highest-stakes deployments (RSP / Preparedness / FSF tier-decision-shaping cells). The attacker is re-fine-tuned specifically against a candidate target rev before that rev's tier decision; the resulting checkpoint is a *decision-specific artefact*.
- **Continuous incremental fine-tuning** for operators with the training-platform maturity to support it. Every red-team win from the operator's own program is folded into the attacker's training set within days; the attacker's checkpoint version-bumps continuously.

Each cadence produces a versioned checkpoint recorded in the CMC section 3; each new checkpoint runs the held-out evaluation before being promoted to the matrix's default attacker slot.

## Handoff to `fine-tuning-engineer` (peer, level 30)

The peer role owns:

- The SFT / DPO / RL training pipeline itself.
- The training-cluster orchestration.
- Checkpoint storage and provenance.
- The training-time evaluation harness.
- Training-data-provenance signing (the training log's Merkle root and the corpus version-hash it consumed).

This module owns:

- The corpus (content, governance, licence review).
- The base-model choice and its licence review.
- The evaluation methodology (ASR, diversity, held-out sets).
- The update cadence.
- The CMC section 3 record of the resulting attacker.

Findings that route to the peer role are shaped as *"the attacker training requires this training-pipeline property; the peer role's platform would benefit from providing it or currently does not."* Examples:

- *"The attacker training requires a corpus-version-hash to be recorded in the training log; the current pipeline does not; recommend adding a corpus-version-header."*
- *"The attacker training requires held-out-evaluation to run every 500 steps; the current pipeline runs at the end only; recommend the intermediate evaluation."*
- *"The attacker training requires the corpus not to be persisted in the training-cluster's shared filesystem; the current pipeline stages the corpus locally; recommend the ephemeral-mount pattern."*

The finding is routed, not fixed inside mod-111. The peer role delivers.

## Interfaces

- **Chapter 03 (LLM-vs-LLM loops).** The fine-tuned attacker plugs into the chapter-03 attacker slot; PAIR / TAP / Crescendo run with the fine-tuned attacker as their attacker LLM.
- **Chapter 05 (judge).** The judge's calibration is what makes the (ASR, diversity) evaluation of the fine-tuned attacker meaningful.
- **Chapter 06 (coverage matrix + reproducibility).** The attacker checkpoint hash is one of the CMC section-6 reproducibility fields.
- **mod-104 / mod-105 / mod-106.** The operator's red-team wins from these modules are the corpus's highest-signal component.
- **mod-108 (guardrails).** The guardrail engineer consumes the attacker's outputs as adversarial-training data; the corpus flows the other direction.
- **`fine-tuning-engineer` (peer, level 30).** Owns the training-pipeline plumbing; the CMC section 3 pins the handoff shape.
- **`ai-infra-security` (peer, level 35).** Owns the training-cluster hardening, the checkpoint provenance, and the harmful-payload storage security. Chapter 06 walks the storage discipline itself.

## Common misreadings to avoid

- **"A fine-tuned attacker replaces general attackers everywhere."** No. The general attacker is the baseline the fine-tuned attacker is measured against; a cell where the fine-tuned attacker is not materially better is a cell that keeps the general attacker. The CMC records this per cell.
- **"The attacker should be trained on an unaligned base to be strongest."** No. Standard aligned bases are fine attacker seeds; the SFT / DPO on the operator's corpus is what teaches attack-generation. An "unaligned base" carries additional licence, security, and reputational risk that the modest ASR gain does not justify.
- **"The corpus can live in the general repo."** No. The corpus is harmful payload. Chapter 06 walks the storage discipline; the CMC section 5 pins the storage location outside the repo.
- **"Diversity gain is nice-to-have."** No. Diversity gain is *necessary*. A fine-tuned attacker that raises ASR and collapses diversity is a mode-collapsed attacker; the CMC's diversity threshold is what catches it.
- **"Once we have a good attacker, we can stop retraining."** No. Every target rev degrades the attacker; the update cadence is a CMC contract. A stale attacker is a false-coverage claim.
- **"We can outsource the base-model choice to the peer role."** No. The licence review, the AUP mapping, the acceptable-use posture is a level-40 responsibility. `fine-tuning-engineer` peers own the training pipeline; the *choice* is signed by mod-111.

## Summary

- A fine-tuned adversarial attacker is a *specialised* attacker whose corpus reflects the operator's target family and whose value proposition is materially higher ASR + diversity at lower cost than a general-attacker baseline.
- The corpus is the load-bearing artefact — accumulated red-team wins + curated public corpora + curated public red-team corpora + governed synthetic augmentation. It lives outside the source repo under harmful-payload discipline (chapter 06).
- Base-model choice is a level-40 responsibility; licence and AUP mapping are the level-40 role's sign-off, not the peer role's.
- Fine-tuning objective is SFT (baseline), DPO (preference-informed), or RL from judge-reward (strongest but reward-hackable). The pipeline is `fine-tuning-engineer` (peer, level 30) craft; this module specifies the contract.
- Evaluate against the general-attacker baseline per cell. Report (ASR, diversity, cost) delta. Held-out corpus, held-out targets, held-out behaviours guard against memorisation and false-coverage.
- Update cadence tracks the target family's rev cadence: quarterly, per-target-rev, or continuous. The CMC section 3 records the checkpoint per cell.
- Diversity collapse is the fine-tuned attacker's characteristic failure mode; the CMC's diversity threshold is what catches it before it ships.
