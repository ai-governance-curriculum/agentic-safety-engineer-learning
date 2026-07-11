# exercise-06 — Certifications Portfolio Planner

**Estimated effort:** 1 hour
**Prerequisite chapters:** 09 (helpful: 01).

## Objective

Author a **personal certifications-and-contribution plan** for the next 12 months that maps to your current level and the level(s) you are targeting, honestly weighs the cost of each investment against the hiring signal it produces, and treats *contribution history* as the primary signal above any certification stack.

## Problem statement

You are your own hiring manager for the next 12 months. Given the current state of the AI-safety-hiring market (per chapter 09), your current credential and contribution portfolio, and the level you are targeting (level 40 if you are new to the role, level 50 if you are moving up, level 30 if you are moving toward this role from a lower-level track), decide what to invest in.

The exercise is short on purpose. The point is not to design a curriculum — it is to **make a specific plan** and defend it.

## Requirements

Produce a single Markdown document, ~600–1200 words. Structure:

### 1. Starting-state snapshot

- Current role and level (or aspirational level if you are targeting a first role at level 40).
- Current relevant certifications (list, not narrative).
- Current relevant contribution history (papers, blog posts, open-source PRs to safety tooling, published system-card contributions, red-team writeups, FMF publications, AISI submissions — list, not narrative).
- One-paragraph honest assessment of gaps.

### 2. Target-state snapshot

- Target role and level 12 months from now.
- One-paragraph honest assessment of the differentiators the target role expects.

### 3. Portfolio plan

Author a table with these columns:

| Investment | Cost (hours) | Signal (posting-frequency estimate) | Load-bearing at target level? | Timing (Q1 / Q2 / Q3 / Q4) | Decision (do / defer / skip) |

Populate at least these rows:

- IAPP AIGP
- ISO/IEC 42001 lead-implementer
- ISO/IEC 42001 lead-auditor
- ForHumanity Independent AI Auditor
- BABL AI algorithm-auditor
- One published Frontier Model Forum working-paper contribution (or equivalent industry-body publication)
- One open-source contribution to Inspect / PyRIT / garak / equivalent safety-eval harness
- One published red-team writeup (personal blog, arXiv preprint, or organisation-facing writeup) on a specific frontier-agent attack surface
- One system-card contribution (if you are inside a lab that publishes system cards)
- One AISI-submission contribution (if you are inside a lab that engages with an AISI)

You may add rows for other credentials or contributions (specific university courses, other certification bodies, adjacent-security credentials like CISSP or OSCP if relevant to a cross-training goal).

### 4. Defence

For each row where you selected "do", one to three sentences defending the decision: why this specifically, why this quarter, and what evidence you expect to have at the end of it that you did not have at the start.

For each row where you selected "defer" or "skip", one sentence explaining why the alternative allocation is stronger.

### 5. Anti-pattern check

- One paragraph naming which certification-portfolio anti-pattern you were most tempted by (per chapter 09: substituting certifications for contribution history, stacking certifications for filter optimisation, over-investing in bias-audit credentials for a role that does not need them, earning 42001 LI before you need it).
- One sentence naming how you will avoid it.

### 6. Six-month checkpoint

- One paragraph naming the review you will run six months from now to check whether the plan is working: what evidence would cause you to accelerate, defer, or drop a planned investment.

## Starter guidance

- Be honest. This exercise is only useful if the plan is realistic. If you plan 300 hours of study over 12 months and know you will study 60, the plan is fiction.
- The **contribution rows** matter more than the certification rows at level 40 and above. If you do not have at least one contribution row marked "do", question the plan.
- Keep the plan short. The exercise is capped at one hour; the plan should be capped at one page.
- Reuse the level-40 rubric in chapter 09; do not re-derive it.
- If your target is not level 40 — you are moving *toward* the role from level 25 / 30, or you are moving up to level 50 — adjust the rubric weights accordingly and note the adjustment.

## Acceptance criteria

- ✅ Single Markdown document, ~600–1200 words.
- ✅ All six sections above.
- ✅ Portfolio-plan table populated with at least the ten rows named above (plus any additional rows you choose).
- ✅ At least one contribution row marked "do".
- ✅ Each "do" row has a defence.
- ✅ Each "defer" or "skip" row has a one-line alternative-allocation explanation.
- ✅ Anti-pattern check is present and honest.
- ✅ Six-month checkpoint criteria are specific enough to act on.

## Stretch goals

- Add a **second target** to the target-state snapshot — one primary target, one fallback target — and check whether the plan serves both.
- Add a **cost-of-abandonment** column to the portfolio table: if you start an investment and abandon it, what is lost? Some investments (a 42001 LA course you paid for) have real sunk-cost exposure; some (an open-source PR you draft) do not.
- Add a **team-transfer plan**: which of the contribution investments produce artifacts your current team benefits from, so the investment is dual-purpose.

## Deliverable location

Personal notes or private repo. Not this course repo. Solutions live in the paired solutions repo.
