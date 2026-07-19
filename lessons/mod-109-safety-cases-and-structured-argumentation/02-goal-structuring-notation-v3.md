# 02 — Goal Structuring Notation (GSN Community Standard v3)

## Motivation

Chapter 01 named the safety case as a structured argument. This chapter names the *structure*: **Goal Structuring Notation (GSN)**, in its Community Standard v3 revision maintained by the Assurance Case Working Group at `scsc.uk`. GSN is what the argument is drawn in. Every serious frontier-AI safety-case paper — Clymer et al., Goemans et al., the safety cases the frontier labs shape their internal review artefacts around — either uses GSN directly or a notation whose primitives map cleanly onto GSN's. Knowing GSN is knowing the argot the discipline argues in.

The point of a notation is not to make diagrams pretty. It is to make the *shape of the argument* legible at a glance. A reviewer reading a GSN diagram should see, in the first thirty seconds: what is being claimed at the top, how the top claim is decomposed, where the decomposition bottoms out into cited evidence, which branches are still undeveloped, and which contexts and assumptions the argument depends on. A prose narrative cannot expose that structure at a glance; a well-formed GSN diagram — or, in the pattern this chapter will name, a well-formed structured markdown outline that maps onto GSN — can.

This chapter walks GSN's primitives, its decomposition rules, its extensions (away goals, modules), and — importantly for engineers who do not want to become full-time diagramming specialists — when to draw a diagram versus when a structured markdown outline is sufficient. Exercise 01 asks you to translate a working safety-case fragment between the two representations.

## Primary sources

