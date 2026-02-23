# BVisor - An Embedded Bash Sandbox, 2ms Boot, Written in Zig

| | |
|---|---|
| **Source** | GitHub (butter-dot-dev/bVisor) |
| **URL** | [github.com/butter-dot-dev/bVisor](https://github.com/butter-dot-dev/bVisor) |
| **Researched** | 2026-02-23 |

## Overview

BVisor is a userspace bash sandbox runtime designed for embedding in applications—particularly AI agent systems—that need secure, ephemeral command execution without VM/container overhead. It achieves ~2ms sandbox initialization by leveraging Linux Seccomp user notifier to intercept syscalls at the process level rather than relying on kernel namespaces or containerization. The approach is "imageless," allowing direct filesystem access with copy-on-write isolation for writes.

## Key Points

- **Syscall interception architecture**: Instead of containerization, BVisor runs child processes directly on hardware and intercepts ~400 Linux syscalls, categorizing them into virtualized (~70), passthrough (~40), blocked (~50), with ~240 not yet implemented.

- **Imageless design**: Eliminates container image overhead. Host tools (npm, git, compilers) work immediately because the filesystem is visible; isolation is achieved through CoW overlays that copy files to sandbox-local directories on write access.

- **Embedded execution model**: Ships as an SDK (TypeScript, with Python/CLI planned) rather than standalone infrastructure—no network, no remote service, no cloud dependency. Sandbox initialization spans milliseconds, not seconds.

- **Performance vs. isolation trade-off**: Native hardware execution means minimal startup penalty but reduced isolation compared to kernel-level namespaces. Suitable for ephemeral, untrusted workloads but explicitly marked "early proof-of-concept."

## Technical Details

**Syscall Handling Strategy:**
- Virtualized syscalls (filesystem, process control, signals) are emulated by the virtual kernel
- Passthrough syscalls (mmap, brk, trivial operations) forward directly to the OS
- Blocked syscalls prevent privilege escalation, capability manipulation, and ptrace

**Copy-on-Write Isolation:**
When a process opens a file with write flags, BVisor copies it to `sandbox-local/` directory. Read-only operations pass through to the real filesystem. This minimizes filesystem overhead while preventing sandbox pollution.

**Implementation:** Written in Zig. Supports Linux on ARM and x86 architectures.

## Implications

**For AI agents and ephemeral tool execution:** BVisor addresses a critical operational gap. Existing approaches (remote Python sandboxes, gVisor, containers) introduce 100ms–1s+ startup latency. For agents executing dozens of short-lived tool calls per interaction, 2ms vs. 500ms matters architecturally.

**Trade-offs to consider:**
- **Isolation strength**: Syscall filtering is narrower than kernel namespaces. Exploit surface exists for determined attackers but suits untrusted but not adversarial workloads.
- **Maturity**: Early PoC status means missing syscalls, potential edge cases, Linux-only (no macOS/Windows support planned mentioned).
- **Applicability**: Best for embedding in applications (agents, REPL tools, CI steps) rather than multi-tenant infrastructure.

**When to use:** Short-lived command execution in trusted execution environments (your infrastructure, not client browsers). When startup latency is a first-class constraint. As a component in agentic safety architecture, not as complete isolation.

**Alternatives:** gVisor (stronger isolation, slower), containers/VM (slower but more mature), restricted subprocess APIs (weaker isolation).

## Sources

- [butter-dot-dev/bVisor on GitHub](https://github.com/butter-dot-dev/bVisor) - Primary source for architecture and design decisions
