# mod-111-automated-and-scaled-red-teaming: Automated and Scaled Red-Teaming

> Scaffolded by `aicg org execute-plan`. Lecture chapters and exercise content are authored on subsequent autonomous cycles.

**Estimated effort:** 14 hours

## Learning objectives

- Orchestrate a scaled red-team program with UK AISI Inspect + Microsoft PyRIT + NVIDIA garak + Promptfoo red-team — coverage matrix across attack technique × behaviour category × model version × decoding config
- Engineer LLM-vs-LLM attacker loops — PAIR / TAP / RL-based attackers; fine-tune a dedicated adversarial-attacker model on your organisation's failure-mode corpus (with harmful-payload discipline) and measure diversity + attack-success-rate against production models
- Adopt StrongREJECT-shape judge methodology — differentiating a jailbreak that succeeds behaviourally from one that produces an empty / hedged / plausibly-refused output; compare LLM-judge to human-judge and calibrate
- Engineer coverage + population-scale replay — evals over trace populations, coverage-matrix reporting, seeded-attack replay bundle, decoding + model + prompt + tool-response hashes pinned for reproducibility
- Adopt harmful-payload storage discipline — evaluation set hosted outside the source repo (private HF org / S3 bucket with per-role IAM), harmful-content redaction in issue trackers + logs, need-to-know access, and legal-review gate for CBRN / cyber-offense payloads
- Cite the boundary to `ai-risk-engineer` (prerequisite, level 25) on garak / PyRIT / Promptfoo at general depth; this module adds the LLM-vs-LLM attacker + fine-tuning + scaled orchestration
- Cite the boundary to `ai-eval-engineer` (peer, level 30) on the trace / judge / eval-gated CI plumbing this suite consumes

## Structure

- `01-…md` … `0N-…md`: lecture chapters.
- `exercises/`: per-exercise prompts.
- `labs/`: long-form hands-on labs.
- `quizzes/`: knowledge checks.
- `resources.md`: external references.
