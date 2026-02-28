# Building Secure, Scalable Agent Sandbox Infrastructure

| | |
|---|---|
| **Source** | Browser Use Blog |
| **URL** | [browser-use.com/posts/two-ways-to-sandbox-agents](https://browser-use.com/posts/two-ways-to-sandbox-agents) |
| **Researched** | 2026-02-27 |

## Overview

Browser Use presents two architectural patterns for securing code-executing agents, with a preference for full agent isolation using Unikraft micro-VMs. This approach makes agents "disposable" by running them in stateless sandboxes with zero access to credentials, eliminating the attack surface for secret exfiltration and simplifying the threat model compared to tool-level isolation.

## Key Points

- **Two Sandboxing Patterns**: Tool isolation (dangerous operations run in separate sandbox) versus agent isolation (entire agent runs sandboxed, communicates through control plane). Browser Use adopted agent isolation for stronger security boundaries and simpler deployment topology.

- **Unikraft Micro-VMs as Foundation**: Each agent gets its own lightweight VM booting in under one second with scale-to-zero idle suspension. Provisioned via Unikraft Cloud REST API on AWS bare metal, enabling elastic scaling without container orchestration overhead.

- **Minimal Secret Exposure**: Sandboxes receive exactly three environment variables (`SESSION_TOKEN`, `CONTROL_PLANE_URL`, `SESSION_ID`) with no AWS credentials, database passwords, or API tokens. Environment variables are deleted from `os.environ` after initial reading.

- **Hardening Through Privilege Dropping**: Bytecode-only execution (source `.py` files deleted post-compilation), `setuid`/`setgid` privilege dropping to unprivileged user, and stateless control plane validation prevent sandbox escape and secret theft.

- **Development-Production Parity**: Same container image runs locally via Docker and production via Unikraft through configuration switching, enabling local testing, parallel evaluation runs, and seamless production deployment.

## Technical Details

### Architecture Layers

The stateless FastAPI control plane proxies all external communication:

| Component | Responsibility |
|-----------|---|
| Sandbox (Unikraft VM) | Executes agent logic, manages single session |
| Control Plane (FastAPI) | Reconstructs conversation history, validates Bearer tokens, proxies LLM calls |
| External Services | Database, S3 (via presigned URLs), LLM APIs |

### Security Mechanisms

**Execution Model**: Python bytecode compiled during Docker build, original source deleted. Entrypoint runs as root to read `.pyc` files, then immediately drops to unprivileged `sandbox` user via `setuid`/`setgid`.

**Credential Handling**: Presigned S3 URLs grant scoped, temporary access. Sandboxes never touch AWS credentials directly. Session validation via Bearer tokens on every control plane request.

**LLM Integration**: Sandbox sends only new messages; control plane reconstructs full conversation history from database, preventing agents from accessing other sessions' data.

### Scaling Trade-offs

Each operation incurs an extra network hop (sandbox → control plane → external service). The architecture trades this latency overhead for decoupled scaling of three independent services. Latency remains negligible relative to LLM response times (typically 1-10 seconds).

## Implications

**Operational Simplification**: Disposable agents with no persistent state dramatically reduce operational complexity. No cleanup, no state leaks, no secrets rotation required per agent instance.

**Threat Model Clarity**: By design, agents have "nothing worth stealing and nothing worth preserving." This eliminates entire classes of attacks and simplifies security audits.

**Development Efficiency**: Same codebase runs locally and in production. Teams can reproduce production behavior locally, run parallel evaluation sweeps without infrastructure investment, then deploy identically to Unikraft.

**Scalability Limitations**: Per-agent micro-VMs cost more than containerized solutions. Organizations must evaluate whether the security and operational benefits justify the per-instance overhead versus running more agents in container clusters.

**When to Adopt Agent Isolation**: Essential when agents interact with untrusted content (web scraping, user-provided prompts) or execute user code. Tool isolation suffices for controlled agents with fixed operation sets and limited external exposure.

## Sources

- [How We Built Secure, Scalable Agent Sandbox Infrastructure](https://browser-use.com/posts/two-ways-to-sandbox-agents) - Core architecture and implementation details
- [Browser Sandbox: Secure Environments for Agents to Interact with the Web](https://www.firecrawl.dev/blog/introducing-browser-sandbox) - Containerized sandbox alternative
- [A thousand ways to sandbox an agent](https://michaellivs.com/blog/sandbox-comparison-2026/) - Comparative analysis of sandboxing approaches
- [AI Agents Are Bringing Back Browser Insecurity](https://www.darkreading.com/application-security/ai-agents-undermine-progress-browser-security) - Security threat context for agent-browser interaction
