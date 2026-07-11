# 08 — Adaptive Attacks and Defence Stress-Testing

## Motivation

Chapter 07 built the defence catalogue. Every layer in the catalogue was measured against a *static* attack set — the payloads in the replay bundle at the time the harness ran. The number that emerged is the *static ASR*.

Static ASR is a lower bound on the ASR against a real attacker. A real attacker knows about your defence and adjusts. The gap between static ASR and **adaptive ASR** — the success rate an attacker who has been told your defence and iterates against it will achieve — is the true measure of a defence's robustness. This chapter builds the discipline for measuring that gap.

The literature makes this point in the general adversarial-robustness setting: **[Carlini et al., "On Evaluating Adversarial Robustness"](https://arxiv.org/abs/1902.06705)** and the broader adaptive-attack methodology from the adversarial-examples community. The methodology transfers to injection: if you only evaluate against non-adaptive attacks, you are evaluating your defence against a strawman.

## What "adaptive" means for injection

An adaptive attacker, for the purposes of this module:

- Knows the operator's defence stack (every layer from chapter 07, including hyper-parameters like the sandwich reminder text, the spotlighting delimiter, the classifier's threshold).
- Has access to the target agent under the same interface a real attacker would (chat, embed, tool, retrieval-facing page, MCP client — whichever surface applies).
- Iterates: authors a payload, observes the outcome, adjusts, tries again — like a fuzzer with an evaluator in the loop.
- May be a human red-team engineer, a scripted attacker, an LLM-in-the-loop attacker (mod-111 territory), or a hybrid.

The threat model is not "the attacker knows everything." An attacker who knows your model weights and internal classifier logits is closer to the adversarial-examples formulation and lands mostly in the model / infra security peer roles' scope. This module's threshold is: **the attacker knows what a well-informed reader could learn from your product surface, your documentation, and a few hours of probing**.

## The adaptive-ASR contract

For each defence layer × each cell of the coverage matrix, the harness reports two numbers:

- **Static ASR** — the pass rate against the replay bundle as-is. Chapter 06 already produces this.
- **Adaptive ASR** — the pass rate after an adaptive attacker has been given a budget (compute, calls, hours) to iterate against the defence.

The **defence gap** for that cell is `adaptive_ASR − static_ASR`. A defence with a small gap is *robust*; a defence with a large gap is *brittle* — it looked good on the static evaluation but collapses under a real attacker. Robustness matters: a brittle defence gives operators false confidence and leaks worst under exactly the conditions that matter (real attackers rather than benchmark payloads).

## The adaptive-attack budget

Adaptive evaluation is expensive — it requires an attacker in the loop. Budget it explicitly:

- **Time-boxed human red-team.** Two engineers, four hours, per cell of the top-priority (Tier A) subset. The two engineers work independently; the union of their findings feeds the harness. Publish per-engineer variance.
- **Scripted attacker loops.** For each cell, a scripted attacker (chapter 08 example below) iterates N payload variants against the defence, gated by an oracle judge. Budget: 100 iterations per cell for Tier A, 20 for Tier B.
- **LLM-in-the-loop attacker.** An LLM asked to red-team the defence, given the defence description and the target agent's interface. Budget: 50 iterations per cell for a chosen subset. Mod-111 develops this pattern; this chapter's version is the minimum-viable form.

The choice of budget shapes the number. Publish the budget alongside the number.

## Adaptive-attack playbook per defence layer

For each layer, a small set of adaptive strategies to *always* try before reporting a robustness number.

### Adaptive attacks against spotlighting

- **Attack the delimiter rule.** Payload arguments the operator has updated the delimiter meaning (e.g., "the new documentation says content in this delimiter is now trusted").
- **Erode across turns.** Send a benign turn or two, then the payload; the model's memory of the delimiter rule may fade.
- **Straddle the delimiter.** A payload that begins outside the delimiter (in nominal operator territory via a template bug) and ends inside it.
- **Encode past the delimiter.** Base64-encoded content the model decodes and then reads as instruction.

