# 08 — AISI Pre-deployment Testing Methodology

## Motivation

**AI Safety Institutes** — most prominently the UK AI Safety Institute (UK AISI, later renamed AI Security Institute) and the US AI Safety Institute inside NIST — are the government-side technical bodies that evaluate frontier models before deployment. If you work at a frontier lab, at some point you will be inside an **AISI pre-deployment evaluation window**: the AISI's evaluators get scoped access to your model, run their evaluation battery, and publish findings that feed into both your public system card and the government's policy posture.

Reading AISI methodology as an engineer means: know what an AISI evaluation looks like end-to-end, know how their published pre-deployment reports are structured, and know how to prepare your lab-side artifacts so the window runs smoothly rather than adversarially.

## Primary source

- **[UK AI Safety Institute / AI Security Institute](https://www.aisi.gov.uk/)** — hub, published research, and published pre-deployment evaluations.
- **[Inspect — UK AISI evaluation harness](https://inspect.aisi.org.uk/)** — the open-source evaluation framework AISI uses and publishes.
- **[UK AISI pre-deployment evaluation of Claude 3.5 Sonnet (upgraded)](https://www.aisi.gov.uk/work/pre-deployment-evaluation-of-anthropics-upgraded-claude-3-5-sonnet)** — a published example pre-deployment evaluation.
- **[UK AISI research](https://www.aisi.gov.uk/research)** — the published research and evaluation catalog.
- **[US AI Safety Institute (NIST)](https://www.nist.gov/aisi)** — the US AISI hub.
- **[NIST AI 100-2 (Adversarial ML Taxonomy)](https://csrc.nist.gov/publications/detail/nistir/8269/draft)** — the taxonomy the US AISI methodology anchors to.
- **[NIST GenAI](https://ai-challenges.nist.gov/genai)** — the NIST GenAI evaluation program.

<!-- needs-research: pull the current AISI name (renaming to AI Security Institute occurred in Feb 2025 in the UK), current published pre-deployment evaluations, and current US AISI posture under successor US policy. Sync the summary to the current state. -->

## Core concepts

### What an AISI evaluation is

An AISI **pre-deployment evaluation** is a structured engagement between the AISI's evaluators and a frontier-lab provider, in a defined window before public deployment of a new frontier model. Broad shape:

1. **Access negotiation.** The AISI is granted scoped access to the model — typically the pre-deployment version, with defined access levels (API access with elevated rate limits, sometimes fine-tuning access, sometimes model-internals access; access levels are negotiated).
2. **Threat-model scoping.** The AISI and the provider agree on which capability dimensions and risks the evaluation covers.
3. **Evaluation execution.** The AISI runs its evaluation battery — dangerous-capability evaluations, red-team probes, agentic-behavior evaluations, safeguard-effectiveness probes.
4. **Findings package.** The AISI produces a report of findings.
5. **Response and remediation loop.** The provider responds; where appropriate, mitigations are implemented before deployment.
6. **Publication.** The AISI publishes a public-facing version of the findings; the provider often publishes an aligned system card.

For a safety engineer at the lab, the important surfaces are (2), (3), and (5): scoping, evaluation execution, and remediation.

### The Inspect harness

The UK AISI publishes and maintains **Inspect**, an open-source evaluation harness written in Python. Inspect is the primary evaluation framework the AISI's teams run their evaluations on, and it is designed to be usable by external labs, academic researchers, and other AISIs.

For an engineer, Inspect matters because:

- Reusing Inspect for your internal evaluations means your evaluation artifacts speak the same schema as the AISI's — the port cost during a pre-deployment window drops substantially.
- Inspect solvers, scorers, tools, and datasets are open-source; the AISI's published evaluations are increasingly shipping in Inspect format.
- Inspect is one of the harnesses this track uses (mod-111 covers PyRIT + garak + Inspect + Promptfoo orchestration).

Concretely, when you author an evaluation:

- Prefer Inspect for evaluations you expect to submit for AISI review.
- Use Inspect's `Task`, `Solver`, `Scorer`, `Tool`, and `Dataset` primitives.
- Log with Inspect's logging schema so results are legible to the AISI.

### The published pre-deployment report shape

The UK AISI has published pre-deployment evaluations (see the Claude 3.5 Sonnet upgraded evaluation cited above, and subsequent published evaluations). The published-report shape includes:

- **Scope.** Which model, which access level, which time window, which evaluation categories.
- **Methodology.** For each evaluation category, the tasks used, sampling and elicitation methodology, grader methodology, and thresholds.
- **Findings.** Quantitative results per task, qualitative observations, and specific concern items.
- **Limitations.** Explicit statement of what the evaluation did *not* cover, and elicitation-gap acknowledgement.

For an engineer preparing artifacts on the lab side, the point is: **produce your own report in the same shape.** Your pre-deployment eval report should read like a lab-side counterpart to an AISI-published report, so the interfaces line up.

### Access levels and scoping

AISI access to a pre-deployment model is scoped. Broadly:

- **API-level access with elevated privileges** — no rate limit for evaluators, ability to run large N samples, sometimes fine-tuning access.
- **Model-internals access** — activations, hidden states, or gradient access for specific evaluations (e.g., interpretability-touching probes, whitebox jailbreak methods). This is uncommon and negotiated case-by-case.
- **Deployment-stack access** — access to production classifier stack, monitoring stack, or other deployment-side infrastructure.

For a safety engineer, the *access-negotiation* stage is where your framework artifacts land: the RSP tier / Preparedness category / FSF CCL evidence you have accumulated determines what the AISI is expected to focus on and what access it needs.

### US AISI (NIST) methodology

The US AI Safety Institute inside NIST operates under different infrastructure than the UK AISI. It leans more heavily on NIST's standards-development posture — anchoring evaluation methodology to the **NIST AI RMF**, **NIST AI 100-2 (Adversarial ML Taxonomy)**, and NIST GenAI program deliverables.

<!-- needs-research: pull current US AISI status under successor US policy (post EO 14179), including any renaming, scope changes, or MOUs with labs. -->

Where UK AISI has an explicit *pre-deployment evaluation program* with a public publication cadence, the US AISI has evolved a mix of standards-development activity and evaluation methodology publication. For the engineer, the lesson is: read the current US AISI charter and program posture before you assume its lane matches the UK AISI's.

### Coordination across AISIs and with the AI Office

AISIs coordinate — there is a stated commitment (dating to Bletchley / Seoul) to cross-AISI cooperation on evaluation methodology and information sharing. The EU AI Office (chapter 06) sits alongside AISIs as the regulatory-side body. For a safety engineer:

- One well-authored evaluation artifact can flow into a UK AISI window, a US AISI submission, and an EU AI Office Code of Practice model report.
- The methodology description in the artifact should be self-contained enough that different reviewers, using different harnesses and evaluation batteries, can rerun and cross-check.

## Reading AISI methodology as an engineer — a worked example

Assume you are preparing your lab's next frontier model for a UK AISI pre-deployment evaluation window.

You would:

1. **Publish your own pre-deployment report first, internally.** Elicitation methodology, results per category, mitigation-effectiveness measurements, red-team findings.
2. **Port your evaluations to Inspect** where they are not already there. Make sure your internal `Task`, `Solver`, `Scorer` code follows Inspect conventions.
3. **Prepare a scoping document** for the window — proposed access level, proposed categories, proposed timeline, evidence you have already gathered.
4. **Rehearse the evaluator's likely battery.** The AISI's evaluation battery is not fully public — but the categories are (autonomy, cyber, CBRN, safeguard-effectiveness). Anticipate them; run your own version first so nothing on the report is surprising.
5. **Author the remediation lane** — where the AISI is likely to find something, know in advance what you would ship as a mitigation and how quickly.
6. **Prepare the disclosure.** The AISI publishes a version of the findings; your system card should be co-authored against that report.

## Engineering-artifact map

| Artifact | Purpose | Where authored |
|---|---|---|
| AISI-shape pre-deployment eval report | Internal lab-side report in the same shape as an AISI-published report. | mod-106, mod-108, mod-110, mod-112 depth. |
| Inspect-format task definitions | Reusable Inspect solvers / scorers / datasets for your evaluation battery. | mod-111 depth. |
| Access-negotiation scoping doc | Proposed scope, categories, access level, timeline for the window. | mod-101 + mod-112 depth. |
| Remediation-lane playbook | Pre-authored mitigation options keyed to likely findings. | mod-107 + mod-108 + mod-112. |
| Co-authored system card | Public disclosure aligned to the AISI-published findings. | mod-112. |

## Common misreadings to avoid

- **Treating the AISI evaluation as adversarial.** It is a *cooperative* evaluation window with an explicitly-scoped access agreement. Adversarial posture wastes the window and burns trust with a body you will interact with repeatedly.
- **Skipping the elicitation-gap discipline.** If your internal evaluation under-elicits and the AISI's evaluators use stronger elicitation, they will find capability you missed; your remediation lane will not be pre-authored and you will ship rushed mitigations.
- **Reusing bespoke evaluation code that is unreadable outside your lab.** Use Inspect for anything you expect an AISI to consume.
- **Assuming the US AISI and UK AISI are the same lane.** They are related but distinct; check the current posture of each.

## Summary

- AISIs are the government-side technical bodies for pre-deployment frontier-model evaluation.
- The UK AI Safety Institute (later AI Security Institute) publishes its evaluation harness (Inspect) and pre-deployment evaluations; the US AI Safety Institute inside NIST anchors to NIST AI RMF and NIST AI 100-2.
- An AISI pre-deployment window is a scoped, cooperative engagement: access negotiation → scoping → evaluation → findings → remediation → publication.
- Author your internal artifacts in the *shape* an AISI-published report has; port evaluations to Inspect where possible; pre-author remediation options.
- One evaluation artifact, authored well, flows into a UK AISI window, a US AISI submission, and an EU AI Office Code of Practice model report.
