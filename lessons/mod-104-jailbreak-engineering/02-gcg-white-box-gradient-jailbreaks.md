# 02 — GCG and White-Box Gradient Jailbreaks

## Motivation

Zou et al. (2023) — "Universal and Transferable Adversarial Attacks on Aligned Language Models" — reframed jailbreak from craft to optimisation. Prior work was largely hand-authored: a human iteratively rewrites a prompt until it works, and the "attack" is the human's intuition. Zou et al. proposed **GCG (Greedy Coordinate Gradient)**: a discrete-optimisation procedure that searches for an adversarial *suffix* — a short block of tokens appended to a harmful request — that drives the model's next-token distribution toward a specified compliance prefix (canonically, "Sure, here is …").

The result matters for two reasons. First, it *scales*: instead of a human iterating for hours per prompt, GPU minutes-to-hours per suffix produce jailbreaks that work across many prompts (**universal**) and across many models (**transferable**), including closed frontier models the attacker cannot directly optimise against. Second, it *industrialises*: the attack is code, not craft, so the JEH can run it in CI. This chapter develops the objective, the procedure, the universality and transferability properties, the engineering knobs, and the measurement contract for what "GCG works on our target" means.

## The differentiable jailbreak objective

Ordinary safety-tuning teaches the model to place high probability on refusal tokens when the input matches a refuseable request. A jailbreak wants to shift probability mass toward compliance tokens instead. GCG makes that shift the direct optimisation objective.

Let the model be $\pi_\theta$, the harmful request be tokens $x_{1:n}$, an adversarial suffix be tokens $s_{1:k}$ appended to the request, and a chosen compliance prefix be tokens $y_{1:m}$ — for instance, "Sure, here is a step-by-step guide". GCG's objective is to find $s$ that maximises the log-likelihood the model would emit $y$ immediately after $[x; s]$:

$$
\max_{s \in V^k} \; \log \pi_\theta \bigl( y_{1:m} \mid x_{1:n} \oplus s_{1:k} \bigr)
$$

Equivalently, minimise the target-loss $\mathcal{L}(s) = -\log \pi_\theta(y \mid x \oplus s)$. Because the tokens $s$ live in a discrete vocabulary $V$, gradient descent does not directly apply. GCG's contribution is a *discrete* search that uses the gradient at each position to guide token substitution.

The high-level GCG loop, per Zou et al. (2023):

```
Input: model π_θ, request x, target completion y, suffix length k, steps T
Initialise s uniformly at random from V^k

repeat T times:
    for each position i in 1..k:
        # top-B token candidates at this position from ∇_{s_i} L(s)
        candidates[i] = top_B_indices_by_negative_gradient(∇_{s_i} L(s), B)

    # sample one substitution per step:
    (i, s_i_new) = random choice from {(i, c) : c ∈ candidates[i]}
    s' = s with position i replaced by s_i_new

    # accept if it improves the objective:
    if L(s') < L(s):
        s ← s'

return s
```

Two engineering points inside this loop matter enough to name:

- **The gradient is over the embedding matrix, not the discrete tokens.** GCG computes $\nabla_{e_{s_i}} \mathcal{L}$ (gradient wrt the embedding of the current token at position $i$) and then finds the top-$B$ *vocabulary* tokens whose embeddings, when substituted in, most decrease the loss to first order. The candidate set is then re-scored *exactly* by running the model forward on each candidate, and the best is accepted. This mixes cheap gradient guidance with an exact acceptance step, which is why GCG works while pure straight-through-estimator approaches struggle.
- **Only the suffix moves.** The request tokens $x$ and the target compliance tokens $y$ are held fixed. This is what makes the suffix *portable* — you can prepend a different request $x'$ and re-use the same $s$ (universality), or move to a different model $\pi_{\theta'}$ and re-use $s$ (transferability).

## Universality — one suffix, many requests

Zou et al. extend the single-request objective to a *set* of requests $\{x^{(1)}, \dots, x^{(N)}\}$ with matched compliance prefixes $\{y^{(1)}, \dots, y^{(N)}\}$:

$$
\min_s \; \frac{1}{N} \sum_{j=1}^N -\log \pi_\theta \bigl( y^{(j)} \mid x^{(j)} \oplus s \bigr)
$$

The optimiser averages the loss across the batch of requests at each step; the accepted substitution is the one that best decreases the *average* target-loss. A suffix trained this way is **universal** in the sense that it drives the same behavioural shift regardless of which harmful request it is appended to, within the training distribution.

