# 03 — LLM-vs-LLM Attacker Loops

## Motivation

Chapter 02 named PyRIT as the *attacker-orchestrator* layer plugged into Inspect. This chapter walks the specific attackers PyRIT hosts — and that a defensible red-team program runs *without* a specific framework, in Python written by the safety engineer — that use one language model to attack another. LLM-vs-LLM attackers are the load-bearing mechanism for scaling adaptive attacks. A single human red-teamer can craft ten adaptive PAIR-shaped iterations against a target in an hour; a PyRIT orchestrator can run ten thousand in the same hour, in parallel, against every candidate rev. The chapter's contribution is the engineering methodology around those loops: rate-limit and cost budgets under scale, RL-based extensions, and the diversity metrics that separate a *ten-thousand-attack* corpus that covers ten failure modes from one that repeats the same jailbreak template ten thousand times.

The load-bearing insight is that *ASR alone is misleading*. An attacker loop that produces ten thousand attacks with an 80% success rate against a target *sounds* devastating; if the ten thousand attacks are ten trivial variants of the same DAN-family prompt tokenised eight hundred times, the attack surface it evidences is one prompt, not ten thousand. Chapter 03 introduces *diversity* as a first-class metric — the semantic distance across successful attacks — and pairs it with ASR everywhere. A CMC section-4 judge contract that scores cells with ASR-alone is under-specified; a contract that scores with (ASR, ASR CI, diversity) is defensible.

## Primary sources

