# 01 — Prompt-Injection as an Engineering Discipline

## Motivation

Prompt injection is the single most-cited failure mode in agentic-system security writing, and the single most *misused* term. This chapter names the discipline the module builds, draws the boundary against the adjacent term "jailbreak" (mod-104), and pins the mapping to the two overlays that anchor the rest of the module: **OWASP Top 10 for LLM Applications LLM01 (Prompt Injection)** and **MITRE ATLAS `Prompt Injection` (AML.T0051)**.

Read this chapter first even if you already write injection payloads by intuition. The subsequent chapters will assume that when you say "direct injection" you mean the specific primitive family in chapter 02, when you say "indirect injection" you mean the specific channel taxonomy in chapters 03–04, and when you say "coverage" you mean the coverage-matrix contract in chapter 06.

## What prompt injection is — and what it is not

**Prompt injection is a class of attack in which adversary-controlled text, delivered through *any* channel the model reads, causes the model to follow instructions the operator did not intend.** The primitive is *instruction confusion between principals*: the model conflates a string that arrived from the operator (system prompt, developer prompt) with a string that arrived from an attacker (user prompt, retrieved document, tool response, memory, peer agent).

It is emphatically **not**:

- **A jailbreak.** A jailbreak is a class of attack in which adversary-controlled text causes the model to violate its own safety training — output that its aligned policy would otherwise refuse. Jailbreak is about *policy override*; injection is about *principal confusion*. The two often co-occur (an injected instruction can carry a jailbreak payload), but their threat models, defences, and evaluation harnesses diverge. Mod-104 covers jailbreak engineering; this module cites the boundary and moves on.
- **A safety-refusal failure.** If the model correctly identifies the injected instruction and refuses to follow it, no injection has occurred — the surface exists, but the attack failed. Refusal is a *defence*, not evidence that the surface is closed.
- **A generic hallucination.** A hallucinated tool call caused by an underspecified user prompt is not injection; there is no adversary-controlled string. If your incident report cannot name *the string* and *the channel it entered through*, it is not an injection incident.

The load-bearing definitional element is **principal confusion mediated by a channel**. Every injection you engineer in this module will name (a) the intended principal boundary, (b) the channel that violated it, and (c) the operator instruction that was overridden.

## Two overlays that anchor the discipline

The module's threat labels come from two published sources. Every finding you emit from a mod-103 exercise will carry at least one label from each.

### OWASP LLM01 — Prompt Injection (2025 edition)

The OWASP Top 10 for LLM Applications names Prompt Injection as **LLM01**, its highest-ranked risk. The 2025 revision splits injection into two sub-classes that we adopt verbatim:

- **Direct prompt injection** — adversary-controlled text enters through the user prompt channel (a chat message, a form field, an API request). The attacker is the user of the system or a party impersonating the user.
- **Indirect prompt injection** — adversary-controlled text enters through any *other* channel the model reads: retrieved documents, tool responses, memory recalls, environment observations, peer-agent messages. The attacker never types into the operator's chat box.

<!-- needs-research: pin the exact OWASP LLM01 sub-class enumeration and mitigation list against the currently published 2025 edition; the OWASP text has iterated across drafts. -->