Two engineering implications:

- **Universality trades depth for breadth.** A suffix optimised on one request tends to be shorter and more brittle; a suffix optimised on 20 diverse requests is often longer and less brittle. Chapter 06 has you report ASR both on the training-set requests and on a held-out set — the gap is the universality-generalisation error.
- **Batch composition matters.** A batch of requests concentrated in one HarmBench category will produce a suffix that under-generalises to other categories. Diversify the training batch across the taxonomy you plan to measure against.

## Transferability — one suffix, many models

The paper's second and more consequential finding: a suffix trained on open-weight models transfers non-trivially to *closed* frontier models. Zou et al. reported transfer to GPT-3.5, GPT-4, Bard, and Claude at rates that depend on the source-model ensemble and target-model era.

<!-- needs-research: pin the specific transfer rates Zou et al. (2023) reported against each closed target — the paper's numbers are model-era-specific and any citation in a JEH artefact should reference the paper's exact table and note the model versions tested. Then measure the current transfer rate against your target's current snapshot, because safety-tuning updates have moved these numbers repeatedly. -->

**Why it transfers, roughly.** Suffixes tend to concentrate probability mass on the compliance prefix by exploiting statistical regularities in the token distribution that are shared across models trained on overlapping data with overlapping safety-tuning pipelines. Trained on a diverse ensemble of open-weight models, the suffix over-fits *less* to any one model's idiosyncrasies and exposes the shared feature.

**Why the transfer rate degrades over time.** Safety-tuning pipelines at frontier labs incorporate known suffixes and their descendants into training data. A suffix that hit GPT-4-early-2024 will typically miss GPT-4-late-2024. The JEH must therefore treat transfer rate as a **time-series metric** — one number per target snapshot per date — not a constant.

**Engineering the transfer measurement.** Chapter 06 develops the coverage matrix; this chapter names the specific transfer-rate columns you owe on your JEH's transfer report:

- **Target list** — every closed-weight target the JEH cares about, versioned.
- **Batch composition** — the request set the suffix was trained on, mapped to HarmBench / JailbreakBench categories.
- **Ensemble composition** — every open-weight model in the source ensemble, with weight files pinned by checksum.
- **Trials per target** — the paper's headline numbers are averages over many trials with random-seed suffix inits; report your CI.
- **Judge** — the StrongREJECT-style judge (chapter 07) that scored the target's response. Refusal-word regexes over-count transfer by roughly the same amount they over-count naive ASR.
- **Snapshot** — the date and, where available, the target model's version string.

## The GCG procedure in practice — engineering knobs

The paper's algorithm is short. The engineering that makes it converge on a real target is not. The following knobs affect wall-clock, memory, and success rate, and every JEH GCG run pins them.

### Suffix length $k$

Longer suffixes give the optimiser more capacity but slow every step and hurt transferability (long suffixes over-fit). Zou et al.'s reference length was around 20 tokens; production JEH runs often sweep $k \in \{16, 20, 32, 64\}$ and pick by held-out ASR. Report the length as part of the finding, not a config default.

### Batch size $B$ (top-$B$ candidates per position)

Per step, per position, GCG takes the top-$B$ tokens by negative gradient and forward-scores the batch. Larger $B$ finds better substitutions per step but multiplies the forward-pass cost. A typical range is $B \in \{128, 256, 512\}$. The paper's default $B = 256$ is a reasonable starting point.

### Substitution granularity — one-position-per-step vs. multi-position

The published algorithm accepts one substitution per step. Variants (nanoGCG and later reproductions) allow multiple positions to be updated in parallel per step, which converges faster but risks proposing incoherent updates. Report the variant explicitly.

### Number of optimisation steps $T$

More steps → deeper convergence → longer runs. Typical single-suffix runs on a single open-weight target take low-thousands of steps. Universal-suffix runs across 20+ requests take much more; report both the step budget and the wall-clock.

### Ensemble weighting for transferability

