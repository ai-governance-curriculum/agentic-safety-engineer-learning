# 06 — Boundaries: `ai-evaluation-engineer` and `senior-ai-governance-architect`

## Motivation

The safety case authored in chapters 01–05 does not exist in isolation. Immediately below it — as consumer, not as producer — is the **release-assurance methodology**: the audit-trail engineering, the regulator-facing artefact packaging, the pre-registration-of-review-outcomes discipline that turns a case into a shippable release. Immediately above it — as architectural context, not as sibling — is the **control-library architecture**: the taxonomy of controls, the cross-jurisdiction reconciliation, the org-wide policy framework the individual case's citations plug into. And laterally, downstream in the same track, is **disclosure engineering** (mod-112): the shaping of the case for external audiences (system cards, AISI submissions, EU AI Act Article 73 serious-incident reports, regulator briefings).

The safety-engineering role in this module *authors and iterates* the safety case. It does *not* own the audit-trail platform or the org-wide control taxonomy. Naming the boundary is what separates a case that ships defensibly (routed correctly to peer roles who consume it) from a case that is bundled with unowned platform work and unowned architectural work and is silently blocked by both.

This chapter draws those boundaries and codifies the handoff shape — what mod-109 hands to `ai-evaluation-engineer`, what it consumes from `senior-ai-governance-architect`, and what it hands laterally to mod-112.

## The two adjacent roles

### `ai-evaluation-engineer` (peer / next-up, level 35, AI Governance family)

Owns the **release-assurance methodology** — the operational discipline that consumes safety cases (and eval reports, red-team suites, DCERs) as *release-gating evidence* and packages them into audit-ready artefacts for internal governance and external regulators.

Includes:

- The audit-trail platform (immutable evidence bundles, hash-anchored artefact registers, sign-off ledgers).
- The release-assurance workflow (pre-registration of review outcomes, sign-off routing, staged-rollout gate management).
- The regulator-facing artefact packaging (EU AI Act Article 55 / 56 GPAI systemic-risk documentation, EU AI Act Article 73 serious-incident reporting, AISI pre-deployment engagement packaging).
- The eval-reproducibility discipline (dataset / model / prompt / decoding / seed hashes, reproduction runbooks).
- The internal-audit function (are the artefacts consistent, versioned, cross-referenced, and defensibly grounded).

mod-109 hands the safety case *to* `ai-evaluation-engineer` as one of the release-assurance evidence artefacts they package. The case does not carry its own release-assurance plumbing; that is `ai-evaluation-engineer`'s craft.

### `senior-ai-governance-architect` (level 50, AI Governance family)

Owns the **control-library architecture** — the org-wide taxonomy of controls (technical, procedural, organisational), the cross-jurisdictional policy reconciliation (US / EU / UK / APAC / rest-of-world), and the framework-composition strategy (RSP × Preparedness × FSF × ISO/IEC 42001 × NIST AI RMF × EU AI Act).

Includes:

- The control library — the enumerated, taxonomised, mapped-to-framework set of controls the organisation implements. Each control has an ID, a description, a framework mapping (which RSP / Preparedness / FSF / ISO / NIST clause it satisfies), an owner, and a testing cadence.
- The policy taxonomy — the org's harm categorisation, threshold-setting, tier-defining language.
- The cross-jurisdictional reconciliation — where EU GPAI systemic-risk criteria differ from US EO 14110 CBRN-uplift definitions from UK AISI's methodology, the architect owns the mapping.
- The framework composition — deciding which framework's language the org's cases author *in*, which framework's language they *disclose in*, and how the two compose.

mod-109 *consumes* the control library as the context its individual cases cite. A safety case does not re-derive the control taxonomy; it cites the control-library ID and the architect's framework mapping.

## Where the boundary lies

Two questions determine which role owns a piece of work.

**Question 1 — What is the artefact?**

- If the artefact is *a safety case for a specific deployment* — the top claim, the shape decomposition, the evidence citations, the assumption capture, the rebuttal register — **mod-109** (this role) owns it.
- If the artefact is *the release-assurance bundle that packages the safety case (and other release evidence) for internal sign-off or regulator submission* — **`ai-evaluation-engineer`** owns it.
- If the artefact is *the org-wide control library, the framework composition strategy, or the cross-jurisdictional policy reconciliation* — **`senior-ai-governance-architect`** owns it.
- If the artefact is *the disclosure shape for an external audience* (system card, AISI report, EU AI Act Article 73 serious-incident report) — **mod-112** (adjacent module, same role family) owns it, with the safety case as input.

**Question 2 — Who is the reviewer?**

