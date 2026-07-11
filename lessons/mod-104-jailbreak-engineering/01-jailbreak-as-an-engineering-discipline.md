# 01 — Jailbreak as an Engineering Discipline

## Motivation

Jailbreak is the second-most-cited failure mode in agentic-system security writing after prompt injection, and the term the popular press is most confident about. The literature has moved well past the popular meaning. GCG (Zou et al., 2023) turned jailbreak into a differentiable optimisation problem. PAIR (Chao et al., 2023) and TAP (Mehrotra et al., 2023) turned it into an LLM-vs-LLM search over a black-box API. Many-shot jailbreaking (Anthropic, 2024) turned the growing context window into an attack vector. Crescendo (Russinovich et al., 2024) turned multi-turn refusal erosion into a repeatable methodology. StrongREJECT (Souly et al., 2024) turned the *measurement* into an honest-broker rubric. HarmBench, JailbreakBench, AIR-Bench, AILuminate, and CyberSecEval turned the whole business into shippable coverage.

This chapter names the discipline the module builds, draws the boundary against the adjacent term *prompt injection* (mod-103), draws the boundary against the prerequisite role (`ai-risk-engineer`, level 25), and introduces the **Jailbreak Evaluation Harness (JEH)** — the engineering artefact the module produces.

Read this chapter first even if you have engineered jailbreaks by hand. The subsequent chapters assume that when you say "GCG" you mean the specific gradient-based procedure in chapter 02; when you say "PAIR" you mean the specific attacker/target/judge loop in chapter 03; and when you say "coverage" you mean the coverage-matrix contract in chapter 06.

## What jailbreak is — and what it is not

**Jailbreak is a class of attack in which adversary-controlled text causes a model to violate its own aligned safety policy — to emit output the model's post-training would otherwise refuse.** The primitive is *policy override*: the attacker moves the model from *would refuse* to *would comply* on a specific request whose refusal is a load-bearing property of the model's alignment.

It is emphatically **not**:

- **A prompt injection.** Prompt injection is *principal confusion mediated by a channel* (mod-103): the model conflates operator-instructions with attacker-instructions and follows the wrong one. A jailbreak targets *what the model considers refuseable*; an injection targets *whose instructions the model considers authoritative*. The two co-occur — an injection can *carry* a jailbreak payload; a jailbreak can *ride* an injection channel — and their measurement, defence, and disclosure workflows diverge. Chapter 09 codifies the boundary against mod-103; the short version is: injection asks "did the model follow the wrong principal?"; jailbreak asks "did the model do the forbidden thing?"
- **A safety-refusal failure in general.** If the model refuses a request and the refusal is wrong (a benign question wrongly refused), that is *over-refusal*, not a jailbreak. Over-refusal is a legitimate concern — MT-Bench, XSTest (Röttger et al., 2023), and the utility-vs-safety Pareto studies chart it — but it is the *opposite* of the failure this module hunts.
- **A hallucination or an emergent capability elicitation.** A model that fluently emits false medical advice under an ordinary framing has hallucinated; a model whose new capability appears only after a specific prompt has been elicited. Neither is a jailbreak unless there is a specific refuseable policy the model bypassed and a specific attacker-controlled string that made it bypass.
- **A term of art with universal agreement.** Different labs, benchmarks, and papers draw the line slightly differently. HarmBench focuses on categorical harmful behaviours; AIR-Bench 2024 grounds in regulator taxonomies; MLCommons AILuminate has its own hazard categories; CyberSecEval focuses on cyber-offence uplift. Chapter 06 reconciles the taxonomies; every finding you emit will name which taxonomy's cell it lands in.