- **[Chao et al. 2023 — "Jailbreaking Black Box Large Language Models in Twenty Queries"](https://arxiv.org/abs/2310.08419)** — the PAIR paper. Reads front-to-back; the attacker / target / judge loop shape and the twenty-query budget are the reference architecture.
- **[Mehrotra et al. 2023 — "Tree of Attacks: Jailbreaking Black-Box LLMs Automatically"](https://arxiv.org/abs/2312.02119)** — the TAP paper. Extends PAIR with branching and pruning; the tree-search shape is the reference architecture for cost-controlled deeper adaptation.
- **[Russinovich et al. 2024 — "Great, Now Write an Article About That: The Crescendo Multi-Turn LLM Jailbreak Attack"](https://arxiv.org/abs/2404.01833)** — the Crescendo paper. The multi-turn refusal-erosion methodology and the way the attacker's prior context is engineered.
- **[Perez et al. 2022 — "Red Teaming Language Models with Language Models"](https://arxiv.org/abs/2202.03286)** — the foundational Anthropic paper for LLM-vs-LLM red-teaming; the RL-based attacker methodology this chapter cites for the deeper attacker training.
- **[Microsoft PyRIT — orchestrator source](https://github.com/Azure/PyRIT/tree/main/pyrit/orchestrator)** — the reference implementation for the loops discussed below. <!-- needs-research: pin the current PyRIT release + the orchestrator subpath; Microsoft has moved this path across releases. -->

## The three canonical attacker loops

### PAIR — Prompt Automatic Iterative Refinement

The reference architecture is a three-agent loop: an **attacker** LLM, a **target** LLM, and a **judge** LLM. The attacker generates a candidate prompt aimed at the target-behaviour; the target completes it; the judge scores whether the target's completion satisfies the target-behaviour. The judge's score plus the target's completion are fed back to the attacker as context for the next iteration. The loop repeats up to a query budget (Chao et al.'s twenty-query cap is the canonical starting point; scaled programs relax it under a cost budget).

Concretely for the CMC:

- **Cell shape.** `(attack_technique=PAIR, target_behaviour=B, model=M, decoding=D)`. Each cell runs N seeds through the PAIR loop.
- **Attacker choice.** A general-purpose instruction-following LLM is the *default* attacker; chapter 04 introduces a *fine-tuned attacker* that plugs in here.
- **Judge choice.** The StrongREJECT-shape judge (chapter 05) is the default; PyRIT's default `SelfAskLikertScorer` is replaced.
- **Budget.** Per-seed query budget (e.g., 20 for a baseline pass, 40 for a deep pass), per-cell total query budget, per-run total dollar budget. The budget is what turns PAIR from *"we tried"* into *"we tried this specific 400-query cell for $12."*
- **Termination.** Success (judge verdict positive), budget exhaustion, or an operator-configured max-refusal-run threshold beyond which the loop declares the seed *unbroken*.

The failure mode PAIR catches that a static-prompt corpus does not: the *iteratively refined* attack — the target refuses the direct request, the attacker sees the refusal and rewrites, the target refuses again, the attacker keeps rewriting. Static-corpus red-teaming will miss the attacks a modestly-clever attacker can reach in ten iterations.

### TAP — Tree of Attacks with Pruning

TAP is PAIR with a *branching* strategy. Instead of a single sequential rewrite chain, the attacker generates *multiple* candidate rewrites at each step; a critic (usually the judge, or a separate lightweight LLM) *prunes* branches that seem unpromising; the remaining branches are explored in depth. The result is a tree whose successful leaves reach behaviour the target refuses on any single sequential chain.

Concretely for the CMC:

- **Cell shape.** `(attack_technique=TAP, target_behaviour=B, model=M, decoding=D, branching_factor=k, depth=d)`. The `(k, d)` axes shape the cell; a defensible CMC pins a small grid rather than sweeping.
- **Cost.** TAP is *more expensive per seed than PAIR*. The scale-up is roughly $O(k^d)$ before pruning. The chapter-01 program cadence discusses when TAP is worth the cost (pre-deployment sign-off, tier-decision inputs) and when PAIR is enough (nightly regression).
- **Diversity.** TAP produces more diverse successful attacks per seed than PAIR — the branching explores different rewrite directions. The diversity metric below reflects this.

The failure mode TAP catches that PAIR does not: the *branching-required* attack, where the single-thread refinement dead-ends after N iterations but a branch three iterations back would have succeeded if explored. In practice, targets that are hardened against PAIR fall to TAP with a small `(k, d)`.

### Crescendo — multi-turn refusal-erosion

Crescendo is a *multi-turn* attack shape. The attacker does not try to jailbreak the target in a single prompt; the attacker engineers a *conversation* that opens on innocuous context, gradually shifts the frame, and reaches the target-behaviour after several turns during which the target's refusal-threshold has effectively been lowered by the accumulated context. The paper walks specific structural patterns — reframe as fiction, escalate through analogy, use the target's own prior compliance as a lever — and the mechanism generalises to a broad category of *conversational* jailbreaks.

Concretely for the CMC:

- **Cell shape.** `(attack_technique=Crescendo, target_behaviour=B, model=M, decoding=D, max_turns=T)`. `T` is often 6–10 for a baseline pass.
- **Attacker choice.** A general instruction-following LLM works, but a *fine-tuned* attacker (chapter 04) that has learned the operator's specific Crescendo-shaped patterns performs materially better.
- **Judge choice.** The StrongREJECT judge scores the *final* completion; the earlier turns are context. This is different from PAIR where every turn is judged; the CMC's judge contract distinguishes.

The failure mode Crescendo catches that single-turn PAIR and TAP do not: the *multi-turn refusal-erosion*, where the target refuses the direct request on turn one and would refuse it on turn six *if asked directly*, but the intervening context has repositioned the target's behavioural frame such that it complies.

## PyRIT hosts these loops; a red-team program owns the composition

PyRIT ships `PAIROrchestrator`, `TreeOfAttackOrchestrator`, and `CrescendoOrchestrator` — the source references above are the reference implementations. The engineering work at this level is *not* re-implementing the loops; it is:

- **Choosing the (attacker, target, judge) triple per cell.** Chapter 04 fine-tunes the attacker; chapter 05 owns the judge; the target is the system-under-test.
- **Configuring the budgets per cell.** Query cap, dollar cap, wall-clock cap.
- **Configuring the seed corpus per cell.** The seed prompt corpus (chapter 06's harmful-payload discipline) is what the orchestrator iterates from.
- **Extracting the (ASR, diversity, cost, turn-budget-consumed) tuple per cell.** For the CMC report.
- **Deciding when a cell needs a stronger attacker.** The chapter-04 fine-tuned attacker replaces the general LLM here; the decision is documented in the CMC section 3.

The composition rule the chapter-02 spine already stated applies: the orchestrator is a *solver*, wrapped in an Inspect task, whose scorer is the chapter-05 judge. PyRIT provides the mechanics; Inspect provides the runner.

## RL-based attackers — beyond canonical loops

PAIR / TAP / Crescendo are prompt-engineering-inside-a-loop attackers. The Perez et al. paper introduces a stronger class: an *RL-trained* attacker whose policy is trained end-to-end to maximise a jailbreak reward against a target family. The attacker becomes *learned* rather than *prompted* — the same distinction that separates GCG from PAIR at a lower level.

For a scaled red-team program, RL-based attackers matter in two places:

- **As one axis of the coverage matrix.** A cell whose attack technique is *"RL-trained attacker, checkpoint version V"* runs alongside PAIR / TAP / Crescendo. It is a different attacker; it produces different failures.
- **As an ongoing attacker-training loop.** The RL attacker's training set is refreshed with the operator's failure-mode corpus (chapter 04). When the target rev changes, the RL attacker is fine-tuned onto the new target. The pipeline is the mod-104 GCG-shaped adversarial-training pattern applied to attacker training instead of defence training.

The engineering caution: an RL-trained attacker whose reward is *"target says yes"* is a reward-hacking magnet. The attacker learns to satisfy the *judge* rather than to *elicit the target behaviour*. This is a known failure mode of every RL-based attacker literature entry, and it is why chapter 05's judge-drift monitoring is load-bearing — a *judge that is being reward-hacked* is a judge that produces false positives at scale.

Fine-tuning a dedicated attacker is chapter 04's craft. RL-based extensions are a subclass; the corpus + evaluation methodology is the same.

## Rate limits and cost budgets under scale

Scaled LLM-vs-LLM loops are *expensive* — three model calls per iteration (attacker, target, judge), N iterations per seed, K seeds per cell, C cells per matrix run. A back-of-envelope for a matrix with 100 cells × 100 seeds × 20 iterations × 3 model calls = 600 000 calls per matrix run. At a typical frontier-model cost, that is real money. Two engineering disciplines matter.

- **Per-cell budgets, enforced in code, not by convention.** The orchestrator carries a `budget_dollars`, `budget_queries`, and `budget_wallclock`. When any budget exhausts, the cell terminates with a *"budget-exhausted, N seeds broken, K unbroken, P undetermined"* report entry. The CMC's section-3 pins these budgets; the report clearly distinguishes *"unbroken with budget"* from *"undetermined because budget exhausted."*
- **Provider rate limits are the *outer* constraint.** Frontier-model APIs enforce token / minute and requests / minute limits per key; the orchestrator's inner parallelism must respect them or the run is throttled and the wall-clock budget exhausts. PyRIT does not itself manage inter-orchestrator rate limits; the *program* wraps its orchestrators in a rate-limit-aware scheduler (a token-bucket in front of each key, a fairness policy across concurrent cells) and enforces the outer constraint. This is a boundary the CMC section-3 pins to the `ai-eval-engineer` peer role's judge-serving / traffic-shaping infrastructure.

Cost accounting per cell is part of the CMC report; a cell whose ASR is 20% at $500 is a different program artefact from a cell whose ASR is 20% at $5. The cost delta may drive the decision to fine-tune (chapter 04) — a fine-tuned attacker on a smaller base model can be materially cheaper per successful attack than a large frontier-model attacker.

## Diversity as a first-class metric

The load-bearing insight of the module: *ASR without diversity is unfaithful.* A cell's ASR is `(successful attacks / total attacks)`; a *diverse* set of successful attacks evidences a wide attack surface; a *repetitive* set evidences a narrow one. The two must be reported together.

### What diversity means

Semantic distance across successful attacks in a cell. Common concrete shapes:

- **Embedding-distance clustering.** Embed each successful attack under a chosen embedding model; cluster with a chosen threshold; the number of clusters and the cluster-size distribution is the diversity metric. Report the number-of-unique-clusters at a threshold, e.g., `unique_clusters@0.85 = 12`. <!-- needs-research: pin the specific embedding model + threshold the CMC uses; the choice is a methodology commitment. -->
- **N-gram diversity.** Distinct token n-grams across successful prompts; simpler than embedding-distance, weaker signal but useful as a check that the loop is not producing near-duplicates.
- **Judge-provided facet coverage.** For behaviour categories the judge can decompose (e.g., CBRN-uplift split into acquisition / synthesis / dispersal), the judge tags each successful attack with the covered facet; diversity is *facet coverage*.

The CMC section-4 judge contract names the specific diversity metric and its threshold per cell; a cell whose diversity threshold is not met is *not* reported as covered even if ASR is high.

### Why diversity matters for downstream artefacts

- **Safety cases (mod-109).** An *inability* claim backed by an ASR-only measurement is weak; an inability claim backed by *"across 15 distinct semantic clusters of attempted attacks the target refused"* is materially stronger. Diversity is the leg that separates *"we did not try hard enough"* from *"we exhausted a broad attempt surface."*
- **Guardrail retraining (mod-108).** A guardrail trained on repetitive successful attacks overfits. Diversity in the training data matters; the CMC's diversity metric feeds the guardrail training-set curation.
- **Disclosure (mod-112).** An AISI-facing report that says *"we ran 10 000 attacks with 20% ASR"* is uninterpretable without diversity. AISI's evaluation methodology treats diversity as a first-class artefact.

### How the loops affect diversity

- **PAIR** produces low diversity per seed (the iterative refinement stays close to the seed) and moderate diversity across seeds. Seed-corpus diversity is what carries the loop.
- **TAP** produces higher diversity per seed than PAIR (branching explores directions) and similar across-seed diversity.
- **Crescendo** produces low diversity per seed (each successful attack is a *conversation*; the conversational shape is repetitive) and moderate across-seed diversity.
- **RL-trained attackers** tend to *reduce* diversity — the RL reward drives towards the successful mode. Countering this is an active engineering problem; entropy-regularisation and diversity-bonus reward shaping are the standard remedies. <!-- needs-research: cite a specific paper on diversity-preserving RL red-team training if one is published; if not, mark as an open problem in the CMC. -->

The CMC section-3 records the diversity of each cell's successful-attack set; deltas across model revs are what a reviewer inspects.

## Composing the loops into a matrix pass

A defensible pass shape for a mid-cadence run (nightly on a candidate rev):

1. **PAIR nightly baseline** — modest per-cell budget (e.g., 20 queries per seed, 50 seeds per cell), across the top attack-technique × behaviour cells. Fast, cheap, catches regressions.
2. **Garak nightly scanner** (chapter 02) — parallel to PAIR; catches known-behaviour regressions.
3. **Promptfoo per-PR** — orthogonal cadence; catches scaffold regressions.
4. **TAP pre-deployment gate** — larger per-cell budget, ran ahead of a material deployment change; feeds the tier-decision letter.
5. **Crescendo pre-deployment gate** — multi-turn coverage; feeds the tier-decision letter.
6. **Fine-tuned attacker (chapter 04) pre-deployment gate** — the operator's dedicated attacker; feeds the RSP / Preparedness / FSF tier decision.
7. **RL-trained attacker (opt-in) pre-deployment gate** — the deeper attacker for the tier-decision-shaping cells only.

Each pass's outputs feed the same coverage matrix. The CMC section-3 names which pass populates which cells and at what cadence.

## Interfaces

- **Chapter 02 (orchestration).** PyRIT orchestrators plug into Inspect as solvers; the composition rule from chapter 02 applies.
- **Chapter 04 (fine-tuned attacker).** The attacker LLM is fine-tuned on the operator's failure-mode corpus; the fine-tuned checkpoint plugs into the PAIR / TAP / Crescendo attacker slot.
- **Chapter 05 (judge).** The StrongREJECT judge replaces PyRIT's default scorers everywhere; the judge's calibration is what makes the loops' verdicts trustworthy.
- **Chapter 06 (coverage matrix).** ASR + diversity + cost per cell is the CMC report shape.
- **mod-104 (jailbreak).** The technique-axis population comes from mod-104's craft depth; this chapter scales those techniques.
- **mod-108 (guardrails).** Guardrail-effectiveness cells run the loops with guardrails on and off; the delta feeds the guardrail work.
- **`fine-tuning-engineer` (peer, level 30).** Chapter 04's attacker fine-tuning is scoped in that peer's craft.

## Common misreadings to avoid

- **"Higher ASR is better."** No. Higher ASR is *worse* in the sense that it evidences a more broken target; the *loop's* ability to hit a high ASR at low cost with high diversity is a fair measure of the loop, but the *target's* posture is judged against the ASR + diversity + cost tuple together.
- **"PAIR / TAP / Crescendo are interchangeable."** No. PAIR is single-thread refinement; TAP is branching; Crescendo is multi-turn refusal-erosion. Each catches attacks the others miss. The CMC covers all three.
- **"An RL-trained attacker replaces the others."** No. RL-trained attackers are one axis; they *reduce* diversity unless carefully regularised. The CMC includes them alongside PAIR / TAP / Crescendo, not instead of.
- **"Cost is a budget concern, not a program concern."** Cost drives which cells run at which cadence; cost is what makes the difference between a per-rev matrix and a per-quarter matrix; cost accounting per cell is part of the CMC.
- **"Diversity is a research metric; ASR is the operations metric."** No. Diversity is the CMC's grading metric alongside ASR. A cell with high ASR and low diversity is a *repetition finding*, not a coverage finding; the CMC distinguishes.
- **"The judge is fine at default; the loop is where the engineering is."** No. The judge is the *trust anchor* for every loop's verdict. A miscalibrated judge produces a devastating scaling failure; chapter 05 is where that anchor is engineered.

## Summary

- LLM-vs-LLM attacker loops are the load-bearing mechanism for scaling adaptive attacks. PAIR, TAP, and Crescendo are the three canonical shapes; RL-trained attackers extend the class.
- PyRIT hosts reference implementations; the engineering work is the composition — choosing the (attacker, target, judge) triple, configuring budgets, extracting the (ASR, diversity, cost) tuple.
- Report ASR *alongside diversity*. ASR alone misleads; diversity separates a broad coverage claim from a narrow-repetition finding.
- Rate limits and cost budgets are enforced in code, not by convention. Per-cell budgets terminate cleanly with reported *"budget-exhausted"* leaves.
- RL-based attackers are a distinct class; they collapse diversity unless deliberately regularised. Chapter 04 discusses the corpus + fine-tuning methodology that produces them.
- Composition into a matrix pass: PAIR nightly, garak nightly, Promptfoo per-PR, TAP + Crescendo + fine-tuned attacker + (opt-in RL) pre-deployment.
- The judge (chapter 05) is the shared trust anchor across all loops; miscalibration cascades everywhere.