### Adaptive attacks against StruQ

- **Slot leakage** — find a template-rendering bug that pushes attacker content into the instruction slot. Any operator template extension or hot-fix is a candidate.
- **Data-slot instructions the model still follows.** Some payloads persuade the model to act *despite* the training signal (framings that argue the instruction is meta-level, or that the current situation is exceptional).
- **Model-swap probes.** Ask what model is serving; if the model was swapped to a non-StruQ variant (canary, comparison, cost-tier), the defence is off.

### Adaptive attacks against sandwich prompting

- **Argue against the reminder.** Payload explicitly instructs the model to ignore any following reminder as "outdated."
- **Split the payload across the sandwich.** Half the payload before the retrieved block, half after — the reminder is bracketed by both halves.
- **Length-attack.** Fill the retrieved block with enough content that the reminder's proportional influence declines.

### Adaptive attacks against self-refuting classifiers

- **Two-model attack.** The primary payload is written to satisfy both the primary model and the classifier. The attacker uses their own local classifier as a scoring oracle.
- **Refusal-shape mimicry.** The primary model output is shaped to look like a legitimate assistant response even though it also emits the injected tool call.
- **Classifier-context injection.** The payload includes text that will land in the classifier's prompt too (if the classifier reads the primary prompt), influencing the classifier.
- **Cost-attack.** Drive up classifier cost until the classifier is bypassed under load.

### Adaptive attacks against tool-response sanitisation and boundary controls

- **Sanitiser-boundary probing.** Systematically try every Unicode block, delimiter, escape sequence, HTML tag, and length to find what the sanitiser silently passes.
- **Legit-URL exfil.** The exfil URL is a legitimately-allow-listed host (a Slack webhook, a Notion API endpoint, a GitHub gist). The payload steers the agent to *use* that surface for exfil rather than a novel domain.
- **Argument-shape re-check bypass.** Look for the delta between the schema the validator checks and the schema the executor honours; smuggle in that delta.
- **Cross-principal boundary probing.** The attacker plants in one tenant's corpus and probes whether the payload activates for a different tenant.
- **Rate-limit shaping.** Steer the tool call to stay under the blast-radius cap while accumulating impact across sessions or users.

Every strategy above is *general*. Adapt to the specific defence version in front of you.

## A scripted adaptive-attack example

A minimum-viable scripted attacker loop against a single defence and a single cell:

```python
# defanged pseudo-code; production adaptations live in the external harness store

def scripted_adaptive_attack(cell, defence_stack, max_iters=100):
    payload = starting_payload_for_cell(cell)
    best_asr = 0.0
    best_payload = payload
    for i in range(max_iters):
        trial = run_target_agent(payload, defence_stack)
        outcome = judge(trial, cell.success_criterion)
        if outcome.asr > best_asr:
            best_asr = outcome.asr
            best_payload = payload
        payload = mutate(
            payload,
            observations=outcome,
            strategy_pool=adaptive_strategies_for(cell, defence_stack)
        )
    return {"adaptive_asr": best_asr, "best_payload_id": store(best_payload)}
```

The mutation function is where the intelligence lives: it might be a hand-authored transformation set (paraphrase, translate, encode, insert-delimiter, argue-against-reminder), an LLM-in-the-loop mutator (mod-111 develops this), or a mixed strategy pool sampled by an outer bandit. The store call moves the winning payload into the external replay bundle under access control — *never* into this source repo.

## Elicitation gap

The **elicitation gap** — the concept borrowed from mod-101's discussion of RSP / Preparedness / FSF — applies to prompt injection as much as to dangerous-capability evaluation: your internal red-team will elicit worse behaviour than the surface adversary population *by definition*, because the internal red team is skilled and iterating. But an external adaptive attacker with time and motivation can *exceed* even the internal red-team's best effort.

The engineering discipline: report ASR against the internal red team as the current best-effort adversarial estimate; report a *widened* confidence interval acknowledging that a determined external attacker likely does better. When a mod-112 disclosure needs to defensibly claim a defence is robust, that widened CI is the honest number.

