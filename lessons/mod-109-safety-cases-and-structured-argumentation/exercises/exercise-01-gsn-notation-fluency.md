# exercise-01: GSN Notation Fluency

**Estimated effort:** 2 hours

## Objective

Get fluent in Goal Structuring Notation (GSN Community Standard v3) by translating a small but *complete* safety-case fragment between the two representations chapter 02 named — a **diagram** (the notation as conventionally drawn) and a **structured markdown outline** (the level-40 authoring-time source of truth). By the end you should be able to look at a Clymer-et-al.-shaped argument and translate it *either* direction without having to reread the standard.

This exercise is the notation-fluency floor the rest of the module stands on. Exercise 02 composes the four argument shapes *in* this notation; exercise 03 authors a full case *in* this notation; exercises 04–05 review cases *written in* this notation. Get the primitives wrong here and every downstream artefact is harder to read.

## Prerequisites

- Read chapter 02 (Goal Structuring Notation v3) end-to-end. Skim chapter 01 for what a safety case *is* so the fragment's top claim is not surprising.
- Skim the [GSN Community Standard v3](https://scsc.uk/gsn) — sections on the six core primitives, the undeveloped-goal marker, away goals, and modules. You are not expected to memorise the spec; you *are* expected to know where each primitive is defined.
- Skim [Clymer et al. §§ 2–3](https://arxiv.org/abs/2403.10462) for the argument-shape framing the source fragment uses.
- Pick a rendering tool. Any of the following is acceptable: a diagramming tool that supports GSN symbols (draw.io / diagrams.net, Lucidchart, OmniGraffle, a dedicated tool like ASCE), a Mermaid diagram, or hand-drawn shapes photographed cleanly. The tool does not matter; the *primitives being drawn correctly* does.

## Requirements

### Part A — Translate an outline into a diagram

Take the following structured markdown outline (a fragment of an *Inability × Control* safety case for a hypothetical low-tier LLM assistant deployment) and render it as a GSN diagram. Every named node in the outline must appear in the diagram with the correct GSN shape, correct connection type (solid arrows for `SupportedBy`, dashed lines for `InContextOf`), and correct label.

```
G-TOP: During the review period 2026-Q1–2026-Q2, deployment <C-SCOPE>
       will not enable an operator to obtain step-by-step synthesis
       instructions for the WMDP-Chem hazard set at BON=16 above the
       ASL-2 uplift threshold defined in <C-THRESHOLD>, with residual
       probability <= 5e-4 per session under <C-THREAT-MODEL>.
  C-SCOPE: Deployment scope — internal-support chatbot, authenticated
           users only, no fine-tuning API, no tools, text-in / text-out;
           see mod-102 threat-model docket TM-2026-01.
  C-THRESHOLD: RSP v<pin> ASL-2 chemistry-uplift threshold (§3.2).
  C-THREAT-MODEL: mod-102 TM-2026-01 profile B — determined jailbreaker,
                  no fine-tuning, ≤ 6 hours per session, single-user.
  C-REVIEW-PERIOD: 2026-04-01 to 2026-06-30 wall-clock.
  A-MODEL-STABILITY: The deployed weights hash matches the weights
                     against which the mod-106 DCER and mod-104
                     jailbreak-suite results were collected.
  S-TOP: Decompose by argument shape — Inability × Control — per
         Clymer et al. §4 shape prescription for ASL-2.
  J-TOP: Trustworthiness and Deference are not load-bearing at ASL-2;
         see chapter 03 shape-prescription table for the rationale.
    G-INABILITY: Under the elicitation protocol in mod-106 DCER v3.2,
                 the model's reachable capability on the WMDP-Chem
                 hazard set is below the ASL-2 threshold at BON=16
                 with confidence ≥ 0.95.
      A-ELICITATION-COMPLETE: mod-106's BON=16 + scaffold-variant
                              protocol bounds the reachable ceiling
                              within δ ≤ 1.5×.
      S-INABILITY: Decompose by WMDP-Chem sub-category — synthesis,
                   precursor, dual-use apparatus.
        G-INABILITY-SYNTHESIS: Model refuses / fails on synthesis
                               sub-category above ASL-2 threshold.
          Sn-DCER-SYNTHESIS: mod-106 DCER v3.2, hash sha256:<pin>,
                             run 2026-02-14, grader panel roster in
                             appendix A.
        G-INABILITY-PRECURSOR: [undeveloped] — closure plan: mod-106
                               sprint S-9 (2026-03).
        G-INABILITY-DUAL-USE: Model refuses on dual-use apparatus
                              sub-category above ASL-2 threshold.
          Sn-DCER-DUAL-USE: mod-106 DCER v3.2, hash sha256:<pin>,
                            same run as Sn-DCER-SYNTHESIS.
    G-CONTROL: The deployment prevents ASL-2 chemistry uplift even
               under an unexpected reachable-capability increase.
      A-MONITOR-COVERAGE: mod-108 monitor labelled-corpus is
                          representative of the deployment's traffic
                          distribution.
      S-CONTROL: Decompose by containment layer — refusal-classifier
                 output guard × safety-monitor sidecar.
        G-CONTROL-OUTPUT-GUARD: Fine-tuned refusal classifier catches
                                chemistry-uplift-shaped outputs at
                                FP <= 1%, FN <= 3%.
          Sn-CLASSIFIER-EVAL: mod-108 classifier eval v1.4, hash
                              sha256:<pin>, adaptive-attack survival
                              curve in appendix C.
        G-CONTROL-SIDECAR: Safety-monitor sidecar flags chemistry-
                           uplift-shaped sessions for review.
          Sn-SIDECAR-DASH: mod-108 monitor dashboard snapshot
                           mon-2026-02-28, fire-rate telemetry.
```

For the diagram:

- Use the canonical GSN shapes: rectangles for **goals**, parallelograms for **strategies**, circles for **solutions**, rounded rectangles for **contexts**, rounded rectangles with an "A" annotation for **assumptions**, rounded rectangles with a "J" annotation for **justifications**.
- Draw the **undeveloped-goal marker** (a small diamond attached to the goal's lower edge) on `G-INABILITY-PRECURSOR`.
- Use **solid arrows** for `SupportedBy` (goal → sub-goal via strategy, goal → solution) and **dashed lines** for `InContextOf` (any node → context / assumption / justification).
- Preserve every node ID (`G-TOP`, `S-TOP`, `Sn-DCER-SYNTHESIS`, etc.) verbatim on the diagram so the outline and diagram cross-reference programmatically.

### Part B — Translate a diagram into an outline

Take the *Control-shape* GSN fragment below (described in text so this exercise is self-contained; treat this description as if it were the diagram) and produce the equivalent structured markdown outline.

> The top goal `G-CONTROL-CODE-AGENT` reads: *"For the code-modification-agent deployment defined in `C-CODE-SCOPE`, the deployment prevents any merge into a `main` branch of a change classified as CVSS ≥ 7.0 without human review during the review period."* It is supported (via strategy `S-CODE-CONTROL`) by three child goals: `G-EACC-EGRESS` (*"EACC v3.7 §egress restricts commits to allow-listed remotes"*), `G-HITL-MERGE` (*"HITL contract requires human approval on every merge to `main`"*), and `G-KILL-SWITCH` (*"Kill switch is drilled quarterly and fires within SLA"*). `G-EACC-EGRESS` is grounded in solution `Sn-EACC-DOC` (the EACC v3.7 document, doc-hash pinned) and solution `Sn-WRAPPER-LOG` (7-day retrospective audit-log stream, log-index snapshot). `G-HITL-MERGE` is undeveloped in this pass (closure plan: awaiting the mod-107 chapter-04 escalation-drill from sprint S-11). `G-KILL-SWITCH` is grounded in solution `Sn-DRILL-2026-Q1` (the Q1-2026 kill-switch drill runbook and timing log). The strategy `S-CODE-CONTROL` carries a justification `J-CODE-CONTROL` (*"the three layers are jointly sufficient because the EACC narrows the egress surface, HITL blocks the merge point, and the kill switch bounds the blast radius under a compromised HITL"*). The top goal has contexts `C-CODE-SCOPE` (deployment scope) and `C-REPO-SCOPE` (repository allow-list) and assumptions `A-EACC-ENFORCED` and `A-HITL-COMPETENT`.

For the outline:

- Use the same node-ID conventions and indentation as the Part-A outline.
- Preserve the connection semantics: contexts / assumptions / justifications indented as *scoping* nodes on their parent; strategy decompositions expanding parent → strategy → children; solutions as leaves.
- Mark undeveloped goals with an explicit `[undeveloped]` annotation and the closure plan on the same or next line.
- Do not silently add nodes the source diagram did not carry, and do not silently drop nodes it did.

### Part C — Fluency notes

Write a short (~1 page) `notes.md` recording:

- One primitive you had to re-check the standard for (goal vs strategy vs justification is a common one).
- One node in Part A that you were tempted to draw wrong (e.g. a solution masquerading as a goal, or a strategy without a name).
- One place where you noticed an *implicit* assumption in the source fragment that the notation did not surface. Would you add it as an `A-` node in your rendition? Justify.
- One reflection on the diagram-vs-outline trade-off: which primitive is easier to work with in the outline, which is easier in the diagram, and why.

## Deliverables

Commit to the paired `agentic-safety-engineer-solutions` repo (or your exercise-solution area):

- `part-a-diagram.<png|svg|drawio|mmd>` — the Part A diagram.
- `part-b-outline.md` — the Part B structured markdown outline.
- `notes.md` — the Part C fluency notes.

## Acceptance criteria

- **All six primitives appear at least once** across Parts A and B: goal, strategy, solution, context, assumption, justification. Missing any is a fail.
- **The undeveloped-goal marker is rendered correctly** in Part A on `G-INABILITY-PRECURSOR` (and its closure plan is present in the outline text).
- **Connection types match the semantics** — solid `SupportedBy` for goal-decomposition and goal-to-solution; dashed `InContextOf` for context / assumption / justification attachments. Getting the arrow style wrong is a common review-time finding; the exercise catches it here.
- **Node IDs are preserved verbatim** across Parts A and B. If Part A's diagram silently renames `Sn-DCER-SYNTHESIS` to "eval report," it is a fail; solutions are *cited artefacts*, not narrative labels.
- **No solution-masquerading-as-goal** — every rectangle you drew as a goal is a *proposition* about the system, not a description of an evidence artefact. Every circle you drew as a solution *cites* a specific evidence artefact (with a hash-or-URI placeholder). Chapter 02's *notation-fluency traps* list is what the graders check against.
- **No unnamed strategies** — every parallelogram has a decomposition rule ("decompose by …") on it. Unnamed strategies are dependency arrows, not GSN strategies.
- **Contexts are not decorations** — every context node reads like a *scope-of-validity* proposition (a deployment scope, a threat model, a threshold, a review period), not like a preamble sentence.
- **The Part C notes name at least one implicit assumption** the source fragment did not spell out. This is the fluency signal that matters most for the rest of the module.

## Stretch goals

- **Render the same Part-A fragment in both a diagram and a Mermaid text notation.** Compare which is easier to review in a code-review comment thread. This informs whether your team should adopt a Mermaid-based authoring flow.
- **Extend Part A with a *module boundary*.** Split the Inability leg into its own module with `G-INABILITY` as the module's exported goal, `A-ELICITATION-COMPLETE` as an imported assumption, and `Sn-DCER-SYNTHESIS` / `Sn-DCER-DUAL-USE` as internal solutions. Draw the module boundary as a labelled box.
- **Add an *away goal*.** Suppose `G-CONTROL-SIDECAR` is decomposed in a separate *Monitor-coverage* module. Redraw the diagram with `G-CONTROL-SIDECAR` as an away goal pointing at that module.
- **Contribute a canonical Part-A rendering to your team's shared style guide.** If your organisation does not yet have a GSN style guide, this exercise's output is the seed.

## Guardrails

- Do not paraphrase the source fragment when translating. If Part B's diagram says *"CVSS ≥ 7.0 without human review,"* your outline says the same. Rewording during translation is where notation drift starts.
- Do not add extra assumptions to Part A that the source outline did not carry, *even if they look load-bearing*. Note them in `notes.md` (Part C) instead. Silently adding assumptions to a case is worse than acknowledging them out-of-band.
- Do not commit any real credentials, weights hashes, tenant IDs, or production evaluation results. The Part-A fragment uses `<pin>` placeholders on purpose; keep it that way.
- Do not treat the diagram tool's default aesthetics as the notation. The GSN standard prescribes primitive shapes; the tool's colour palette does not. Prefer legibility over prettiness.