Read the [OWASP LLM Top 10 project page](https://owasp.org/www-project-top-10-for-large-language-model-applications/) once before continuing this chapter. Every finding rubric in this module cites the LLM01 sub-class and, where the finding also touches sensitive-information disclosure or excessive agency, the adjacent LLM item too.

### MITRE ATLAS — Prompt Injection (AML.T0051)

The **MITRE Adversarial Threat Landscape for AI Systems (ATLAS)** matrix names Prompt Injection as technique **AML.T0051** under the *ML Attack Staging* / *Initial Access* tactics depending on the ATLAS version you cite. The ATLAS technique page enumerates procedures (with links to real incidents) and defensive mitigations.

<!-- needs-research: confirm the current ATLAS technique ID for prompt injection and the parent tactic(s); ATLAS has churned IDs across versions and the surrounding technique family (LLM Plugin Compromise, LLM Jailbreak, LLM Meta Prompt Extraction) has moved around it. -->

Read the [MITRE ATLAS matrix](https://atlas.mitre.org/) once before continuing. Every red-team finding you emit will reference the ATLAS technique ID *and* pin the ATLAS version, because the IDs drift.

The two overlays overlap in coverage but disagree in framing. OWASP frames LLM01 as an *application-security risk category* with mitigations; ATLAS frames prompt injection as an *adversary technique* with procedures. Emit both labels — they route to different reviewers.

## The six-primitive taxonomy this module owns

Direct injection is not one attack — it is a family. This module teaches six primitives, each with its own signature, its own defence, and its own coverage-matrix cell:

1. **Instruction-override** — a payload that tells the model to disregard prior instructions and follow the attacker's instead. The canonical "ignore previous instructions" pattern and its many descendants.
2. **Refusal-bypass** — a payload that argues the model out of a specific refusal it would otherwise emit (roleplay framing, hypothetical framing, translation framing, "grandma exploit," DAN-style personas). Overlaps with jailbreak (mod-104); the injection variant is the one delivered through non-user channels.
3. **Role-hijack** — a payload that convinces the model it is now a different assistant, a different persona, or under a different system prompt. Distinct from refusal-bypass in that it targets the model's *identity*, not any single refusal.
4. **Task-override** — a payload that swaps the user's task for the attacker's task while preserving the appearance of doing the user's work (silent task swap in agents; the model does the attacker's task and reports success to the user).
5. **Exfiltration-through-completion** — a payload that turns the completion channel itself into an exfil channel: the model is instructed to encode secrets in its output (base64 in a JSON field, first-letters of a poem, a chosen URL query string) that the attacker later reads via an external side channel.
6. **Output-format hijack** — a payload that manipulates the model's output structure (JSON schema, XML, code fences) to break downstream parsers, inject markdown that renders as clickable exfiltration links, embed HTML that runs in a downstream chat renderer, or produce structured output the tool runtime executes without validation.

Chapter 02 develops each primitive with concrete transcripts, the OWASP + ATLAS labels it carries, and the surface × persona cells it lands on. Every direct-injection payload you emit in exercise 01 will name one or more of these six.

## The direct-vs-indirect axis in agent settings

For a chat-only LLM, the direct-vs-indirect split matters mostly as an academic distinction — the attacker still ends up typing into the chat box either way. For a tool-using agent, the split is *load-bearing* because indirect injection is the primary channel:

- The attacker who controls a webpage, a support ticket, a PDF, a search-result snippet, an npm package README, a GitHub issue title, an email footer, a calendar-invite description, a shared document's comments, an image's OCR-recognisable text, or a peer-agent's message can inject **without ever speaking to the operator's user**.
- The blast radius of one poisoned document scales with every retrieval — a corpus poisoning affects every downstream user of the RAG index.
- The provenance chain from "adversary posted" to "model followed instructions" runs through infrastructure the operator does not fully control (search engines, third-party SaaS, MCP servers).

Chapters 03 and 04 develop indirect injection into a full channel taxonomy. Greshake et al. (2023) is the canonical grounding paper; InjecAgent is the canonical benchmark. Neither exists in the level-25 prerequisite; both are required reading before chapters 03–04.

## The evaluation-harness contract this module produces

The engineering artefact you author from this module is a **Prompt-Injection Evaluation Harness (PIEH)** for one concrete agent deployment. Its skeleton — filled in across chapters 06–08 and exercises 04–05:

```yaml
pieh:
  target: acme-support-agent-v1.2
  atmd_reference: <link to mod-102 artefact>
  coverage_matrix:
    direct:
      instruction_override: {payloads: [...], success_criteria: [...]}
      refusal_bypass:       {...}
      role_hijack:          {...}
      task_override:        {...}
      exfil_through_completion: {...}
      output_format_hijack: {...}
    indirect:
      retrieved_context:
        webpage:      {...}
        pdf:          {...}
        rag_snippet:  {...}
      tool_response:
        search_result:      {...}
        code_interpreter:   {...}
        external_api:       {...}
      long_term_memory:     {...}
      cross_plugin:         {...}
    obfuscation:
      unicode_confusables:       {...}
      homoglyph_substitution:    {...}
      ascii_art:                 {...}
      base64_rot13_cipher:       {...}
      low_resource_language:     {...}
      invisible_characters:      {...}
      tool_response_html:        {...}
  replay_bundle:
    location: <external-payload-store>  # see mod-111 harmful-payload discipline
    manifest:  <checksum + signature>
  reproducibility_bundle:
    model_ids_pinned:  [...]
    decoding_config:   {...}
    tool_manifest:     {...}
    prompt_scaffold:   {...}
    seed_ranges:       [...]
  defence_catalogue:
    spotlighting:            {applied_at: [...], stress_results: [...]}
    struq:                   {...}
    sandwich_prompting:      {...}
    self_refuting_classifier:{...}
    tool_response_sanitisation: {...}
  boundary_to_ai_eval_engineer:
    consumes:
      - app_side_traces:  <link to eval-engineer trace schema>
      - judge_plumbing:   <link to eval-engineer judge harness>
    provides:
      - injection_findings_feed: <schema>
      - regression_fixtures:     <schema>
```

Every field derives from one of the eight remaining chapters or from an exercise. If a field is empty in your PIEH, name the chapter that fills it.

## Harmful-payload discipline (previewing chapter 06)

Some payloads in this module *work* — they elicit the behaviour they claim to elicit against production frontier models. That is the point of a red-team artefact and also the reason for the storage rule this module inherits from mod-111:

- **The evaluation set — the actual payload strings — lives outside this source repo.** It is stored in an access-controlled artefact store (S3 bucket, dedicated internal repo, signed tarball on an evaluation server) with a manifest committed here.
- **The chapters and exercises reference payload *shapes* and *categories*, not literal weaponised strings.** Illustrative snippets in this module are truncated, defanged, or demonstrably safe.
- **Access to the payload store is logged.** Reviewers who need to run the harness receive scoped credentials; the credentials do not live in a checkout.

Chapter 06 develops the storage layout, the manifest format, and the review process. Exercise 04 exercises it against a real harness. If you catch yourself pasting a working payload into this repo or into any public artefact, stop.

## Common misreadings to avoid

- **"Prompt injection is a solved problem — we filter the input."** Filtering the *user* prompt closes maybe 5 % of the attack surface for a tool-using agent. Every other channel — retrieval, tool response, memory, environment, cross-agent — routes around the filter by construction.
- **"Prompt injection and jailbreak are the same thing."** No. Jailbreak targets the *policy*, injection targets the *principal*. A properly-refused jailbreak payload is not a successful injection; an executed benign attacker-instruction *is* a successful injection even if nothing unsafe was said.
- **"We don't have this problem — our agent has a strong system prompt."** System-prompt hardening is one control among many; it does not survive an indirect payload whose delivery channel places attacker-controlled text *after* the system prompt in the model's context. Chapter 07 covers system-prompt hardening and its limits.
- **"Our vendor guardrails cover it."** Vendor guardrails are a defence *layer*, not a defence *complete*. Chapter 07 treats them as one row in the defence catalogue and chapter 08 stress-tests them against adaptive attacks.
- **"We can wait to build the eval harness until we have a fix."** No. Without a harness you cannot measure whether any fix works, whether it regresses under model swaps, or whether an adaptive attack circumvents it. Chapter 06 builds the harness *before* chapter 07 builds defences for exactly this reason.

## Summary

- Prompt injection is *principal confusion mediated by a channel*, not a policy failure and not a synonym for jailbreak. Every finding names the channel and the overridden operator instruction.
- The module anchors its threat labels in **OWASP LLM01** (application-security risk category) and **MITRE ATLAS `Prompt Injection` / AML.T0051** (adversary technique). Every emitted finding carries both.
- Direct injection is a six-primitive family: instruction-override, refusal-bypass, role-hijack, task-override, exfiltration-through-completion, output-format hijack. Indirect injection routes those primitives through non-user channels.
- The engineering artefact this module produces is a **Prompt-Injection Evaluation Harness (PIEH)** with a coverage matrix, a replay bundle, and a reproducibility bundle — with the payload store held outside this repo per mod-111 harmful-payload discipline.
- The remaining eight chapters populate the PIEH; the five exercises exercise it against a concrete target agent.