- **[GSN Community Standard v3 — Assurance Case Working Group, `scsc.uk/gsn`](https://scsc.uk/gsn)** — the canonical specification. <!-- needs-research: pin the exact published revision (v3, minor version) of the GSN Community Standard when citing in an artefact; the working group publishes point releases. -->
- **[Kelly and Weaver — "The Goal Structuring Notation: A Safety Argument Notation"](https://www.york.ac.uk/media/tftv/documents/GSN-Kelly-Weaver.pdf)** <!-- needs-research: confirm the citation format and canonical URL for the Kelly and Weaver introduction to GSN; a York University hosted PDF is the expected form. --> — the historical introduction. Read for the *why* behind the primitives before the *what* of the standard.
- **[ISO/IEC/IEEE 15026-2 — Systems and software assurance — Part 2: Assurance case](https://www.iso.org/standard/74396.html)** <!-- needs-research: confirm the current published edition of ISO/IEC/IEEE 15026-2 and whether the working draft for the next edition changes the assurance-case terminology cited below. --> — the standards-body view of the assurance case. GSN is one of the concrete notations 15026-2 admits.
- **[Clymer et al. §3 — argument decomposition examples](https://arxiv.org/abs/2403.10462)** — worked examples of GSN-shaped arguments for the four safety-case argument shapes chapter 03 dissects.

## The six core primitives

GSN has six core primitives, each with a canonical shape when drawn and a canonical role in the argument. Learn the primitives by their *role* before their shape.

### Goal

A **goal** is a proposition the argument is trying to establish. Drawn as a **rectangle**. Every goal is either the top claim of the case, an intermediate sub-claim, or a leaf claim that a solution grounds. Goals are written as declarative refutable propositions:

> *G-TOP: During the review period (2026-Q1 to 2026-Q2), the deployment described in C-SCOPE will not enable a CBRN-uplift event exceeding the RSP ASL-3 threshold defined in C-THRESHOLD with probability greater than 1×10⁻⁴.*

Not:

> ~~G-TOP: The system is safe.~~

A goal that cannot be falsified cannot be a goal. Chapter 01 named this posture; GSN enforces it at the notation layer.

### Strategy

A **strategy** is the *decomposition rule* that takes a parent goal and produces child goals. Drawn as a **parallelogram**. A strategy answers *"why does establishing these child goals establish the parent goal?"* — the argumentative content of the case lives in the strategies, not in the goals themselves.

Common strategy shapes:

- *Argument by case-split.* The parent goal is decomposed into an exhaustive set of cases, and each case is a child goal. The strategy asserts case-exhaustiveness.
- *Argument by argument-shape.* The parent goal is decomposed by the four Clymer-et-al. shapes (Inability, Control, Trustworthiness, Deference), and each shape is a child goal. The strategy asserts that the shapes are jointly sufficient.
- *Argument by risk-class decomposition.* The parent goal (unacceptable-harm avoided) is decomposed by risk class (CBRN, cyber, autonomy, persuasion, exfiltration), and each risk class is a child goal.
- *Argument by lifecycle stage.* The parent goal is decomposed by deployment-lifecycle stage (pre-deployment, canary, staged rollout, general availability, post-deployment), and each stage is a child goal.

A strategy without a name is an argument without content. If your GSN has an unnamed parallelogram, you have not authored a strategy; you have drawn a dependency arrow.

### Solution

A **solution** is a leaf node grounding a goal in a specific external piece of evidence. Drawn as a **circle**. Solutions cite artefacts *outside* the case — an eval report with a hash, a monitor dashboard with a snapshot, a red-team suite with a version, an incident-history query with a timestamp.

A defensible solution names:

- The artefact ID and version (a hash, a URI, a document number).
- The specific claim the artefact supports.
- The measurement methodology at high level (with the artefact carrying the detail).
- The evidence's *provenance* — who ran the eval, when, under what protocol, with what elicitation-gap accounting.
- Any relevant *scope limits* — what the evidence does *not* cover.

A solution that says "we ran red-team tests" is not a solution; it is a wish. A solution that says "HarmBench v0.3.0 run on model-fingerprint-`sha256:...`, StrongREJECT judge v2.1, ASR = 3.1% [1.8, 5.0] 95% CI, elicitation-gap-adjusted per mod-106 §4" is a solution.

### Context

A **context** is a proposition that *scopes* a goal or a strategy: it names the surrounding facts under which the goal or strategy is stated. Drawn as a **rounded rectangle**. Contexts are attached to goals or strategies with a dashed line.

Typical contexts on a safety-case top goal:

- *C-SCOPE.* The deployment scope: the specific system, tool inventory, integration surface, user population, review period.
- *C-THREAT-MODEL.* The threat model the case argues under (a citation to mod-102's deliverable for the deployment).
- *C-THRESHOLD.* The unacceptable-harm threshold the claim references (a citation to the RSP / Preparedness / FSF tier definition).
- *C-REVIEW-PERIOD.* The wall-clock window the case is valid for.

A context is not a decoration. If a reviewer changes the context, the argument may no longer hold — and *that is the point*. Contexts make the case's *scope of validity* explicit.

### Assumption

An **assumption** is a proposition the argument *depends on* but does *not* itself establish. Drawn as a **rounded rectangle with an "A" annotation**. Assumptions are attached to goals or strategies with a dashed line.

Typical assumptions in a frontier-AI safety case:

- *A-ELICITATION.* The elicitation-gap accounting in mod-106's protocol captures the reachable capability ceiling under adversarial fine-tuning, best-of-N scaffolding, and reasonable red-team effort.
- *A-MONITOR-COVERAGE.* The monitor's labelled evaluation corpus is representative of the deployment's traffic distribution.
- *A-CONTAINMENT-INTEGRITY.* The EACC (mod-107) is honoured by the runtime — the wrapper is not bypassed, the sandbox is not escaped, the audit log is not tampered with.
- *A-MODEL-STABILITY.* The evidence in solution nodes was collected against a model whose weights, tokenizer, and inference stack match the deployed configuration.

Every assumption is a **rebuttal surface**. Chapter 05 makes this load-bearing: a reviewer's counter-argument is often a challenge to an assumption, and the case's response is either to strengthen the argument so the assumption is no longer needed, to widen the evidence portfolio so the assumption becomes testable, or to accept the challenge and route it into follow-on evidence work.

### Justification

A **justification** is a proposition that *explains why* a strategy or a goal is stated the way it is. Drawn as a **rounded rectangle with a "J" annotation**. Attached with a dashed line.

Justifications are where the case explains, for a reviewer, *why* the strategy's decomposition is complete, *why* the goal's threshold is the right number, *why* the context is scoped the way it is. Where an assumption is a *load-bearing but unestablished* proposition, a justification is a *load-bearing rationale* that the argument's *shape* is right. The distinction matters because a challenged assumption changes the argument's validity; a challenged justification changes the argument's *legibility*.

## The undeveloped-goal marker

A goal that has *not yet* been decomposed carries an **undeveloped-goal marker** — canonically, a diamond attached to the goal's lower edge. This is one of the notation's most important features, and one engineers instinctively resist.

An undeveloped-goal marker says, out loud, *"this leg of the argument is acknowledged but not yet supported."* A reviewer reading the case sees the marker, sees the number of undeveloped goals, and asks *"what is the plan for closing these?"* — a bounded question with a bounded answer. Compare to the alternative, which is to hide the gap in a paragraph of prose or to silently omit the leg entirely.

Some undeveloped goals are load-bearing (the case cannot ship until they are closed). Others are lifecycle-scheduled (the case ships with them open, and a subsequent review cycle closes them). The case makes the distinction. mod-112's disclosure engineering carries the *"open undeveloped goals under active work"* list into the system card. That is the disclosure posture chapter 01 named: **acknowledged and routed**, not **hidden**.

Adjacent markers:

- **Undeveloped-strategy marker.** A strategy whose decomposition rule has not yet been justified. Same posture as an undeveloped goal.
- **Uninstantiated marker.** A pattern that has not yet been bound to a specific artefact — used in *template* cases (Goemans et al. is one). The Goemans template is a *pattern*; a deployment-specific case *instantiates* it.

## Away goals and modules

Frontier safety cases are large. A case for one high-risk deployment can have dozens of intermediate goals and hundreds of solutions. GSN v3's *modularity* extensions make this tractable.

- **Away goals.** A goal decomposed in *another* module of the case. Drawn with a distinctive shape (a rectangle with a small module reference). The away goal appears as a leaf in the current diagram but references its full decomposition in the target module. This is how the case supports zoom-in / zoom-out reading.
- **Modules.** A module is a coherent *sub-case* — a set of goals, strategies, solutions, contexts, and assumptions with defined public interfaces (top goal, imported contexts, exported evidence). A frontier-AI safety case commonly modularises into a *containment* module (citing mod-107's EACC), a *dangerous-capability* module (citing mod-106's DCER), a *monitor-coverage* module (citing mod-108), a *control-eval* module (citing mod-110), and a *lifecycle* module (staged rollout, canary criteria, rollback contract).
- **Contracts between modules.** Each module publishes the goals it *satisfies* (its exports) and the contexts / assumptions it *requires* (its imports). A change in an exported goal is a version bump on the case. A change in an imported assumption routes to whichever module owns the assumption.

Modularity is what makes the case *maintainable* over the deployment lifecycle. A CVE in the sandbox platform re-routes to the containment module; a new elicitation technique in mod-104 re-routes to the dangerous-capability module. Without modules, every change is a whole-case rewrite.

## When to draw a diagram, and when to write a structured outline

GSN is conventionally drawn as a diagram. For a level-40 engineering practice, that convention is worth challenging.

**Draw the diagram** when:

- The case is being *presented* — to a review body, a regulator, an external evaluator. The diagram is legible at a glance in a way an outline is not.
- The case has *modular complexity* — dozens of goals and cross-references between modules. The diagram makes the module boundaries visible.
- The case is being *taught* or handed off — the diagram is the artefact a new reviewer or a new author reads to internalise the argument's shape.

**Write a structured markdown outline** — a hierarchical text representation with explicit `G-`, `S-`, `Sn-`, `C-`, `A-`, `J-` node IDs — when:

- The case is being *authored* — text is faster to iterate, faster to diff in version control, faster to review in a pull request. The Goemans template is essentially a structured outline; that is not an accident.
- The case is *cited* by other systems — an outline with node IDs is programmatically citable in a way a diagram is not. mod-112's disclosure engineering can pull a specific solution's node ID into a system card without a screenshot.
- The case is *versioned* — a text outline diffs cleanly; a diagram-only case does not.

The right level-40 posture is: **author in a structured markdown outline**, **maintain the outline as the source of truth**, **render the diagram from the outline for presentation moments**. Some GSN tools support this bidirectionally; when they do not, keep the outline authoritative and treat the diagram as a build output.

A worked outline stub:

```
G-TOP: During <C-REVIEW-PERIOD>, deployment <C-SCOPE> avoids
       unacceptable-harm class C beyond threshold <C-THRESHOLD>
       with probability ≤ 1e-4 under threat model <C-THREAT-MODEL>.
  S-TOP: Decompose by argument shape (Inability × Control ×
         Trustworthiness × Deference), per Clymer et al. §2.
  J-TOP: The four shapes are jointly sufficient because <...>;
         no other shape has been proposed for class-C claims in
         the literature reviewed in mod-109 §01.
  A-TOP-ELICITATION: mod-106's elicitation-gap accounting
                    captures the reachable capability ceiling.
    G-INABILITY: Under the elicitation protocol in mod-106, the
                 model cannot produce class-C harm above threshold X.
      S-INABILITY: Decompose by risk sub-class (bio / chem / rad / nuc).
        G-INABILITY-BIO: ... [undeveloped]  <-- undeveloped marker
        G-INABILITY-CHEM: ...
          Sn-CHEM-EVAL: DCER v3.2, hash <...>, run 2025-11-14.
        ...
    G-CONTROL: The deployment prevents class-C even under an
               inability-argument gap.
      S-CONTROL: Decompose by containment layer (EACC × monitors
                 × HITL × kill switch).
      ...
    G-TRUSTWORTHINESS: ...  <-- undeveloped in the pattern this
                              case follows; see chapter 03.
    G-DEFERENCE: External evaluators corroborate the residual
                 risk claim.
      Sn-AISI-REPORT: AISI pre-deployment report, docket <...>.
```

The outline is the artefact exercise 01 asks you to author for a small worked example, and the artefact chapter 04's authoring workflow scales up.

## Notation-fluency traps

Even engineers who have read the standard tend to fall into the same handful of traps. Naming them here saves review cycles later.

- **Confusing a strategy with a goal.** A strategy is a *decomposition rule*; a goal is a *proposition*. If your diagram has a parallelogram that reads like a proposition ("the containment is enforced"), it is a mis-drawn goal. Fix the label to name the decomposition rule ("decompose by containment layer").
- **Naming a solution as a goal.** A solution is *what evidence grounds the goal*. If your rectangle reads "eval report shows ASR < 3%," it is a solution masquerading as a goal. Split it: the goal is "the model refuses class-C prompts with ASR < 3%"; the solution is the eval report.
- **Undeveloped goals without a plan.** An undeveloped-goal marker is not a shrug. It is a commitment that the goal is *scheduled* for decomposition or that its absence is *acknowledged residual risk*. A case with 40 undeveloped goals and no closure plan will not survive review.
- **Contexts as decorations.** A context that reads "the system is a chatbot" is not a context; it is a preamble. A context reads "the deployment scope is limited to authenticated users of the `internal-support` role with the tool inventory in EACC v3.7."
- **Assumptions the argument silently depends on.** Every load-bearing dependency the argument does not itself establish is an assumption. If the argument depends on "the monitor's labelled corpus is representative" and does not say so, a reviewer will find the gap and treat the whole leg as unsupported.
- **Away goals whose target does not exist yet.** An away goal is an *interface contract*; the target module must exist (even as a stub) and expose the goal as an *export*. An away goal pointing at a non-existent module is a broken link.

## Interfaces

- **Chapter 01 (safety cases).** This chapter is the notation for the artefact chapter 01 named.
- **Chapter 03 (argument shapes).** The four Clymer shapes each project onto GSN in a canonical way: the top-level strategy decomposes by shape; each shape is a sub-goal with its own decomposition. Chapter 03 walks the shapes; the notation for each is this chapter's material.
- **Chapter 04 (authoring workflow).** The workflow *writes* GSN — top goal, strategies, solutions, contexts, assumptions, justifications, undeveloped markers, module boundaries. Chapter 04 uses this chapter's outline convention as the working representation.
- **Chapter 05 (pitfalls).** Notation misuse — undeveloped goals without a plan, assumptions the argument silently depends on, away goals whose targets do not exist — is a specific class of pitfall chapter 05 catalogues.
- **mod-107 (EACC).** The Containment module in the safety case *imports* the EACC as a context and *cites* its wrapper log stream as a solution. The GSN module boundary is where the mod-107 artefact plugs in.
- **mod-108 (Monitors).** The Monitor-coverage module cites monitor dashboards as solutions and imports the monitor's coverage claim as a context.
- **mod-112 (Disclosure).** The system card and regulator-facing disclosure cite specific GSN node IDs. Structured outline authoring makes the citation programmatic.

## Common misreadings to avoid

- **"GSN is for aerospace / rail; it does not fit AI."** Wrong on both facts. GSN was developed in the safety-critical systems community, aerospace and rail are two of its earliest adopters, and the Clymer / Goemans templates use it for AI because the notation *is* domain-general. What is *AI-specific* is the argument shapes and the evidence portfolio; the notation is the same.
- **"A diagram is required."** No. The Community Standard admits equivalent structured representations. What is required is that the notation carry the primitives faithfully — goals, strategies, solutions, contexts, assumptions, justifications, undeveloped markers, modules — and that the representation be *reviewable*. A structured markdown outline meets that bar.
- **"Every leaf must be a solution."** No. Undeveloped goals are legitimate leaves with an explicit marker. What is *not* legitimate is a leaf that *pretends* to be a solution but is not backed by an artefact citation.
- **"Assumptions are for edge cases."** No. Assumptions are for *every load-bearing dependency the argument does not itself establish*. Elicitation-gap accounting is an assumption; monitor coverage representativeness is an assumption; runtime-honours-EACC is an assumption. Under-stating assumptions is a review-time finding.
- **"Modules are optional."** For a small case, yes. For a frontier-AI safety case with dozens of goals and hundreds of solutions, modularity is what keeps the case maintainable. A monolithic case rots on first evidence-drift.
- **"The notation guarantees a good argument."** No. The notation makes a bad argument *legibly bad*. The argument's quality is chapter 03's material; the notation's job is to expose it.

## Summary

- **GSN Community Standard v3** is the notation the safety-case discipline argues in. Learn its primitives — **goal, strategy, solution, context, assumption, justification** — by their role in the argument.
- **Undeveloped-goal markers**, **away goals**, and **modules** are what turn GSN from a single-diagram notation into a maintainable artefact across a frontier-AI deployment's lifecycle.
- Draw the **diagram** for presentation, teaching, and handoff. Author in a **structured markdown outline** for iteration, versioning, and programmatic citation from mod-112's disclosure artefacts.
- The notation's job is to make argument shape legible. Bad arguments are legibly bad in GSN — undeveloped goals show up as markers, unstated assumptions show up as gaps, mis-drawn strategies show up as unnamed decompositions. That legibility is the point.
- Exercise 01 asks you to translate a working safety-case fragment between a diagram and a structured outline. Do it in both directions; internalise which primitives map cleanly and which force you to think harder.
- Chapter 03 uses the notation to walk the four Clymer argument shapes. Chapter 04 uses it to author a case end-to-end.
