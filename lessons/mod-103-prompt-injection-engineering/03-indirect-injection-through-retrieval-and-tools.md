# 03 — Indirect Injection Through Retrieved Context and Tool Responses

## Motivation

Chapter 02 built the six-primitive vocabulary against the user-prompt channel. This chapter opens the *primary* channel for agent-scale prompt injection: **retrieval and tool responses**. The attacker never types into the operator's user prompt. Instead they place attacker-controlled text where the model will encounter it *as if it were data* — a webpage the agent browses, a PDF the agent parses, a snippet the RAG retriever pulled, a search-result title, a code-interpreter stdout, an external API response body — and the model then reads the payload as an instruction.

This is the channel Greshake et al. (2023) formalised as **indirect prompt injection** and the channel InjecAgent benchmarks systematically. Chapters 03 and 04 together enumerate the indirect channels; chapter 03 covers the two highest-volume sources — retrieved context and tool responses — and chapter 04 covers persistence (memory / vector stores) and cross-plugin channels.

## Primary sources for this chapter

- **Greshake, Kai et al., "Not what you've signed up for: Compromising Real-World LLM-Integrated Applications with Indirect Prompt Injection," AISec 2023.** The paper that named the class, enumerated the delivery vectors, and demonstrated end-to-end attacks against real integrations (Bing Chat, Google Bard prototypes). Read it once, cover to cover, before continuing this chapter.
- **InjecAgent benchmark (Zhan et al., 2024).** A benchmark of ~1,000 indirect-prompt-injection test cases across ~30 tool categories that measures both attack-success rate and the false-positive rate of defences. It is the closest public analogue to the coverage matrix chapter 06 builds. <!-- needs-research: confirm the exact case count and tool-category count in the currently published InjecAgent paper and the URL of the dataset release. -->
- **OWASP LLM01 (2025)**, sub-class *indirect*.
- **MITRE ATLAS `Prompt Injection` (AML.T0051)**, indirect procedure family.

## The channel taxonomy this chapter owns

For this chapter's scope — retrieval + tool responses — the channels are:

- **Retrieved context**
  - Webpages fetched by an agentic browser
  - PDFs / office documents parsed by a document-ingest tool
  - RAG snippets returned from a vector store
- **Tool responses**
  - Search-engine result snippets and titles
  - Code-interpreter stdout / stderr / files-written
  - External API response bodies (weather, calendar, ticketing, shopping, mail)

Chapter 04 continues the taxonomy with long-term memory / vector-store writes and cross-plugin (peer agent, MCP, A2A) channels. Together, chapters 03 and 04 populate the "indirect" rows of the PIEH coverage matrix (chapter 01).

Each channel needs its own coverage-matrix row because the *delivery mechanics*, *sanitisation contract*, and *plausible-defence surface* differ. A payload that hides in an HTML `<meta name="description">` tag is not the same finding as the same primitive delivered in a PDF's XMP metadata, even though both are "webpage / PDF injection."

## Retrieved context — webpages

**Delivery mechanics.** An agent browses the web (Puppeteer, headless Chrome, a browsing tool) and renders the fetched HTML into text (or into a DOM description) that the model then reads. Attacker-controlled text can enter through:

- **Body content** — the visible text of any page the agent visits.
- **Meta / hidden content** — `<meta>` descriptions, `hidden` inputs, elements positioned off-screen with `position:absolute; left:-9999px;`, `aria-label` and `title` attributes, CSS-hidden text, `<template>` fragments.
- **Alternative-text channels** — `alt` attributes on images, `srcset` descriptions, `<figcaption>` text.
- **Comments and metadata** — HTML comments, JSON-LD blocks, Open Graph metadata, RSS feed metadata.
- **Search-engine snippets** — the summary a search engine returned about the page, which may be attacker-controlled if the attacker controls the page's meta tags.
- **Third-party embeds** — iframes, widgets, ads, tracker fragments. The agent may render these transitively if the browser tool follows them.

**Primitives that ride this channel.** All six from chapter 02. Common combinations:

- **Instruction-override in an off-screen div.** The visible page looks benign; the model reads the DOM including the off-screen div.
- **Task-override in a `<meta>` description** that becomes the search snippet the agent reads before deciding to fetch.
- **Exfil-through-completion via image `alt` text** requesting the agent to render a specific markdown image URL in its reply.

**Sanitisation contract to look for (and often to find absent).**

- Rendering pipeline strips or normalises hidden CSS content.
- HTML comments and non-content metadata are discarded before the string reaches the model.
- Snippet from search engine is *labelled as attacker-controlled* before being concatenated into the context.
- URL fetches are gated by an allow-list; agents cannot browse arbitrary pages by default.

**Real-world grounding.** Greshake et al. demonstrate this exact class against Bing Chat: attacker-controlled webpages injected instructions into the chat when the search integration retrieved them. Read the paper's case-study section before authoring your own webpage-injection payloads.

