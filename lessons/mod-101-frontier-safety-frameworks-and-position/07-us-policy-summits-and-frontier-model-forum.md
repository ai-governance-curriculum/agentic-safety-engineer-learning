# 07 — US Policy, Summit Outcomes, and the Frontier Model Forum

## Motivation

Outside the EU, frontier-AI governance is currently structured around **voluntary commitments**, **international summit outcomes**, and **industry-body coordination** rather than binding regulation. That does not make it optional reading for a safety engineer — the artifacts you produce feed into US-flavoured pre-deployment evaluation windows, Bletchley / Seoul / Paris summit outcomes, Frontier Model Forum working papers, and (until recently) the reporting infrastructure set up under US Executive Order 14110.

Reading this policy layer as an engineer means: know which commitments the labs have publicly signed, know which reporting infrastructure is currently in force, and know which industry body owns which coordination lane.

## Primary source

- **[Executive Order 14110 (Oct 2023) — full text (Federal Register)](https://www.federalregister.gov/documents/2023/11/01/2023-24283/safe-secure-and-trustworthy-development-and-use-of-artificial-intelligence)** — the Biden-era EO on safe, secure, and trustworthy AI, with the dual-use foundation model reporting provisions.
- **[Executive Order 14179 (Jan 2025) — Removing Barriers to American Leadership in AI](https://www.federalregister.gov/documents/2025/01/31/2025-02172/removing-barriers-to-american-leadership-in-artificial-intelligence)** — the Trump-era EO revoking EO 14110 and directing a new AI action plan.
- **[White House AI Action Plan (July 2025)](https://www.whitehouse.gov/ai/)** — the successor policy document referenced by EO 14179's implementation.
- **[Bletchley Declaration (Nov 2023)](https://www.gov.uk/government/publications/ai-safety-summit-2023-the-bletchley-declaration/the-bletchley-declaration-by-countries-attending-the-ai-safety-summit-1-2-november-2023)** — declaration from the AI Safety Summit at Bletchley Park.
- **[Frontier AI Safety Commitments (May 2024, Seoul)](https://www.gov.uk/government/publications/frontier-ai-safety-commitments-ai-seoul-summit-2024/frontier-ai-safety-commitments-ai-seoul-summit-2024)** — voluntary commitments signed by frontier-model providers at the AI Seoul Summit.
- **[Seoul Declaration (May 2024)](https://www.gov.uk/government/publications/seoul-declaration-for-safe-innovative-and-inclusive-ai-ai-seoul-summit-2024)** — inter-government declaration from the AI Seoul Summit.
- **[Frontier Model Forum](https://www.frontiermodelforum.org/)** — industry body of frontier-model developers (founded July 2023 by Anthropic, Google, Microsoft, OpenAI; more members since).
- **[Frontier Model Forum publications](https://www.frontiermodelforum.org/updates/)** — the working-papers and issue-briefs feed.

<!-- needs-research: cross-check current US policy state under the White House AI Action Plan and any subsequent EOs / OMB memos; confirm the current Frontier Model Forum membership, publication list, and working-group activity. Also confirm Paris AI Action Summit (Feb 2025) outcomes and whether they should be cited here alongside Bletchley and Seoul. -->

## Core concepts

### US Executive Order 14110 (October 2023, subsequently rescinded)

EO 14110, signed October 30, 2023 by President Biden, was the most substantial US executive action on AI safety to that date. It directed federal agencies to act on AI safety across numerous domains and — most relevantly for frontier-safety engineers — invoked the **Defense Production Act** to require reporting from developers of certain **dual-use foundation models** (defined by scale of training compute) about their capabilities, safety measures, and red-team results.

The EO also directed the establishment of the **US AI Safety Institute** within NIST (see chapter 08) and set expectations for pre-deployment red-team testing coordination.

EO 14110 was **rescinded** by EO 14179 (**January 20, 2025**), which directed a new US AI action plan. The rescission changes the *policy scaffolding* but does not remove the fact that:

- Labs completed reports and pre-deployment evaluations under EO 14110 during 2023–2024; those artifacts are historical record and continue to inform current commitments.
- The US AI Safety Institute infrastructure created under EO 14110 continued to exist inside NIST, though its scope and priorities have shifted under the successor policy.
- Many of the *voluntary commitments* the labs made (see Seoul commitments below) predate and outlast EO 14110.

<!-- needs-research: pull the current state of the US AI Safety Institute under the White House AI Action Plan and any successor EOs; sync the summary to the actual current infrastructure name and scope. -->

For a safety engineer, EO 14110 is now primarily useful as:

- **Historical record** — the reports labs filed under it are cited in system cards and safety cases from that period.
- **Policy vocabulary** — the "dual-use foundation model" and "safety test results" terminology it introduced continues to circulate.
- **Contrast** — reading EO 14110 alongside its January 2025 rescission is a compact case study in how quickly the US policy scaffolding can move.

### Bletchley Declaration (November 2023)

The **AI Safety Summit** held at Bletchley Park (November 1–2, 2023) produced the **Bletchley Declaration**, a joint statement from participating countries recognising the opportunities and risks of frontier AI and committing to international cooperation on frontier-AI safety.

The Declaration itself is short and largely aspirational — its practical significance is that it:

- Established the *summit series* (Bletchley → Seoul → Paris → future).
- Created diplomatic space for AI Safety Institutes to coordinate cross-jurisdiction.
- Set the shared vocabulary ("frontier AI", "safety") that later summit outcomes and voluntary commitments built on.

For an engineer, the Bletchley Declaration is context: it explains why the AISI coordination lane (chapter 08) exists.

### Seoul Statement of Intent and Frontier AI Safety Commitments (May 2024)

The **AI Seoul Summit** (May 21–22, 2024) produced two useful documents:

- The **Seoul Declaration** — the inter-government statement, deepening the Bletchley Declaration's commitments.
- The **Frontier AI Safety Commitments** — a set of voluntary commitments signed by frontier-model providers, committing signatories to publish safety frameworks, define severe-risk thresholds, evaluate against them, and take specific mitigation and disclosure actions.

The Seoul commitments are load-bearing for a safety engineer because they are the *industry-side* commitment layer that RSP / Preparedness / FSF *operationalise*. When you author a safety-framework artifact, the Seoul commitments are the reference class the artifact is being measured against.

The list of signatories, and the specific text of the commitments, are published on the UK government hub cited above.

<!-- needs-research: confirm the current signatory list of the Frontier AI Safety Commitments and any subsequent amendments; also note any equivalent commitments made at the Paris AI Action Summit (Feb 2025) and the AI Impact Summit (India 2026). -->

### Frontier Model Forum

The **Frontier Model Forum** (FMF) is an industry body founded in July 2023 by Anthropic, Google, Microsoft, and OpenAI. Its stated mission is to advance frontier AI safety research, identify safety best practices, share information with governments and civil society, and support applications to address the world's greatest challenges.

The FMF publishes:

- **Working papers** — issue briefs and technical papers on frontier-safety topics (bio threat models, model reporting, evaluation methodology).
- **Issue briefs** — shorter policy-shaped documents.
- **Announcements** — membership, executive-director changes, initiative launches.

For a safety engineer, FMF publications matter because they are:

- The **most-cited industry-body technical resource** on frontier-safety topics like the bio threat model. Your dangerous-capability evaluation methodology (mod-106) will reference FMF working papers.
- A **cross-lab coordination lane** — where a single lab's framework language gets translated into a language multiple labs can agree on.
- A **regulator-and-AISI-facing publication channel** — governments read FMF publications when they draft policy or draft AISI evaluation methodology.

### The policy layer as a system

At the risk of over-simplifying, the layer looks like:

- **International summits** (Bletchley, Seoul, Paris, subsequent) — set the diplomatic space and the voluntary-commitment reference class.
- **National governments** (US, UK, EU, and others) — turn commitments into national policy, either through EOs, statutes, or regulatory guidance.
- **AI Safety Institutes** (UK AISI, US AISI/NIST, others; chapter 08) — build the technical evaluation methodology under those national policies.
- **Industry bodies** (Frontier Model Forum, Partnership on AI) — coordinate cross-lab positions and publish shared technical resources.
- **Frontier labs** (Anthropic, OpenAI, Google DeepMind, others) — implement RSP / Preparedness / FSF and populate the shared resources.

The safety engineer sits at the lab layer and produces artifacts that flow up into all of the above.

## Reading the US + summit + FMF layer as an engineer — a worked example

Assume you have completed a bio-uplift evaluation on a new frontier model.

For this layer, you would:

1. **Check the current US policy scaffolding.** Which reporting lane is currently in force? (This changes based on current administration policy — read the current state.)
2. **Cross-cite the Seoul Frontier AI Safety Commitments.** Your provider is (likely) a signatory. The eval methodology and result populate the signatory's commitment to defined-thresholds evaluation. Reference the commitment in your eval report.
3. **Publish or contribute to a Frontier Model Forum publication.** If the eval methodology is novel or shared, coordinate with the FMF working-group lane so the methodology becomes shared industry resource.
4. **Submit to AISI windows.** Chapter 08 covers the pre-deployment evaluation lane; the artifact you produce lands there in parallel.
5. **Feed the safety case.** The Seoul commitments, the FMF papers, and the current US reporting posture all feed into the deferrals-and-context section of your safety case.

## Engineering-artifact map

| Artifact | Purpose | Where authored |
|---|---|---|
| Voluntary-commitment mapping note | Which Seoul commitment(s) each eval / mitigation populates. | mod-101 depth. |
| FMF working-paper contribution | Shared methodology publication where the eval / mitigation methodology is novel or cross-lab. | mod-112 depth. |
| US policy-scaffolding note | Current state of US reporting obligations; feeds the deferral contract and safety-case context. | mod-101 + mod-112. |
| Summit-commitment cross-reference | For a safety case, cross-reference the Seoul commitments the case discharges. | mod-109 + mod-112. |

## Common misreadings to avoid

- **Assuming EO 14110 is still in force.** It was rescinded January 2025. Read the current White House AI Action Plan and successor EOs.
- **Assuming voluntary commitments have no bite.** Signatories publicly staked on the Seoul commitments — a signatory that misses a commitment carries reputational and, increasingly, regulatory consequence.
- **Treating FMF publications as marketing.** They are technical publications with genuine methodology content; read them as such.
- **Ignoring the summit series.** Each summit produces both diplomatic outcomes and industry-side commitments; keep reading the current one.
- **Missing the Paris and subsequent summits.** As of the current writing, the summit series continues; read each summit's outcomes.

<!-- needs-research: pull outcomes from the Paris AI Action Summit (Feb 2025) and any subsequent summit; cite the most recent outcomes explicitly. -->

## Summary

- The US policy layer moved fast: EO 14110 (Oct 2023) established federal AI-safety scaffolding, then was rescinded by EO 14179 (Jan 2025) and superseded by the White House AI Action Plan.
- The international layer runs on summit outcomes: Bletchley Declaration (Nov 2023), Seoul Declaration and Frontier AI Safety Commitments (May 2024), Paris and subsequent summits.
- The Frontier Model Forum is the industry-body coordination lane; its working papers are the most-cited shared technical resource on frontier-safety methodology.
- The safety engineer sits at the lab layer and produces artifacts that populate voluntary commitments, industry-body publications, and (where in force) national reporting lanes.
- Read the *current* state of each layer before authoring; the policy scaffolding can move quickly.
