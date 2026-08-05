# Resources for mod-107-excessive-agency-containment

Curated references for engineering an Excessive-Agency Containment Contract (EACC). Version-pin every source when you cite it in an artefact — most of these are living documents.

## Standards and primary sources

### OWASP GenAI / LLM Top 10

- [OWASP Top 10 for LLM Applications 2025 — LLM06: Excessive Agency](https://genai.owasp.org/llmrisk/llm062025-excessive-agency/) — the framing this module organises around: excessive functionality, excessive permissions, excessive autonomy.
- [OWASP Top 10 for LLM Applications 2025 — LLM05: Improper Output Handling](https://genai.owasp.org/llmrisk/llm052025-improper-output-handling/) — the failure mode a leaky wrapper produces.
- [OWASP Top 10 for LLM Applications 2025 — LLM02: Sensitive Information Disclosure](https://genai.owasp.org/llmrisk/llm022025-sensitive-information-disclosure/) — the exfil failure mode network isolation and audit-log scoping contain.
- [OWASP GenAI Security Project — Agentic AI Threats and Mitigations](https://genai.owasp.org/resource/agentic-ai-threats-and-mitigations/) — the source of the Tool Misuse, Privilege Compromise, Resource Overload, Overwhelming Human-in-the-loop, Human Manipulation, and Repudiation & Untraceability entries this module contains.
- [OWASP LLM & Generative AI — Security Landing](https://genai.owasp.org/) — the umbrella project; check for updated deliverables and working drafts.

### NIST

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the *Manage* function's human-oversight guidance is the reference posture for consequential-action HITL. <!-- needs-research: pin the current NIST AI RMF revision (1.0, later revisions, or the GenAI Profile) as it evolves. -->
- [NIST AI 100-2 E2025 — Adversarial Machine Learning: Taxonomy and Terminology](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.100-2e2025.pdf) — the agent-specific attack sections describe classes of misuse capability gates blunt.
- [NIST SP 800-207 — Zero Trust Architecture](https://csrc.nist.gov/publications/detail/sp/800-207/final) — the *authorise every request* posture translated from network to tool-call.
- [NIST SP 800-190 — Application Container Security Guide](https://csrc.nist.gov/publications/detail/sp/800-190/final) — reference posture for container-level isolation; not sufficient for adversary-executed code on its own.
- [NIST SP 800-92 — Guide to Computer Security Log Management](https://csrc.nist.gov/publications/detail/sp/800-92/final) — the reference posture for security-relevant logging behind chapter 03's audit-log requirements.
- [NIST SP 800-61 Rev. 3 — Computer Security Incident Handling Guide](https://csrc.nist.gov/publications/detail/sp/800-61/rev-3/final) — the four-phase IR shape (preparation → detection & analysis → containment, eradication & recovery → post-incident activity) the kill-switch fire contract honours.
- [NIST SP 800-63-3 — Digital Identity Guidelines (AAL2/AAL3)](https://pages.nist.gov/800-63-3/) — assurance levels behind the per-decision step-up authentication in the HITL flow.

### Regulatory frameworks

- [EU AI Act — Regulation (EU) 2024/1689](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — Article 14 (Human oversight), Article 15 (Accuracy, robustness, cybersecurity), and Article 73 (Serious-incident reporting) are the regulatory floor for the HITL contract, the sandbox invariants, and the post-fire disclosure obligations. <!-- needs-research: verify the current Article 73 implementing-regulation cadence (initial notification / follow-up / final report) once published. -->
- [ISO/IEC 42001:2023 — AI management systems](https://www.iso.org/standard/81230.html) — the management-system standard operators can align the EACC review process to.

### Frontier-safety framework anchors

- [Anthropic — Responsible Scaling Policy (RSP)](https://www.anthropic.com/rsp) — the rollback-trigger framing behind chapter 05's kill-switch modes. mod-101 covers this in depth.
- [OpenAI — Preparedness Framework](https://openai.com/safety/preparedness/) — safeguards / safety-response section names the mitigations a runtime stop button implements.
- [Google DeepMind — Frontier Safety Framework](https://deepmind.google/public-policy/ai-safety/frontier-safety-framework/) — Critical Capability Level responses; the FSF's halt-deployment language is the framing for org-wide stop-button orchestration.

## Sandbox and runtime isolation

- [gVisor project](https://gvisor.dev/) — Google's user-space Linux syscall interceptor; read the *Architecture Guide* and *Security Model* sections.
- [Firecracker microVM](https://firecracker-microvm.github.io/) — AWS-authored KVM-backed microVM; read the *Design* doc for the threat model.
- [Linux `namespaces(7)` man page](https://man7.org/linux/man-pages/man7/namespaces.7.html), [`seccomp(2)`](https://man7.org/linux/man-pages/man2/seccomp.2.html), [`cgroups(7)`](https://man7.org/linux/man-pages/man7/cgroups.7.html) — the OS primitives every user-space sandbox composes.
- [Kubernetes — Pod Security Standards](https://kubernetes.io/docs/concepts/security/pod-security-standards/) — the Restricted profile is the base case for containerised sandboxes; harden further for adversary-executed code.
- [Docker — seccomp profile reference](https://docs.docker.com/engine/security/seccomp/) — the default profile is broad; the exercise-02 test suite tightens it.
- [Kata Containers](https://katacontainers.io/) — hardware-virtualised container runtime; another point in the sandbox design space alongside gVisor and Firecracker.
- [Bubblewrap (`bwrap`)](https://github.com/containers/bubblewrap) — lightweight unprivileged sandboxing used by Flatpak; useful reference for minimal namespace-only isolation.
- [Cloudflare Workers isolate architecture](https://blog.cloudflare.com/cloud-computing-without-containers/) — the V8-isolate model for isolating adversarial JS; instructive for the trade-offs between speed and isolation depth.
- [OpenAI — Sandbox for Code Interpreter (Assistants API tools reference)](https://platform.openai.com/docs/assistants/tools/code-interpreter) — vendor-published overview of the hosted code-interpreter posture.
- [Anthropic — Claude Code and computer use documentation](https://docs.claude.com/en/docs/agents-and-tools/computer-use) — vendor documentation for a computer-use agent; the containment considerations sections are load-bearing. <!-- needs-research: pin the URL and revision as Anthropic's docs move. -->

## Threat catalogues and taxonomies

- [MITRE ATT&CK — Container Escape (T1611)](https://attack.mitre.org/techniques/T1611/) — the technique catalogue the sandbox must resist.
- [MITRE ATLAS — Adversarial Threat Landscape for AI Systems](https://atlas.mitre.org/) — the tactic / technique framework mod-102 uses; cross-reference tool-misuse and privilege-compromise techniques when authoring the EACC.
- [CWE-250 — Execution with Unnecessary Privileges](https://cwe.mitre.org/data/definitions/250.html) — the PoLA credential principle in CWE form.
- [CWE-266 — Incorrect Privilege Assignment](https://cwe.mitre.org/data/definitions/266.html) — the side-effect-scope failure in CWE form.
- [CWE-778 — Insufficient Logging](https://cwe.mitre.org/data/definitions/778.html) — the audit-log failure that Repudiation & Untraceability produces.

## Identity, credentials, and secrets

- [SPIFFE / SPIRE](https://spiffe.io/) — workload-identity fabric; the reference shape behind chapter 06's per-wrapper cryptographic identity.
- [OAuth 2.0 Token Exchange (RFC 8693)](https://www.rfc-editor.org/rfc/rfc8693) — the on-behalf-of exchange that lets a tool call operate as the end user rather than the operator.
- [AWS STS — AssumeRole and session tags](https://docs.aws.amazon.com/STS/latest/APIReference/welcome.html) — cloud-provider primitives for the credential broker in AWS environments.
- [GCP Workload Identity Federation](https://cloud.google.com/iam/docs/workload-identity-federation) — GCP-side equivalent.
- [HashiCorp Vault — Dynamic Secrets](https://developer.hashicorp.com/vault/docs/secrets) — vendor documentation for a short-TTL secret broker.

## Audit logs and tamper-evidence

- [RFC 6962 — Certificate Transparency](https://www.rfc-editor.org/rfc/rfc6962) — the Merkle-tree log pattern behind chapter 03's tamper-evident action log.
- [RFC 3161 — Time-Stamp Protocol (TSP)](https://www.rfc-editor.org/rfc/rfc3161) — trusted timestamps for the highest-integrity streams.
- [Trillian — a transparent, tamper-evident log](https://github.com/google/trillian) — the open-source implementation of the RFC 6962 pattern; useful reference architecture.
- [Sigstore — Rekor transparency log](https://docs.sigstore.dev/logging/overview/) — a production Trillian deployment; instructive for the operational shape.
- [AWS S3 — Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html), [Azure Blob — Immutable storage](https://learn.microsoft.com/en-us/azure/storage/blobs/immutable-storage-overview), [GCS — Bucket Lock](https://cloud.google.com/storage/docs/bucket-lock) — WORM storage substrates for the log sink.

## Human-in-the-loop, oversight, and consent UX

- [EU AI Act — Article 14 (Human oversight)](https://eur-lex.europa.eu/eli/reg/2024/1689/oj) — the four capacities (recognise-and-understand, correctly-interpret, override, stop) every HITL flow in this module must support.
- [NIST AI RMF — Manage function](https://airc.nist.gov/AI_RMF_Knowledge_Base/Playbook/Manage) — playbook entries for consequential-action oversight.
- [ACM — "Where Are Human Subjects in Big Data Research?"](https://dl.acm.org/doi/10.1145/2909824) — background on consent revocability that informs chapter 04's revocable-consent contract.
- [Anthropic — Constitutional AI paper](https://arxiv.org/abs/2212.08073) — the model-side norms behind why HITL cannot rely on the model to self-refuse consequential actions.

## Agent-protocol and platform references

- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) — the wire protocol many tool-using agents standardise on; the wrapper implements the security-considerations sections on the operator side. <!-- needs-research: pin the current MCP spec version and any published security hardening notes / erratas. -->
- [OpenAI — Function Calling API reference](https://platform.openai.com/docs/guides/function-calling) — the tool-schema shape the gate's schema validator is applied against on the OpenAI-side.
- [Anthropic — Tool Use documentation](https://docs.claude.com/en/docs/build-with-claude/tool-use) — the equivalent on the Anthropic side. <!-- needs-research: pin the URL as Anthropic's documentation site restructures. -->
- [LangChain — PythonREPLTool / shell tools security notes](https://python.langchain.com/docs/integrations/tools/python) — vendor cautions on adversary-controlled code paths.

## Related programmatic guidance

- [UK AI Safety Institute — Evaluations approach](https://www.aisi.gov.uk/) — public-sector evaluations posture; cross-reference the containment claims in operator-side safety cases.
- [US AI Safety Institute — mission and activities](https://www.nist.gov/artificial-intelligence/artificial-intelligence-safety-institute) — programmatic anchor for domestic frontier-safety collaboration.
- [MLCommons — AILuminate benchmark](https://mlcommons.org/benchmarks/ailuminate/) — reference benchmark suite; the hazard categories overlap with the tool-misuse and privilege-compromise entries the EACC contains.

## Incident reference material

- [OWASP GenAI — Incident Response for GenAI](https://genai.owasp.org/resource/) — check the current published incident-response guidance in the OWASP GenAI Security Project deliverables list. <!-- needs-research: link to the specific incident-response deliverable if / when it is published as a standalone artefact. -->
- [MITRE — AI Incident Sharing Initiative](https://ai-incidents.mitre.org/) — a case-based catalogue useful for the false-positive-cost analysis and the post-incident review.
- [AI Incident Database (Responsible AI Collaborative)](https://incidentdatabase.ai/) — third-party incident catalogue for grounding threat-model priors.

---

Version-pin what you cite. If a source has moved, mark it `<!-- needs-research: ... -->` in your artefact rather than deleting the citation.