The load-bearing definitional element is **policy override**. Every jailbreak you engineer in this module names (a) the specific model policy the model would refuse to violate absent the attack, (b) the attacker-controlled string that flipped it, and (c) the compliance evidence (the model's post-attack completion, judged by a rubric).

## Two boundaries this module draws

### Boundary 1 — jailbreak vs. prompt injection (mod-103)

For a chat-only model, the boundary is subtle. The attacker types text; the model does or does not comply with something it would otherwise refuse. The engineering distinction is about *which primitive the payload attacks*:

- If the payload's mechanism is "convince the model this is a *different task* than the operator asked for" — role-hijack, system-prompt spoofing, instructions-from-a-retrieved-document — it is an **injection** (mod-103), even if what it eventually asks for is policy-forbidden.
- If the payload's mechanism is "argue the model out of *this specific refusal*" — persona framing, fictional framing, many-shot pattern establishment, gradient-optimised suffix — it is a **jailbreak** (this module), even if delivered through an injection channel.

Formally: **injection is a principal attack; jailbreak is a policy attack**. Chapter 09 develops the boundary against the two overlays (OWASP LLM01 vs. MITRE ATLAS `Jailbreak` / AML.T0054) and the reason both labels are usually correct on the same finding.

<!-- needs-research: confirm the current MITRE ATLAS technique ID for jailbreak — the ID has moved across ATLAS versions and the surrounding technique family has been re-organised. Verify against https://atlas.mitre.org before citing. -->

The reason to insist on the distinction: the *defences* are different. Injection is defended with spotlighting, StruQ, sandwich prompting, tool-response sanitisation, and boundary controls (mod-103 chapter 07, mod-107). Jailbreak is defended with post-training safety-tuning, constitutional classifiers, judge-time monitors, and refusal-robustness curriculum (mod-108). Emitting the wrong label routes the finding to the wrong team.

### Boundary 2 — this module vs. `ai-risk-engineer` (prerequisite, level 25)

The prerequisite role covers **garak**, **PyRIT**, and **Promptfoo** as working red-team suites at general depth. Those suites include jailbreak plug-ins — DAN variants, roleplay probes, encoding tricks, basic refusal-bypass templates. If you completed the prerequisite you already know how to run those suites, wire their outputs to a CI, and produce a per-plug-in pass-rate table.

This module adds three capabilities that the prerequisite does not cover:

1. **Frontier-depth attack construction.** GCG's gradient objective, PAIR's LLM-vs-LLM attacker prompt, TAP's tree-of-attacks branch-and-prune, Crescendo's turn budget, many-shot's pattern-establishment scaling, and low-resource-language / cipher attacks that live outside every plug-in library.
2. **LLM-vs-LLM attacker loops** engineered for cost, rate limits, and stopping criteria — the loop is a system, not a script.
3. **Judge methodology at StrongREJECT standard** — a rubric with a calibration figure, not a refusal-word regex.

Chapter 09 codifies the boundary. When emitting a jailbreak finding, tag whichever module owns the attack construction and reference the prerequisite for the plug-in-suite context.

## The four attack families this module owns

Jailbreak construction sorts into four families that this module treats as first-class chapters:

1. **White-box gradient attacks** (chapter 02) — GCG and its descendants. Requires model weights or a good open-weight proxy. Produces adversarial suffixes that are often universal (one suffix works across many prompts) and often transferable (a suffix trained on `llama-family-open-weight` sometimes lands on `gpt-family-closed-weight`). The chapter measures both properties on your target.
2. **Black-box LLM-vs-LLM attacker loops** (chapter 03) — PAIR (single-turn iterative) and TAP (tree-of-attacks). An *attacker* LLM proposes prompts, the *target* LLM answers, a *judge* LLM scores compliance; the attacker updates its prompt from the judge's feedback. Runs against a production API. Cost and rate limits determine what "shippable" looks like.
3. **Multi-turn attacks** (chapter 04) — Crescendo and its variants. The attacker never asks for the forbidden thing directly; each turn escalates from an obviously-benign starting point until the model's refusal has been eroded. Single-turn evals miss this class by construction.
4. **In-context / long-context / linguistic attacks** (chapter 05) — Anthropic's many-shot jailbreaking, persona-hijack (DAN and descendants), low-resource-language translation attacks (Yong et al., 2023), cipher attacks (Yuan et al., 2023, CipherChat). The common thread is that the attack exploits some *aspect of the input distribution* — long context, rare language, encoded text, roleplay framing — that the model's safety training under-covers.

Every jailbreak you author in this module falls in exactly one of these families. Chapters 06–08 then treat *measurement* (benchmark landscape), *judging* (StrongREJECT), and *taxonomy* (Shen et al., DAN evolution) as first-class engineering concerns rather than after-the-fact chores.

## The Jailbreak Evaluation Harness (JEH) — artefact skeleton

The engineering artefact you author from this module is a **JEH** for one concrete target. Its skeleton — filled in across chapters 02–08 and exercises 01–07:

```yaml
jeh:
  target: <model_id_or_agent_id>          # open-weight, closed-weight API, or agent wrapping either
  target_kind: open_weight | closed_api | agent
  attackers:
    gcg:                                   # chapter 02
      run: {open_weight_proxy: <id>, closed_targets: [...]}
      universality: {n_prompts: ..., asr: ..., ci: ...}
      transferability: {targets: [...], transfer_rate: {...}, judge: ...}
    pair:                                  # chapter 03
      attacker_model: <id>
      target_model:   <id>
      judge_model:    <id>
      budget:         {max_queries: ..., wall_clock_s: ..., dollars: ...}
      stopping:       {asr_ceiling: ..., patience: ...}
    tap:                                   # chapter 03
      branch_factor: ...
      depth: ...
      prune: {reason: ...}
    crescendo:                             # chapter 04
      max_turns: ...
      plan:  <deterministic | attacker-llm-driven>
      refusal_erosion_track: [per_turn_asr, per_turn_toxicity, per_turn_topic_shift]
    many_shot:                             # chapter 05
      context_shots: [16, 64, 256, 1024]
      demo_source:   <curated defanged demos in external store>
    linguistic:                            # chapter 05
      low_resource_langs: [...]
      cipher: [rot13, base64, caesar, ascii_art, ...]
      persona: [dan_variants, opposite_mode, developer_mode, ...]
  benchmarks:                              # chapter 06
    harmbench:     {n_behaviours: ..., asr: ..., judge: ...}
    jailbreakbench:{n_behaviours: ..., asr: ..., judge: ...}
    airbench_2024: {n_categories: ..., asr: ..., judge: ...}
    ailuminate:    {n_hazards: ..., asr: ..., judge: ...}
    cyberseceval:  {tests: [...], asr: ..., judge: ...}
  judge:                                   # chapter 07
    scaffold:      strongreject_style_v1
    human_agreement: {k: ..., ci: ...}
    cost_per_1k_trials: $...
    over_refusal_control: xstest_or_equivalent
  regression_fixtures:                     # chapter 08
    seed_source: [shen_et_al_dan_corpus, harmbench_val, jailbreakbench_val, internal_wild]
    n_fixtures: ...
    reproducibility_bundle: <pointer>
  boundaries:
    to_mod_103_injection_harness: <pointer to PIEH>
    to_mod_108_guardrail_harness: <pointer>
    to_mod_111_scaled_red_team:   <pointer>
    to_ai_risk_engineer_suites:   {garak: <version>, pyrit: <version>, promptfoo: <version>}
```

Every field derives from one of the eight remaining chapters or from an exercise. If a field is empty in your JEH, name the chapter that fills it. Chapters 06–07 develop the coverage-matrix and judge substructures further.

## Harmful-payload discipline (previewing the modules that codify it)

Some jailbreak payloads in this module *work* — they elicit refuseable content against production frontier models. That is the point of a red-team artefact and also the reason for the storage rule this module inherits from mod-103 chapter 06 and mod-111:

- **The evaluation set — the actual jailbreak strings — lives outside this source repo.** Access-controlled artefact store (S3 bucket, dedicated internal repo, signed tarball on an evaluation server) with a manifest committed here.
- **The chapters and exercises reference payload *shapes* and *categories*, not literal weaponised strings.** Illustrative snippets are truncated, defanged, or point at published benchmark datasets by reference.
- **Access to the payload store is logged.** Reviewers running the harness receive scoped credentials; credentials do not live in a checkout.
- **Published benchmark strings are treated as citations, not as content.** HarmBench, JailbreakBench, and AIR-Bench publish their behavioural strings; your harness references them, but your derived jailbreak *responses* to those strings — the completions that show the jailbreak worked — do not get pasted into this repo.

Chapter 06 develops the storage layout for benchmark-adjacent artefacts. Exercise 05 exercises the discipline against a real harness. If you catch yourself pasting a working jailbreak string or a policy-violating completion into this repo or into any public artefact, stop. Working payloads route to your org's harmful-payload store; the finding routes to the mod-112 disclosure workflow.

## Success criteria for a jailbreak finding

Every JEH-emitted finding names:

- **The policy the model would refuse absent the attack.** Cite the model provider's usage policy (Anthropic Usage Policies, OpenAI Usage Policies, Google Gemini Prohibited Uses) or, for open-weight models, the closest published policy the fine-tune is meant to enforce.
- **The attacker-controlled string** (or, for multi-turn attacks, the turn plan) — held in the payload store, referenced by ID.
- **The target model, version, and decoding config** — reproducibility bundle. Chapter 02 develops the bundle in detail for GCG runs; chapter 06 for benchmark runs.
- **The judge and its version** — chapter 07's StrongREJECT-style rubric with a per-run calibration figure.
- **The compliance evidence** — the target completion, held in the store, referenced by ID.
- **The attack family** — one of the four this module owns.
- **The taxonomy cell** — HarmBench / JailbreakBench / AIR-Bench / AILuminate / CyberSecEval label, so the finding routes to the right benchmark's coverage report.
- **The severity** — the mod-112 severity scale. A refuseable-recipe elicitation is not the same finding as a WMDP-uplift elicitation.

A finding missing any of the above is a story, not a finding.

## Metrics the harness reports

For each cell and each run (developed further in chapters 06–07):

- **Attack Success Rate (ASR)** — fraction of trials the attack succeeded per the judge.
- **Refusal robustness** — inverse of ASR under an adversarial-elicitation budget, framed to catch models that refuse the naive prompt and comply with the paraphrased one.
- **Elicitation gap** — ASR under best-effort adversarial elicitation vs. ASR under a naive baseline. This is the RSP / Preparedness / FSF elicitation-gap metric from mod-101, applied to jailbreak.
- **Adaptive-attack survival** — ASR of an attack retrained specifically against the current defence stack. Chapter 07 develops the metric as StrongREJECT's third derived quantity.
- **Judge–human agreement** — per-run agreement on a random-sampled subset. Uncalibrated judge → un-shippable finding.
- **Judge cost** — dollars and tokens per 1k trials. Cost is a first-class metric because it determines what the mod-111 scaled harness can afford to sample.
- **Over-refusal control** — an XSTest-style benign-request pass-rate reported alongside ASR, so the harness distinguishes "the defence stack helps" from "the defence stack refuses everything."

## Common misreadings to avoid

- **"Jailbreak is solved — safety-tuning caught up."** Jailbreak evolves as fast as safety-tuning. Every named-and-published attack in this module post-dates the safety-tuning practices of its era; the pattern will continue. Chapter 07 develops the *elicitation-gap* metric precisely to catch the "solved on the benchmark, not solved in the wild" failure mode.
- **"We passed HarmBench, we're covered."** HarmBench is one benchmark, one taxonomy, one judge protocol. Chapter 06 develops the five-benchmark landscape precisely because no single benchmark covers the space. A JEH that ships one benchmark is a fraction of the artefact.
- **"Our vendor guardrails cover it."** Vendor guardrails are a defence layer that this harness measures, not a defence layer this harness relies on. Chapter 06 treats vendor guardrails as one row in the benchmark coverage report and chapter 07 stress-tests them.
- **"We can wait to build the JEH until we ship the defence."** Without a JEH you cannot measure whether any defence works, whether it regresses under model swaps, or whether an adaptive attacker circumvents it. Build the harness first.
- **"String-match on refusal words is a fine judge."** No. Souly et al. (2024) showed that refusal-word judges over-count ASR by a large margin because models produce apparent-refusal preambles before complying. Chapter 07 develops the StrongREJECT-style rubric that fixes it.
- **"We do not need to pin the model version — it is the same API."** Frontier lab APIs re-tune underneath the same endpoint. Every JEH run names a model snapshot / date pin explicitly (chapter 06). A finding without a snapshot pin is un-reproducible by construction.

## Summary

- Jailbreak is *policy override*, not principal confusion. Every finding names the refused policy, the attacker-controlled string, and the compliance evidence.
- The boundary against **prompt injection** (mod-103) is: injection attacks the principal; jailbreak attacks the policy. The two co-occur; the labels do not.
- The boundary against the **`ai-risk-engineer` prerequisite** (level 25) is: the prerequisite covers garak / PyRIT / Promptfoo at general depth; this module adds frontier-depth attack construction and LLM-vs-LLM attacker loops.
- The module owns four attack families — white-box gradient (GCG), black-box LLM-vs-LLM (PAIR / TAP), multi-turn (Crescendo), and in-context / long-context / linguistic (many-shot, persona, low-resource, cipher).
- The engineering artefact is a **Jailbreak Evaluation Harness (JEH)** with attackers, benchmark coverage (HarmBench / JailbreakBench / AIR-Bench / AILuminate / CyberSecEval), a StrongREJECT-style judge, and a regression-fixture library — with the payload store held outside this repo.
- The remaining eight chapters populate the JEH; the seven exercises exercise it against a concrete target.