- If the reviewer is *the internal-safety-review body* (RSO / Preparedness Committee / Safety Board) reading the case as a tier-decision input — mod-109 owns it.
- If the reviewer is *the internal-audit function* checking that release-evidence bundles are consistent, versioned, and correctly signed — `ai-evaluation-engineer` owns it.
- If the reviewer is *the executive / board / cross-functional governance forum* deciding on cross-jurisdictional strategy or org-wide framework alignment — `senior-ai-governance-architect` owns it.
- If the reviewer is *an external audience* (AISI, EU AI Office, national regulator, the public via system card) — mod-112 owns the artefact shaping, with mod-109's case as the primary input.

Ambiguity resolves by naming co-owners. A safety case cited in a system card is co-owned by mod-109 (case authorship) and mod-112 (disclosure shaping); a safety case cited in an internal release-assurance bundle is co-owned by mod-109 and `ai-evaluation-engineer`.

## Handoffs from mod-109 to `ai-evaluation-engineer`

For the safety case to be *consumable* as release-assurance evidence, this role must provide:

- **A signed, version-pinned safety case.** The GSN outline (chapter 02), the top claim, the shape decomposition (chapter 03), the evidence portfolio with citations, the assumption capture, the rebuttal register (chapter 05). All version-pinned so that release-assurance can hash-anchor the artefact.
- **A citable-at-node-ID case.** Every goal, strategy, solution, context, and assumption has a stable ID. `ai-evaluation-engineer`'s release bundle cites specific nodes; a case without stable IDs cannot be programmatically cited.
- **A rebuttal register.** The pre-rehearsed counter-arguments and responses (chapter 05). Release-assurance packages this alongside the case so that internal audit and regulator-facing artefact can point at the rehearsed responses.
- **A closure-plan register for undeveloped goals.** Every undeveloped-goal marker has a closure plan (chapter 04 step 5). Release-assurance tracks the closure plan through the deployment lifecycle; without it, the release-gate can neither open nor close.
- **A framework-context version pin.** The RSP / Preparedness / FSF / EU AI Act version the case was authored against (chapter 04 step 1). Release-assurance uses the pin to detect moving-target-threshold drift (chapter 05 pitfall 4).
- **A staged-rollout / canary criteria set.** The case's Lifecycle module (chapter 04 step 4) names the rollout gates. Release-assurance operates the gates; the case names the criteria.

Findings from mod-109 that route to `ai-evaluation-engineer`:

- *"The case's rebuttal register needs to be packaged into the AISI submission bundle in the pre-defined AISI-methodology shape; the shape's schema is in AISI's methodology publication; the packaging is release-assurance's craft."*
- *"The case has three undeveloped goals with closure plans in mod-108 (monitor coverage), mod-110 (control-eval), and mod-106 (elicitation-gap). Release-assurance owns the through-lifecycle tracking; mod-109 owns the case update at each closure."*
- *"The framework version pin needs a monitoring signal that fires when RSP is updated during the review period. The signal lives in release-assurance's platform; the case authors the pin."*

## Handoffs from `senior-ai-governance-architect` to mod-109

For the safety case to be *authorable defensibly*, the architect must provide:

- **A control library.** The enumerated, taxonomised, framework-mapped set of controls the org implements. Each case's solution nodes cite control-library IDs (e.g., "control `CTRL-CONTAIN-42` = EACC v3.7 §credential-broker"). Without a library, each case re-derives the taxonomy and drifts from every other case.
- **A framework composition strategy.** The org's decision about which framework to author cases *in* (RSP language, Preparedness language, FSF language, or a hybrid) and which to *disclose in*. mod-109 authors against the chosen composition; drift between the case and the composition is a routing back to the architect.
- **A cross-jurisdictional harm taxonomy.** The org's harm-class definitions across US / EU / UK. mod-109's top claim references the harm class; the class definition is architect-owned.
- **A threshold-setting policy.** The org's stance on how thresholds are set (framework-inherited, org-tightened, deployment-specific). mod-109's top claim references the threshold; the setting policy is architect-owned.
- **A sign-off routing.** Which reviewer signs which tier / harm-class / deployment-scope combination. mod-109's case is routed per the architect's routing table.

Findings from mod-109 that route to `senior-ai-governance-architect`:

- *"The case needs to cite a control the org does not yet enumerate — an EACC egress-allow-list control for a specific downstream API. The control needs adding to the library; the mapping to Preparedness Framework autonomy scorecard needs the architect."*
- *"The threshold in the Preparedness Framework's `Autonomy` `Medium` bound is stated in a unit the org has not yet decided to translate into. The architect owns the translation; mod-109 consumes it."*
- *"The AISI pre-deployment report cited a jurisdictional definition of `serious incident` that differs from the EU AI Act Article 3 definition. The reconciliation is architect-owned; the case's `C-HARM-CLASS` context inherits the reconciliation."*