## Retrieved context — PDFs and office documents

**Delivery mechanics.** An agent ingests a PDF (or DOCX, XLSX, ODT, PPT). A parser extracts text and hands the string to the model. Attacker-controlled text can enter through:

- **Body text** — the visible content, including footers, headers, columns, and marginalia.
- **Metadata** — Title, Author, Subject, Keywords, custom XMP entries.
- **Hidden objects** — text drawn in white on white, text off-page, text in unused OCG (optional content group) layers, invisible text rendered at 0-pt.
- **Structural fields** — form field default values, comments, annotations, revision history in tracked-changes formats.
- **Embedded objects** — attached files (PDF *can* have file attachments), embedded fonts whose names carry text, JavaScript in the PDF that the parser executes.
- **OCR fallback** — for scanned PDFs, the OCR text stream. Adversarial images (chapter 05, obfuscation) can shape what OCR emits.

**Primitives.** As above. PDFs are especially strong carriers for **role-hijack** (metadata often includes structured Author/Publisher fields the model reads as authority) and **task-override** (long documents allow the payload to be planted deep in the body where a summarising agent will still surface it).

**Sanitisation contract to look for.**

- Metadata is extracted separately and either dropped or labelled distinctly from body text.
- Hidden-text detection (colour = background, out-of-page) drops the offending regions.
- Non-content annotations (form defaults, comments) are labelled.
- Multi-modal ingestion (raster the page, run OCR) has an adversarial-image sanity pass.

## Retrieved context — RAG snippets

**Delivery mechanics.** The agent runs a similarity search against a vector store and the top-*k* results are formatted into the context, often with a snippet template like:

```
[SOURCE 1] <title>: <top passage>
[SOURCE 2] <title>: <top passage>
...
```

Attacker-controlled text can enter through:

- **Poisoned corpora** — the attacker uploaded a document to a shared knowledge base that was then embedded and indexed.
- **Embedding hijacking** — the attacker crafted a document whose embedding is close (in the retriever's space) to a common query, so it is retrieved for queries that have nothing to do with its topic.
- **Snippet-template injection** — the attacker's document contains text that mimics the snippet template's delimiters ("[SOURCE 3]:", "USER:", "SYSTEM:"), causing the model to read attacker text as though it were a different source or role.

**Primitives.** All six ride this channel, but the standout is **cross-tenant task-override**: an attacker who plants a payload in a shared corpus can affect every downstream user's session.

**Sanitisation contract to look for.**

- Corpus-write authority is restricted; user-supplied uploads carry a low trust label.
- Retrieved snippets are rendered with unambiguous delimiters *the attacker cannot spoof*.
- Snippets are truncated to a length that limits payload complexity.
- Anomaly detection on the embedding step flags documents that are close to many queries.

**Grounding.** Chapter 04 develops corpus poisoning further; this chapter treats corpus-write as a channel and moves on.

## Tool responses — search-engine result snippets

**Delivery mechanics.** The agent calls a search tool and the tool returns a list of `(title, snippet, url)` triples. The model reads the titles and snippets to decide which URL to fetch — and, importantly, may read a snippet as authoritative *without ever fetching the underlying page*.

**Where the payload lives.** In the page's `<meta name="description">` (which most search engines surface as the snippet), in the page title, in the URL itself (URL-encoded payload strings that the agent's client renders and reads), and — for search engines that expose additional structured data — in the result's rich-result JSON-LD.

**Primitives.** Instruction-override and task-override are the highest-hit rate here because a search snippet is exactly the token stream the model treats as "concise informative summary" — a natural spot to plant a directive framed as a fact.

**Sanitisation contract to look for.**

- Search-engine responses are labelled as untrusted before entering the model context.
- Titles and snippets are length-capped.
- URL rendering strips any structured payload from the query string.
- The agent does not act on snippet content without at least one corroborating fetch or user confirmation for high-stakes actions.

**Historical incident to read.** Greshake et al. §5 documents the Bing-Chat search-injection case. Frontier labs have since deployed mitigations; the *channel* has not gone away.

## Tool responses — code-interpreter stdout

**Delivery mechanics.** The agent has a code-execution tool (Python REPL, Jupyter kernel, Firecracker sandbox, `run_shell`). The tool returns stdout, stderr, and produced files. The model reads that output as data and often as a source of authority (the interpreter is treated as an oracle).

**Where the payload lives.**

- **In a fetched file** — the interpreter downloaded a webpage, unzipped an archive, or read a CSV whose row contains attacker text.
- **In an installed package** — a package's `__init__.py` prints attacker text at import; the model reads the stdout.
- **In an environment banner** — a shell prompt string that the payload author controls.
- **In error messages** — a triggered exception whose message contains attacker text.
- **In terminal control sequences** — ANSI escape codes can rewrite terminal display; text after a `\r` overwrites earlier text. If the raw stream reaches the model, control sequences can shape what the model reads. If the model reads a filtered view, only that filtered view is a channel.

