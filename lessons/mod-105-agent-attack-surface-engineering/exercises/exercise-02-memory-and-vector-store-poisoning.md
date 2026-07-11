# exercise-02: Memory and Vector-Store Poisoning

**Estimated effort:** 3 hours

## Objective

Engineer two poisoning attacks (chapter 03) against one memory-enabled and RAG-enabled agent, populate the poisoning bench of the AASS, and produce persistence, cross-tenant, and retriever-blast-radius measurements per attack. Attacks must include at least one **content-injection** attack (PoisonedRAG family) and at least one **embedding-space nearest-neighbour** attack (AgentPoison family).

The exercise turns chapter 03 from taxonomy into instrumented measurement. It is the module's answer to the question "how bad is the memory / vector-store surface for this agent, and under what defensive postures does it survive?"

## Prerequisites

- Read chapter 03 (memory and vector-store poisoning) end-to-end.
- Read Zou et al. (2024) PoisonedRAG and Chen et al. (2024) AgentPoison — at least the abstract, threat model, methodology, and evaluation sections.
- Read Greshake et al. (2023) §7 on memory persistence.
- Read mod-102 chapter 02 (surface 3) if you need a refresher on the store surface.
- Have a working target agent with:
  - A **memory / user-profile store** the agent can read and write.
  - A **RAG corpus** the agent retrieves from, with an embedding model and retriever whose configuration you can inspect and version.
  - Ideally, more than one user (real or simulated) so the cross-tenant reach measurement is meaningful.
- Access-controlled harmful-payload store set up per mod-103 chapter 06.

## Requirements

### Attack 1 — Memory / user-history poisoning (sub-family 1 or 2 from chapter 03)

Author a payload that lands in the memory store during session S1 and fires on the target user's session S2 producing an operator-observable effect (e.g., a tool call the operator would not have authorised, a response bias, a leaked field). Requirements:

1. **Design.** Name the store (conversation memory, user profile, scratchpad, etc.), the write pathway (which tool wrote, under whose authority, with what trust label), and the read pathway that fires it.
2. **Measure persistence horizon.** Report turns-to-forget and sessions-to-forget under the store's default policies (summarisation, eviction, hard-wipe). Include a delete-pathway completeness check: does the operator's "wipe memory" pathway remove *every* copy?
3. **Measure cross-tenant reach** if the store admits any cross-user or cross-tenant read path. If it does not, state so explicitly and cite the schema / architecture doc that justifies the claim.
4. **Measure per posture** (raw / provenance labels on read / per-tenant isolation / memory-write HITL / memory-write anomaly detector). Report ASR per posture.

### Attack 2 — RAG corpus / embedding-space poisoning

Author *either* a PoisonedRAG-style content-injection attack *or* an AgentPoison-style embedding-space nearest-neighbour backdoor — bonus if you produce both. Requirements:

1. **Design.** Name the corpus, the write-access model (who can upload, under what authority), the retriever and its embedding model + version, and the target query family the poisoning aims at.
2. **Measure retrieval signature.** Number of poisoned documents required, hit rate on target queries, benign precision impact (does the poisoning surface on unrelated queries?), retriever-transfer measurement across at least one alternative embedding model or retriever configuration.
3. **Measure downstream effect rate.** Of retrievals that surfaced the poisoning, on what fraction did the model *adopt* the poisoned content into its answer or into a tool call?
4. **Measure per posture** (raw / trusted-source-labelling / retrieval-consistency pass / anomaly detector on embedding pipeline / periodic re-embedding). Report ASR per posture.

## Deliverables

- `poisoning-<slug>/design.md` per attack: the store, the write / read pathways, the defanged payload shape, the target query family (for RAG).
- `poisoning-<slug>/persistence.yaml`: turns-to-forget, sessions-to-forget, hard-wipe completeness. Cross-tenant reach with evidence.
- `poisoning-<slug>/retrieval_signature.yaml` (RAG attacks only): n_poisoned_docs, hit_rate, benign_precision_impact, retriever_transferability.
- `poisoning-<slug>/coverage_row.yaml`: matrix rows per posture per attack.
- `poisoning-<slug>/judge/` and the human-agreement number per cell.
- `harmful-payload-store/manifest.yaml`: handles for poisoned documents / adversarial embedding entries.
- `README.md` in the exercise directory: target agent, model + snapshot, embedding-model + version, framework version, and a summary table of per-posture ASR and the persistence / reach numbers.
- `boundary_routing.yaml`: which peer / module owns each proposed remediation class (chapter 07).

## Acceptance criteria

- **Two attacks authored** in the two sub-families named above.
- **Persistence horizon reported** for the memory attack (turns and sessions), including a hard-wipe completeness check.
- **Cross-tenant reach reported** with evidence (an observed cross-user effect *or* an architectural argument that reach = 0 with a schema pointer).
- **Retrieval signature reported** for the RAG / embedding attack (n_poisoned_docs, hit_rate, benign_precision_impact, at least one transfer measurement).
- **Downstream effect rate reported** (not just retrieval hit rate).
- **Every attack is measured under at least four postures**, with ASR per posture and utility per posture on a paired benign query set.
- **The boundary-routing block routes each finding** to `senior-agentic-ai-engineer` (architecture patterns), `ai-infra-security` (store hardening), and/or mod-107 / mod-108 with proposed remediation classes.
- **No working payload committed to this repo.**

## Stretch goals

- **AgentPoison recipe.** Implement the embedding-space optimisation recipe from Chen et al. (2024) against your target's retriever. Report the trigger recall × benign precision Pareto curve.
- **PoisonedRAG stealth.** Design your content-injection attack so its embedding does not cluster far from other corpus entries (defeats a naive outlier-detection defence). Report the anomaly-detector's per-cell survival rate before and after the stealth reworking.
- **Feedback-loop poisoning.** If the target's memory writes are read into any downstream fine-tune or preference-learning pipeline (thumbs-up feedback, DPO), test whether the poison propagates into training data. Route findings to the peer role responsible for that pipeline.
- **Cross-agent memory.** If your target is part of a multi-agent graph (chapter 05), test whether a memory poison written by one agent fires on a peer agent's session.
- **Retriever version-drift.** Test whether re-embedding the corpus under a new retriever version breaks each of your attacks. Report the break rate — this is the operational-hygiene defence's efficacy figure.

## Guardrails

- Do not commit poisoned documents or adversarial embedding entries to this repo.
- Do not upload poisoned content to production knowledge bases you do not own or have not been authorised to test. The default target is a self-hosted RAG corpus and a self-hosted memory store on a local instance of your framework.
- If a poisoning attack you author lands against a production frontier deployment during authorised testing, route the finding through the mod-112 coordinated-disclosure workflow before publishing.
