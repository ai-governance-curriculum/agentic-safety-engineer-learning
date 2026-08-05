# exercise-02: Sandboxed Tool Execution — Hands-On

**Estimated effort:** 3 hours

## Objective

Build a **sandbox-contract test suite** — a set of adversarial probes that verifies a real sandbox implementation honours the four containment axes from chapter 02 — and run it against a sandbox of your choice. The output is the test-suite code, a run report, and section 7 of the EACC (the sandbox contract) populated with the *measured* invariants (isolation class, propagation latency, egress default, per-tool profile hashes) rather than nominal ones.

You are not building a sandbox platform. You are proving that the platform you plan to depend on honours the invariants the EACC will pin to it.

## Prerequisites

- Read chapter 02 (Sandboxed Tool Execution) end-to-end.
- Skim [gVisor Security Model](https://gvisor.dev/docs/architecture_guide/security/), the [Firecracker Design doc](https://github.com/firecracker-microvm/firecracker/blob/main/docs/design.md), and [NIST SP 800-190](https://csrc.nist.gov/publications/detail/sp/800-190/final).
- Have access to at least one candidate sandbox platform. Any of:
  - gVisor (`runsc` runtime under Docker / containerd), or
  - Firecracker via a wrapper (Kata Containers, Weave Ignite, or a hand-rolled `firecracker-go-sdk` harness), or
  - A hardened OCI-container runtime with a narrow seccomp profile (least isolated; explicitly document the ceiling), or
  - A hosted code-interpreter sandbox where you can enumerate the invariants from the vendor's documentation and probe from inside.
- A Linux host you can run privileged containers on (or a VM you own). Do not run the adversarial probes inside a shared corporate laptop's default sandbox.
- Completed exercise 01, or have a candidate EACC that section 7 can be populated for.

## Requirements

### Part A — Author the probe catalogue

Author a probe catalogue `probes/README.md` that, per containment axis, enumerates the probes and the expected invariant each verifies. Every probe carries a **name**, a **pre-condition** (what state the sandbox must be in for the probe), an **action** (the operations the probe performs), and a **pass criterion** (what "the invariant held" looks like in the probe's output).

Cover, at minimum:

**Axis 1 — Compute isolation**

- `probe-compute-01` — attempt to `unshare(CLONE_NEWNS)` from inside the sandbox; pass = EPERM.
- `probe-compute-02` — attempt `ptrace(PTRACE_ATTACH)` against the sandbox's own PID 1; pass = EPERM.
- `probe-compute-03` — attempt `mount("/dev/sda1", ...)`; pass = EPERM.
- `probe-compute-04` — attempt a syscall known to be outside the seccomp allow-list (e.g., `keyctl`, `kexec_load`, `perf_event_open`); pass = EPERM.
- `probe-compute-05` — enumerate the syscalls the interpreter *actually needs* under a representative workload; report the diff against the current seccomp profile.
- `probe-compute-06` — attempt `getpid()` from inside a spawned child and verify the PID namespace is isolated (`getpid` = a low number, not the host's).

**Axis 2 — Network isolation**

- `probe-net-01` — attempt an outbound TCP connect to `169.254.169.254:80` (cloud metadata); pass = connection refused / no route.
- `probe-net-02` — attempt outbound DNS `A` lookup against `8.8.8.8`; pass = refused unless the platform is intentionally allow-listing.
- `probe-net-03` — attempt HTTPS GET to a random attacker-shaped domain not in the allow-list; pass = proxy 403 / no route.
- `probe-net-04` — attempt outbound TCP connect to the *internal* network (a specific known internal service the sandbox should *not* reach); pass = no route.
- `probe-net-05` — attempt to reach an allow-listed domain and record the egress-proxy log entry; pass = request succeeded *and* proxy log records the request with the tool ID and session ID.
- `probe-net-06` — attempt DNS tunnelling by encoding data in `TXT` queries; pass = DNS restricted to allow-listed resolvers with query patterns bounded.

**Axis 3 — Filesystem isolation**

- `probe-fs-01` — attempt to read `/etc/shadow`; pass = EACCES or ENOENT.
- `probe-fs-02` — attempt to write outside the scratch mount (e.g., to `/`); pass = EROFS.
- `probe-fs-03` — attempt to consume more than the cgroup's disk quota under `dd`; pass = write fails at the quota.
- `probe-fs-04` — write a file inside the overlay, tear down the sandbox, spin up a fresh sandbox, attempt to read the file; pass = ENOENT.
- `probe-fs-05` — attempt to create a SUID binary in the scratch mount and `execve` it; pass = SUID bit ignored (`nosuid` mount).
- `probe-fs-06` — attempt to escape via `/proc/1/root` or `/proc/1/cwd`; pass = access denied or path resolves inside the sandbox.

**Axis 4 — Persistence / cross-session isolation**

- `probe-persist-01` — write a file inside a warm-pool sandbox, release it back to the pool, acquire a *fresh* sandbox from the same pool as a *different* session, attempt to read the file; pass = ENOENT.
- `probe-persist-02` — set an environment variable inside a warm-pool sandbox, release, acquire again as a different session; pass = env var not present.
- `probe-persist-03` — write to a well-known temp path (`/tmp/foo`), release, acquire again; pass = clean state.
- `probe-persist-04` — measure the sandbox teardown wall-clock (release + re-acquire round-trip); report against the EACC section-7 propagation-latency SLA.
- `probe-persist-05` — verify the base image's digest at boot equals the pinned digest in the EACC.

**Axis 5 — Side-effect quarantine**

- `probe-sideeffect-01` — attempt to POST to `stripe.com` directly from inside the sandbox (i.e., bypass the two-phase proposal pattern); pass = egress-proxy 403 or the wrapper detects the missing proposal.
- `probe-sideeffect-02` — verify the wrapper receives a **proposal** rather than an executed action for a tool the EACC classifies side-effecting.
- `probe-sideeffect-03` — mutate the proposal after it is emitted (i.e., in a second call whose payload disagrees with the first); pass = the wrapper rejects on the canonical-form hash mismatch.

### Part B — Run the suite

Wire the probes into a runner that emits a structured report per run:

- Sandbox platform + version.
- Base-image digest.
- Seccomp profile hash.
- Cgroup limits (CPU, memory, disk, wall-clock).
- Egress policy summary.
- Per-probe pass / fail / skipped-because.

Run against your candidate platform. Every FAIL is a finding routed to `ai-infra-security`. Every SKIP with reason "the platform does not support this control" is a finding routed to the platform-choice decision.

### Part C — Populate EACC section 7 (sandbox clauses)

With the measured invariants, fill in your EACC's section-7 sandbox clauses:

- **Sandbox class + version.** Concrete, pinned.
- **Seccomp profile hash.**
- **Cgroup limits.**
- **Egress default and allow-list per tool.**
- **Base-image digest and CVE-patch cadence.** (The cadence is a peer-role commitment; cite it.)
- **Propagation-latency SLA.** Report the measured value from `probe-persist-04`.
- **Two-phase side-effect enforcement.** State whether every side-effecting tool honours the proposal pattern.

## Deliverables

Commit to your exercise-solution area:

- `probes/` — the probe code, one file per axis or one runner + a table.
- `runs/<platform>-<date>.json` — the structured run report(s).
- `EACC-section-7.md` (or a diff against the exercise-01 EACC) — the populated sandbox clauses with the measured values.
- `findings.md` — every FAIL, every SKIP, routed to the appropriate peer role with a specific remediation ask.
- `README.md` naming the platform you probed, the version, and a one-paragraph summary of the findings.

## Acceptance criteria

- **At least 15 probes across the five axes are implemented and executable.**
- **Every probe has a documented pass criterion.** *"Looks fine"* is not a pass criterion.
- **The run report is structured and machine-readable** — JSON or a similarly parseable shape mod-108's monitors could consume.
- **Every FAIL is a finding with a routed remediation.** No FAILs are silently tolerated.
- **Section 7 of the EACC carries measured values, not nominal ones.** The propagation-latency SLA is what your probe measured, not what the vendor doc claims.
- **Fail-closed under partial platform failure.** If the sandbox is unreachable, the wrapper stub the probes use must refuse; document how the probes assert this.
- **Every attempted escape is logged.** The audit log must show the probe attempts as they would if a real tool tried them.

## Stretch goals

- **Cross-platform comparison.** Run the suite against *two* platforms (e.g., gVisor and a hardened container runtime). Publish the diff of measured invariants; propose which is defensible for which class of tool.
- **CVE-response drill.** Pick a known historical sandbox CVE (e.g., a runc / gVisor / Firecracker CVE) and augment your suite with a probe that would have detected an unpatched version. Cite the CVE.
- **Long-running interpreter probe.** For interactive sandboxes (per-session, TTL-bounded), verify the TTL is enforced by probing beyond the expected TTL and asserting the sandbox is torn down.
- **Egress-proxy fuzz.** Fuzz the egress proxy with malformed hostnames, SNI-only, HTTP/2 CONNECT tunnels, WebSocket upgrades, and unusual TLS ciphers. Report any host that reaches through unexpectedly.
- **Adversarial code inside the sandbox.** Package a corpus of published sandbox-escape POCs (with source citation) and run them as probes. Every one that succeeds is a critical finding.

## Guardrails

- **Do not run the adversarial probes on a shared host.** Use a VM you own or a scratch cloud account. Some probes (mount, ptrace, unshare) are noisy or trigger security tooling.
- **Do not publish credentials or internal hostnames in the run report.** Redact.
- **Do not include weaponised sandbox-escape POCs in the exercise commit** — cite the CVE + POC repository and consume them at run-time. This exercise is defensive; the escape POCs stay outside the curriculum repo.
- **Do not target a third-party's hosted sandbox without their authorisation.** Probing a vendor's hosted code-interpreter without permission is an unauthorised security test.
- **Route findings, do not fix.** The platform is the peer role's scope; mod-107's output is the finding, not the patched runc.