**Primitives.** All six. Task-override is particularly potent because the code interpreter frames its output as a *trusted computed result*.

**Sanitisation contract to look for.**

- Interpreter output is labelled as untrusted before entering the context.
- Fetched files that could contain attacker text (any file whose provenance is user- or web-supplied) are read into a labelled zone.
- Control sequences are stripped or normalised.
- The interpreter runtime has an egress policy so an injected instruction cannot cause the interpreter to fetch attacker infrastructure.

## Tool responses — external API responses

**Delivery mechanics.** The agent calls an external API (weather, calendar, ticket lookup, e-commerce, mail read, CRM lookup) and the API's JSON response body enters the model context.

**Where the payload lives.**

- **User-controlled fields** — a support-ticket `title` or `description` the requester wrote, a calendar-invite `subject` the invitee sent, a product `review` a shopper posted, an email `body` the sender authored, a CRM `notes` field a customer edited.
- **Machine-emitted fields** — sometimes benign; sometimes the API itself is compromised (supply-chain attack) or is a wrapper the attacker maintains.
- **Error responses** — an attacker who triggers an API error whose message includes their controlled input.
- **Redirects and pagination** — the API returned a next-page URL or a redirect; the agent's client will follow it, expanding the attacker-controlled surface transitively.

**Primitives.** All six. **Exfiltration-through-completion** is unusually rich here because the API is often a two-way channel — the attacker's payload can instruct the agent to *write back* to the API with data drawn from other tool responses.

**Sanitisation contract to look for.**

- Per-field trust labels — the `description` field of a ticket is user-supplied and untrusted; the `ticket_id` is machine-supplied.
- The agent's tool wrapper marks user-supplied fields with a distinct provenance label before rendering.
- Structured-response fields are not concatenated flatly into a prose blob — they are rendered as key-value pairs the model can attend to distinctly.
- Egress checks on any downstream tool call whose arguments were derived from an API response.

## InjecAgent as the current benchmark

InjecAgent (Zhan et al., 2024) is the canonical public benchmark for exactly this chapter's scope: indirect prompt injection through tool responses in agentic workflows. It classifies attacks into (a) **direct-harm attacks** (the injected instruction causes the agent to invoke a harmful tool directly, e.g., "transfer $100 to attacker") and (b) **data-stealing attacks** (the injected instruction causes the agent to leak information through subsequent tool calls). The benchmark reports both success rate and the false-positive rate of a candidate defence.

For your PIEH coverage matrix (chapter 06), InjecAgent is a starting point, not a substitute — its scenarios are representative but not exhaustive of the channels your target agent actually exposes. Cite the benchmark and pin the version. <!-- needs-research: confirm the current InjecAgent release URL, version tag, and citation format. -->

## Common engineering mistakes at the retrieval + tool-response boundary

- **Testing the payload only in the "happy path" retrieval.** The payload rides through only when the retriever ranks the poisoned document highly. Chapter 06 forces adversarial-retrieval evaluation.
- **Failing to distinguish payload delivery from payload effect.** If the model *reads* the payload but does not act on it, that is a defence, not a failed delivery. Emit a "delivery observed / effect suppressed" finding, because payload delivery is the load-bearing pre-condition for adaptive attacks (chapter 08).
- **Ignoring the tool-response wrapper.** A frontier lab's tool response is not "raw" — it is wrapped in a template, may be sanitised, and may have provenance labels the operator's chat template does or does not preserve. The wrapper is part of the surface and part of the reproducibility bundle.
- **Not enumerating structured-response fields per-tool.** Every tool has its own field schema; the coverage matrix has one row per tool per field of interest, not one row per tool.
- **Assuming the tool response is machine-emitted.** The most common surprise is that the tool response *is* the user-supplied data of the *previous* user (a ticket description, a review body, an email body) — attacker-controlled and just as untrusted as a webpage.

## Summary

- The primary indirect-injection channels for agents are **retrieved context** (webpages, PDFs, RAG snippets) and **tool responses** (search results, code-interpreter output, external API responses). Every payload enters through a specific sub-channel of one of these.
- Greshake et al. (2023) is the canonical grounding; InjecAgent is the canonical benchmark; OWASP LLM01 (indirect) and MITRE ATLAS AML.T0051 are the mandatory labels on every finding.
- Each channel has its own sanitisation contract; each contract's absence is a finding on its own.
- The six primitives from chapter 02 map onto each channel — the coverage matrix has one cell per (primitive × channel) pair.
- Chapter 04 continues the taxonomy with **long-term memory** and **cross-plugin** channels; chapter 05 layers obfuscation across every channel; chapter 06 assembles them into the harness.