Universal-and-transferable runs optimise the loss averaged over an ensemble of source models. Equal-weight is the simple baseline; weighted mixtures (favouring models whose behaviour matches the target's safety-tuning approach) sometimes help. Report the mixture.

### Chat-template pinning

The compliance-prefix $y$ is chat-template-sensitive: on a model whose system-turn opener includes "As an AI assistant," the natural compliance prefix looks different than on a model whose opener is "Assistant:". Renders that use the wrong template silently degrade convergence. **Log the exact chat-template renderer version in the reproducibility bundle.** Every GCG finding you report cites the template.

### Loss variants

The vanilla objective is target-loss on $y$. Variants:

- **Multi-target** — average the loss over several compliance prefixes.
- **Anti-refusal** — add a penalty term on the probability of common refusal phrases ("I cannot", "I'm sorry"), which speeds convergence for models whose refusals are peaky.
- **Contrastive** — regularise against a benign completion, so the suffix stays coherent (relevant for downstream transferability tests where the target model's judge sniffs incoherent suffixes).

Every JEH run names its loss variant.

## Defences, and why they matter to the attack

GCG's utility to a red-team engineer is precisely that it stress-tests defences that block hand-authored prompts. This chapter mentions defences only to close the loop; mod-108 covers guardrail training in depth.

- **Perplexity filters** (Alon & Kamfonas, 2023; Jain et al., 2023) — flag high-perplexity suffixes as adversarial. Effective against vanilla GCG suffixes because those suffixes tend to look like token-salad; less effective against GCG variants that regularise for fluency.
- **SmoothLLM** (Robey et al., 2023) — perturb the input at inference (random dropouts, swaps) and majority-vote the completion. Erodes GCG suffixes because a small perturbation to the suffix destroys its optimisation.
- **Circuit-breakers / representation-engineering defences** (Zou et al., 2024) — intervene at model internals rather than input surface.
- **Safety-tuning updates** — the frontier labs' first line, and the reason transfer rates decay in weeks.

Chapter 06 records each defence's effect on the JEH's GCG runs as a per-cell number, and chapter 07 develops the *adaptive-attack survival* metric — GCG re-run against the current defence stack, not against the pre-defence baseline.

## GCG variants and descendants

The GCG line of work continued after Zou et al.:

- **AutoDAN (Liu et al., 2023)** — hierarchical genetic-algorithm search over adversarial prompts that produces *fluent* jailbreaks, sidestepping perplexity filters.
- **AutoDAN-Turbo (Liu et al., 2024)** — strategy-library-conditioned attacker LLM that leverages a growing library of learned attack strategies.
- **nanoGCG** — a compact, open-source reference implementation of GCG with modern engineering (batched forward-scoring, HuggingFace integration, checkpointing) that most JEHs start from.
- **PGD-on-embeddings / GBDA** — continuous relaxations of the discrete search. Faster in wall-clock but harder to transfer.
- **BEAST (Sadasivan et al., 2024)** — beam-search over candidate substitutions with time-and-cost accounting.
- **Multi-target and defence-adaptive GCG** — GCG re-run to specifically defeat a chosen defence layer (perplexity filter, SmoothLLM, classifier-guarded input).

The JEH does not have to run every variant. It has to name which variant it runs and why, and it has to update when a new variant demonstrably raises ASR on the target.

<!-- needs-research: verify the citation years and paper titles for AutoDAN, AutoDAN-Turbo, BEAST, and nanoGCG before quoting from them — the arXiv metadata for follow-up work on GCG has been updated in place multiple times. -->

## Reproducibility bundle for a GCG finding

Every GCG finding your JEH emits includes a reproducibility bundle. Minimum contents:

- **Source ensemble** — each open-weight model, checkpoint hash, tokenizer hash, chat template ID.
- **Target list** — each closed-weight target with version snapshot, decoding config, and endpoint used.
- **Request batch** — the exact requests $x^{(1)}, \dots$ and compliance prefixes $y^{(1)}, \dots$ used in training, referenced by ID in the harmful-payload store.
- **Suffix length**, **top-$B$**, **step budget $T$**, **loss variant**, and any regulariser weights.
- **Random-seed range** — several seeds per experiment, so a single lucky suffix is not confused for a universal one.
- **Wall-clock and dollars** — the run's cost, so the mod-111 scaled harness can plan.
- **Judge and its version** — the StrongREJECT-style judge that scored the outputs (chapter 07).
- **Snapshot date** — the exact date the transfer runs happened, because target-model versions drift.

A GCG finding without a bundle is a story.

## Concrete example — the GCG training loop, defanged skeleton

The following is a *skeleton* of a GCG training loop for illustration. It is not a working exploit — it hands off both the specific harmful requests and the compliance prefixes to `HARMBENCH_BATCH_ID`, which resolves against your JEH's payload store outside this repo. Full reproductions live in the `nanoGCG` repository (see `resources.md`); this fragment is here so the JEH's authoring team knows what to plug where.

```python
# Skeleton only. Do not paste harmful strings here.
# Load requests / target completions from the payload store, not this file.
from jeh.payload_store import load_batch
from jeh.judges import StrongRejectStyleJudge
from jeh.gcg import GCG

batch = load_batch("HARMBENCH_BATCH_ID_v1")             # request/target pairs
ensemble = ["llama-family-open-weight-v1",
            "mistral-family-open-weight-v1"]            # names, not weights

gcg = GCG(
    source_ensemble=ensemble,
    suffix_len=32,
    top_b=256,
    steps=2000,
    loss="target_plus_antirefusal",
    seeds=list(range(8)),
)

suffix = gcg.fit(batch)                                 # returns suffix + trace

judge = StrongRejectStyleJudge(version="v1.2")
report = gcg.transfer_report(
    suffix=suffix,
    targets=["closed-api-target-A",                     # via provider SDK
             "closed-api-target-B"],
    holdout_batch="HARMBENCH_HOLDOUT_ID_v1",
    judge=judge,
    seeds=list(range(8)),
)
report.write("jeh/reports/gcg_run_2026_07_11.yaml")
```

The report's structure — universality ASR on the training batch, universality ASR on the held-out batch, transfer ASR per target — feeds directly into the JEH's coverage matrix (chapter 06).

## Common engineering mistakes when running GCG

- **Running one seed.** GCG's optimisation is randomised. A single lucky seed produces a single lucky suffix; the JEH reports across a seed range with a CI.
- **Optimising against one open-weight model, then reporting "universal-and-transferable".** Universality requires a diverse request batch; transferability requires a diverse source ensemble. Report both compositions.
- **Ignoring the chat template.** A suffix optimised under the wrong chat template silently under-converges; a transfer test under a mis-matched template silently under-succeeds.
- **Reporting ASR with a refusal-word judge.** Chapter 07 has the numbers on how much this over-counts. Do not accept the over-count.
- **Committing suffixes to the JEH source repo.** Suffixes are working payloads. They live in the store, not the repo.
- **Confusing "high perplexity" with "won't transfer".** Perplexity filters catch a chunk of vanilla suffixes; fluency-regularised variants (AutoDAN-family) route around them. Do not conclude "GCG doesn't work on our target" because you tested one variant against one defence.
- **Skipping the snapshot pin.** A GCG transfer number without a target-model snapshot is not a data point.

## Boundary to the black-box loops (chapter 03)

GCG requires white-box access — the source-model weights and forward/backward pass. Its transferability is what lets it *reach* black-box targets. But if you have zero white-box surface anywhere in the ecosystem — no open-weight model similar enough to your target for the transfer to hold — GCG is not your first tool.

Chapter 03 develops the **black-box iterative loops** (PAIR, TAP) that need only the target's API. In practice a JEH runs both: GCG for the models where transfer holds and for the offline stress tests; PAIR / TAP for the closed-only frontier targets and for the loops the scaled harness (mod-111) industrialises. The two are complementary.

## Summary

- GCG (Zou et al., 2023) reframed jailbreak as a differentiable optimisation problem: search for an adversarial suffix that drives the model toward a chosen compliance prefix. The suffix is often **universal** across requests and **transferable** across models.
- The core algorithm is *discrete-search-with-gradient-guidance*: gradient over embeddings picks a candidate token set per position; forward-scoring accepts the best substitution.
- Engineering knobs — suffix length, top-$B$ candidates, step budget, loss variant, ensemble composition, chat-template pinning, and seed range — determine whether a run converges and whether its number is defensible.
- **Universality** requires a diverse request batch; **transferability** requires a diverse source ensemble and time-sensitive measurement — closed-target transfer rates decay as safety-tuning updates ship.
- Descendant work (AutoDAN, AutoDAN-Turbo, BEAST, nanoGCG, defence-adaptive variants) extends the line; the JEH names which variant it runs and why.
- Every finding carries a reproducibility bundle: source ensemble, target list with snapshots, suffix length, top-$B$, step budget, seeds, judge version, dollars, and wall-clock.
- Chapters 06 and 07 fold the GCG numbers into the coverage-matrix and judge-methodology framing.