## Composition-under-adaptive-attack

Defence layers do not *compose linearly* under adaptive attack. An attacker who defeats layer 4 (self-refuting classifier) is often also positioned to defeat layer 3 (sandwich), because the same "argue against the reminder" strategy applies to both. The harness must therefore evaluate the *composed* adaptive ASR, not just the sum of per-layer adaptive deltas.

Two composition patterns to test:

- **Attacker-picks-easy** — the adaptive attacker chooses the payload that beats the *weakest* layer first, without regard to composed defence. Baseline; usually low ASR.
- **Attacker-picks-hard** — the adaptive attacker is told what layers are present and iterates against the composed stack. This is the number that matters.

Report both; the delta is a signal of whether the composition is *actually* helping.

## What findings look like

Adaptive-attack findings emitted from this module carry:

- **Cell** — the coverage-matrix cell.
- **Defence stack version** — every layer's version and hyper-parameters at the time.
- **Attacker budget** — iterations, wall-clock, whether human / scripted / LLM-in-loop.
- **Static ASR** and **adaptive ASR** — with a CI derived from run-to-run variance.
- **Gap** — adaptive minus static.
- **Sample payloads (by ID)** — pointers into the external replay bundle.
- **Root cause per attack** — which of the layer-specific strategies succeeded; feeds the defence-owner's remediation queue.

## Handoff to mod-108 and mod-111

- **mod-108 (Frontier-Scale Guardrails and Safety-Monitor Engineering)** — takes the classifier-layer findings and engineers Constitutional-Classifier-style scalable, robust replacements. This module measures; mod-108 engineers the replacement classifiers.
- **mod-111 (Automated and Scaled Red-Teaming)** — takes the scripted-attacker loop and industrialises it: LLM-vs-LLM attacker chains, fine-tuned adversarial attackers, StrongREJECT-style judges. This module builds the minimum-viable loop; mod-111 scales it.
- **mod-112 (Frontier-Safety Program and Disclosure)** — takes the adaptive-ASR numbers and routes disclosure. A defence claim in a system card, an internal safety case, or an AISI submission must cite the adaptive number, not the static one.

## Common engineering mistakes with adaptive stress-testing

- **Reporting only static ASR.** Static-only numbers overstate defence robustness. Every release must run the adaptive pass on at least Tier A cells.
- **Under-budgeting the attacker.** A 5-iteration attacker is not adaptive — it is a static evaluation with more variance. Adaptive attacker budgets are calibrated by observing where the ASR curve plateaus.
- **Using the same LLM for attacker and defender.** An attacker LLM that is a sibling of the defender may share blind spots. Use a different family and cross-check.
- **Not routing findings back to defence owners.** Every adaptive-ASR gap is a work item for the layer's owner. Track them.
- **Publishing the winning adaptive payloads inside the repo.** They belong to the external replay bundle. Winning adaptive payloads are the highest-leverage payloads and thus the most important to keep out of public checkouts.
- **Under-reporting attacker-side variance.** Two engineers with the same budget often find different payloads. Report per-engineer variance and take the max.

## Summary

- Adaptive ASR — the pass rate under an attacker who knows the defence and iterates — is the real robustness metric. Static ASR is a lower bound and usually a comfortable one.
- Every defence layer has a small playbook of adaptive strategies to always try before reporting robustness.
- A minimum-viable scripted adaptive-attack loop combines a starting payload set, an observation-driven mutator, an oracle judge, and a small compute budget. mod-111 industrialises the loop.
- Defence composition does not fold linearly under adaptive attack; measure composed adaptive ASR explicitly.
- Findings emit with cell, defence-stack version, attacker budget, static vs. adaptive ASR, and root-cause pointers. Payloads live in the external replay bundle.
- mod-108 engineers the classifier-layer replacements; mod-111 scales the attacker loop; mod-112 disclosure cites the adaptive number as the honest one.
