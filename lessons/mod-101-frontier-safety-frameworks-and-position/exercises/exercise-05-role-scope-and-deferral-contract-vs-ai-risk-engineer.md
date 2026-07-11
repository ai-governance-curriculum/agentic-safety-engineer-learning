# exercise-05 — Role Scope and Deferral Contract vs. `ai-risk-engineer`

**Estimated effort:** 2 hours
**Prerequisite chapters:** 01 (helpful: all).

## Objective

Author the **level-40 role scope and deferral contract** for this role, explicitly against the level-25 `ai-risk-engineer` prerequisite and the peer / next-up roles named on the level ladder. Produce a contract you could hand to a hiring manager, a new hire, or a peer at level 25, 30, 35, 50, or 60 to answer the question: *what does this role own that mine does not, and where do we hand off?*

## Problem statement

You just joined a hypothetical AI Governance function at level 40 as its Agentic Safety & Red-Team Engineer. Your director asks you to draft a **role-scope and deferral-contract document** to publish internally. The document has to keep three audiences in sync:

- The **level-25 `ai-risk-engineer`** on the same team, who has been asked "why don't you own this?" for various frontier-safety artifacts and needs a clean answer.
- The **level-35 `ai-evaluation-engineer`**, the **level-30 peers** (AI eval engineer, model evaluation engineer, fine-tuning engineer), and the **level-35 `security` / `ai-infra-security`** engineer, who all have overlapping surface area and need to know where you take over and where they do.
- The **level-50 architect** and the **level-60 program lead**, who inherit your evidence upward and need to know what evidence to expect from you.

## Requirements

Produce a single Markdown document, ~1500–2500 words. Structure:

### 1. Role statement

- One paragraph naming the role, the level, the family, and the one-sentence scope.
- Cite the two documents this scope is grounded in ([`CURRICULUM.md`](../../../CURRICULUM.md) and [`JOB_REQUIREMENTS.md`](../../../JOB_REQUIREMENTS.md)).

### 2. The ownership rule

- Restate the ownership rule from chapter 01 in your own words.
- Give one worked example of the rule applied to a specific artifact (pick one from the artifact map in chapter 02, 03, 04, 05, or 06).

### 3. What this role owns

- Table listing at least the 12 modules from the CURRICULUM and, for each, one to three concrete artifacts this role owns.
- Include a "how this differs from level 25" column, naming the concrete difference against `ai-risk-engineer`.

### 4. What the level-25 `ai-risk-engineer` prerequisite already owns (that this role does not re-teach)

- Enumerate the prerequisite fluency this role assumes, per the list in chapter 01. Structure as bullets tied to the peer's curriculum: framework fluency (NIST AI RMF, ISO/IEC 42001 / 23894 / 42005 / 25059, EU AI Act Articles 9–15, SR 11-7, FDA GMLP), harm modelling, risk quantification, general LLM red-teaming, general adversarial ML, fairness / bias / explainability, privacy-risk engineering, general guardrail engineering, MRM shape, risk-register wiring, incident RCA, cross-functional program at team scope.
- For each item, note where the peer's craft *ends* and where the frontier-agent depth *begins*. Example: "General guardrail engineering (Llama Guard, ShieldGemma, NeMo Guardrails, Constitutional Classifiers at general depth) — level 25 owns; level 40 adds fine-tuning the classifier, adaptive-attack survival curves, and defence-in-depth composition."

### 5. Peer boundaries (level 30 and 35)

- One row per peer role: `ai-eval-engineer` (30, AI Engineering), `model-evaluation-engineer` (30, ML Engineering), `fine-tuning-engineer` (30), `security` / `ai-infra-security` (35), `ai-evaluation-engineer` (35, Governance).
- Per row: what the peer owns, what this role owns, and the interface artifact (what does the level-40 engineer *send* to the peer, what does the level-40 engineer *receive* from the peer).

### 6. Next-up boundaries (level 50, 60, 70)

- Per next-up role: what evidence this role produces that they consume, and what decisions they own that this role does not.

### 7. Out-of-scope

- Legal opinion, SOC / DFIR handling, frontier-lab safety-research-scientist ladder. For each, one line naming the boundary and the lane it belongs to.

### 8. A worked deferral-contract YAML

- Author a full deferral-contract YAML block (using the template from chapter 01) for a *concrete* artifact — pick one from the artifact maps in chapters 02–06. The YAML must include `artifact`, `level_of_owner`, `deferred_down_to`, `deferred_sideways_to`, `consumed_by`, and (where applicable) `out_of_scope_to`.

### 9. Escalation and handoff patterns

- Author three short scenarios naming when this role should escalate up and when it should hand down. Examples: "a peer's guardrail deployment causes a regression on the internal safety-eval battery — you flag and hand back to the peer, you do not fix the guardrail" ; "the safety-case argument you author will be relied on for a board decision — you produce the evidence and the safety case, the architect owns the cross-jurisdiction reconciliation, the head-of-governance owns the board presentation".

## Starter guidance

- Do not restate the whole level ladder in your own words — cite chapter 01 and add the *specific* boundaries with respect to your (or your hypothetical) team's staffing.
- Use *artifact* language, not *skill* language. "Dangerous-capability elicitation report" is precise; "we do dangerous-capability evaluation" is fuzzy.
- If your team does not currently have a level-50 architect or a level-60 head, note the *decision-authority gap* and where it lands (typically upward on the level ladder to whoever occupies the gap).
- Do not invent peers your organisation does not have. If you are the sole level-40 in a small team and level-25 through level-30 do not exist as separate roles, note who effectively covers the level-25 prerequisite (typically yourself, meaning you *personally* have to be fluent in it).

## Acceptance criteria

- ✅ Single Markdown document, ~1500–2500 words.
- ✅ All nine sections above are present.
- ✅ The "what this role owns" table has one row per module (12 rows) with at least one concrete artifact per module.
- ✅ The prerequisite-fluency section names each of the level-25 substrates listed in chapter 01, with the "ends where / begins where" delta.
- ✅ Peer boundaries and next-up boundaries are populated for all named roles.
- ✅ The deferral-contract YAML is valid YAML, references a concrete artifact, and includes all required fields.
- ✅ At least three escalation / handoff scenarios.
- ✅ Reads as an internal working document, not a marketing pitch. Names names of roles, not "the team".

## Stretch goals

- Add a matrix at the top of the document mapping each of the 25 requirement themes in the [`JOB_REQUIREMENTS.md`](../../../JOB_REQUIREMENTS.md) table to the specific artifact(s) this role authors. This closes the loop between hiring intent and role scope.
- Produce a **role-onboarding checklist** for a new hire at level 40: which of the 12 modules to prioritise first based on your team's current gaps, and what artifacts to author in the first 30 / 60 / 90 days.
- Add a **role-offboarding checklist**: what artifacts must be handed over cleanly before the role is left vacant.

## Deliverable location

Personal notes or private repo. Not this course repo. Solutions live in the paired solutions repo.