## Handoff from mod-109 to mod-112 (disclosure)

mod-112 (Frontier-Safety Program and Disclosure) is the adjacent module in the same track that consumes the safety case for external audiences. The handoff is one-directional (mod-109 authors, mod-112 discloses), and the interface is well-defined:

- **The safety case is the *primary source* for the system card.** mod-112 pulls specific goal / solution IDs into the system card's evidence claims. The case's stable node IDs (chapter 02) make this programmatic.
- **The rebuttal register (chapter 05) is the *primary source* for AISI-response and regulator-briefing packaging.** mod-112 shapes the register for external audience; the register's content is mod-109's.
- **The assumption catalogue is the *primary source* for the residual-risk disclosure.** External disclosures (EU AI Act Article 55 systemic-risk documentation, GPAI Code of Practice safety chapter disclosure) call out residual risk; the case's assumption catalogue is the underlying material.
- **The undeveloped-goal closure plan is the *primary source* for the "under active work" section of the system card.** The disclosure posture is *acknowledged and routed* (chapter 01); mod-112 shapes it; mod-109 provides it.

Findings that route mod-109 → mod-112:

- *"The case names three assumptions that will be difficult to disclose without triggering competitive concerns; mod-112 needs to shape the disclosure without weakening the case's defensibility."*
- *"The case's rebuttal register anticipated a specific AISI counter-argument; the pre-rehearsed response needs mod-112's shaping into the AISI-response packaging format."*

## What mod-109 does *not* build

The following are common temptations for this role to build, and they are adjacent-role scope:

- **A release-assurance platform.** Immutable evidence bundles, hash-anchored registers, sign-off ledgers — these live in `ai-evaluation-engineer`'s scope. mod-109 uses the platform; it does not build it.
- **A control library.** The org-wide taxonomy of controls is `senior-ai-governance-architect`'s craft. mod-109 cites the library; it does not author it.
- **A cross-jurisdictional policy reconciliation.** Where EU / US / UK definitions differ, the architect owns the reconciliation. mod-109 inherits it.
- **A system card.** The system card, the AISI pre-deployment report shape, the EU AI Act Article 73 incident report shape — these are mod-112's craft. mod-109's case is the primary input.
- **A regulator engagement plan.** How and when to engage AISI, the EU AI Office, national regulators — this is mod-112 tactically and `head-of-ai-governance` strategically (level 60, out of scope for this track's ownership). mod-109 provides the case; it does not run the engagement.

## What mod-109 *does* build

Concretely, from mod-109's exercises and the artefacts they produce:

- **A GSN-notation fluency artefact** — one small safety-case fragment rendered both as a diagram and a structured markdown outline, with a mapping between them (chapter 02, exercise 01).
- **An Inability × Control × Trustworthiness × Deference composition drill** — a worked example that assigns evidence to shapes correctly and audits the compositional assumptions (chapter 03, exercise 02).
- **A safety case for one specific deployment** — top claim, shape decomposition, evidence portfolio, assumption capture, rebuttal register, undeveloped-goal closure plan, framework-version pin. This is the module's load-bearing artefact (chapter 04, exercise 03).
- **A rebuttal-and-evidence-provenance review** of an existing safety case (an artefact from a peer's exercise 03, or one of the frontier-lab safety cases in the primary sources) — locating each of chapter 05's pitfalls, classifying findings, drafting remediation routes (chapter 05, exercise 04).
- **A safety-case pitfall catalogue** — the operational check-list a level-40 author runs the case against before signing (chapter 05, exercise 05).

Each artefact *names* the adjacent-role components it plugs into. None re-derives them.

## Reading the boundaries against the rest of the module

- **mod-101 (Frameworks).** Framework fluency is prerequisite. The case cites framework-defined thresholds and tier language; a case without framework fluency is not authorable at level 40.
- **mod-102 (Threat Modelling).** Threat model is a *context node* on the case (chapter 04 step 1). The case's argument-completeness (chapter 05 pitfall 2) is measured against the threat model's hazard enumeration.
- **mod-103 / 104 (Prompt Injection / Jailbreak).** Feed evidence into the *Trustworthiness* leg's refusal-robustness solution nodes and, for high-blast-radius agent settings, into the *Control* leg's tool-abuse-chain coverage.
- **mod-105 (Agent Attack Surface).** Feeds *Control* leg's agent-scope coverage and *Deference* leg's third-party red-team engagements.
- **mod-106 (DCER).** Feeds *Inability* leg's load-bearing evidence. Elicitation-gap ambiguity (chapter 05 pitfall 3) is the primary interface risk.
- **mod-107 (EACC).** Feeds *Control* leg's containment-contract solution nodes. The tamper-evident log stream is the primary runtime-enforcement evidence.
- **mod-108 (Monitors).** Feeds *Control* leg's monitor-coverage solution nodes. Adaptive-attack-survival curves are the primary defensibility signal.
- **mod-110 (Control / Deception).** Feeds *Control* leg's adversarially aligned control-eval and *Trustworthiness* leg's disposition stress-tests. A Control × Trustworthiness composition without mod-110 evidence is under-defended at level 40.
- **mod-111 (Automated Red Team).** Feeds the coverage-matrix evidence that both *Control* and *Trustworthiness* legs' adaptive-attack-survival claims cite.
- **mod-112 (Disclosure).** Consumes the case for external audiences; the handoff is one-directional and well-defined above.

## Out of scope

- **Legal argumentation.** The case argues technical claims about system behaviour; legal argumentation about liability, regulatory duty, or contractual obligation is out of scope. Legal counsel consumes the case; the case does not construct legal argument.
- **Novel-alignment research at PhD-publication depth.** The Trustworthiness leg cites frontier alignment methodology (mod-110); it does not *produce* new methodology. Producing new alignment methodology is the frontier-lab safety-research-scientist ladder's craft, which the curriculum-scope README explicitly defers to.
- **Program leadership at organisation scope.** The case is a technical artefact; the program that runs the annual safety-case cadence, the board-level reporting, the regulator relationship, is `head-of-ai-governance` (level 60) and above.
- **Statistical calibration at published-methodology depth.** Best-of-N confidence intervals, judge-vs-human calibration, elicitation-gap statistical treatment — these are `model-evaluation-engineer` (peer, level 30, ML Engineering family) craft consumed by mod-106. The case cites the calibration; it does not produce it.

## The boundary in one sentence

Mod-109 owns the **safety case** — the structured, defeasible, evidence-grounded argument for a specific deployment. `ai-evaluation-engineer` owns the **release-assurance methodology** that consumes the case as one release-gating artefact among many. `senior-ai-governance-architect` owns the **control library and framework composition strategy** the case is authored against. mod-112 owns the **disclosure engineering** that shapes the case for external audiences.

## Common misreadings to avoid

- **"The safety case is a release-assurance artefact, so it belongs to `ai-evaluation-engineer`."** No. The safety case is *consumed by* release-assurance, but it is authored by mod-109. Release-assurance packages it, hashes it, routes it — but does not compose the argument.
- **"The control library is a mod-109 artefact because the case cites it."** No. The library is *cited by* mod-109 and owned by `senior-ai-governance-architect`. Citing an artefact does not confer ownership.
- **"Once the case is signed, mod-109 is done."** No. The case iterates across the deployment lifecycle: undeveloped-goal closures, framework-version drift, evidence-provenance refreshes, rebuttal-register updates in response to actual reviewer findings. mod-109 owns the case until the deployment retires.
- **"mod-112 owns disclosure, so mod-109 does not need to think about external audiences."** No. mod-109 authors the case with an awareness of what mod-112 will shape it into. A case whose structure makes external disclosure impossible is a case that fails the downstream boundary.
- **"The peer roles will figure out the case's requirements on their own."** No. The peer roles need the case's *interface* — the citable node IDs, the closure-plan register, the rebuttal register, the framework version pin. mod-109 designs the interface; the peer roles consume it. Without the interface, release-assurance re-derives from prose and drifts.
- **"The safety engineer signs the case."** No. The safety engineer *authors* the case. Signing is done by the internal-safety-review body (RSO / Preparedness Committee / Safety Board), with peer-role co-signatures per the architect's routing table. Author and signer are separated by design.

## Summary

- Mod-109 owns the **safety case as artefact**; peer roles own the platforms, taxonomies, and disclosure shaping the case plugs into.
- **`ai-evaluation-engineer`** (peer / next-up, level 35) owns the **release-assurance methodology** that consumes the case; mod-109 provides a version-pinned, node-ID-stable, rebuttal-registered case as input.
- **`senior-ai-governance-architect`** (level 50) owns the **control library, framework composition, and cross-jurisdictional reconciliation**; mod-109 authors cases against the architect's library and composition.
- **mod-112** (Frontier-Safety Program and Disclosure) consumes the case for external audiences; the handoff is one-directional and interface-defined.
- Findings from mod-109 that require platform work route to `ai-evaluation-engineer`; findings that require architectural work route to `senior-ai-governance-architect`; findings that require disclosure work route to mod-112.
- Out of scope: legal argumentation, PhD-depth alignment research, org-level program leadership, published-methodology statistical calibration.
- The safety-engineering role's craft at level 40 is the **case** and its **rebuttal defensibility**. Everything else routes.
