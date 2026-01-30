# ChatGPT Containers: Bash and Package Installation Capabilities

| | |
|---|---|
| **Source** | Simon Willison |
| **URL** | [simonwillison.net/2026/Jan/26/chatgpt-containers/](https://simonwillison.net/2026/Jan/26/chatgpt-containers/) |
| **Researched** | 2026-01-26 |

## Overview

ChatGPT has expanded its container sandbox to support direct bash execution alongside multi-language compilation (Python, Node.js, Ruby, Go, Java, C/C++, etc.) and dynamic package installation via pip and npm. This represents a significant shift toward agentic code execution with environmental proxy constraints designed to prevent data exfiltration while enabling legitimate package dependencies.

## Key Points

- **Multi-language execution**: Bash plus 10+ compiled and interpreted languages now execute directly in containerized sessions
- **Managed package installation**: pip/npm work through internal proxy gateways (`applied-caas-gateway1.internal.api.openai.org`) configured via environment variables, bypassing direct outbound network access
- **File download support**: New `container.download` tool fetches and materializes URLs into the container filesystem
- **Network isolation**: All package manager traffic routes through monitored internal gateways; no direct outbound network from container
- **URL-based injection prevention**: Mirroring Claude's approach, the system blocks URL downloads unless previously viewed in conversation, preventing prompt-injection attacks that could exfiltrate via file requests

## Technical Details

**Package Manager Configuration:**
```
PIP_INDEX_URL → Internal PyPI proxy
UV_INDEX_URL → Internal uv proxy
NPM_CONFIG_REGISTRY → Internal npm registry
```

Environment variables suggest planned support for Go, Maven, Gradle, Cargo, and Docker registries, indicating a tiered rollout strategy.

**Execution Model:**
The container maintains a per-session filesystem isolated from other conversations. Package installations persist within the session but are ephemeral across chat boundaries—critical for preventing shared-dependency attacks.

**Security Posture:**
Sandbox prevents URL-based exfiltration by requiring prior conversation context. No direct network access from container means malicious code cannot bypass OpenAI's proxy inspection layer. However, bash execution fundamentally expands the attack surface compared to code-only evaluation.

## Implications

**For Practitioners:**
- **Agent complexity**: Agentic systems can now handle multi-step workflows requiring system-level operations (dependency management, file manipulation, process composition)
- **Dependency management**: Internal proxy strategy means you cannot install arbitrary PyPI/npm packages; curated registries limit supply-chain attack exposure but also flexibility
- **Session-scoped resources**: Ephemeral filesystems per chat prevent cross-session contamination but require agents to materialize all dependencies fresh per session
- **Attack surface**: Bash access dramatically increases what a compromised or misaligned agent can do. Security relies on OpenAI's proxy filtering and conversation context validation—"I think this is all safe, though I'm curious if it could hold firm against a more aggressive round of attacks" (Willison)

**Architectural Decisions:**
- Use internal gateways over direct internet access trades speed for security
- Per-session isolation trades persistence for compartmentalization
- Environment variable configuration allows rapid feature addition (Go, Docker registries noted) without container rebuilds

## Related

- Simon Willison's analysis of Claude's container execution (referenced in original article)—similar sandbox architecture with agentic safety considerations
- OpenAI's approach to URL blocking mirrors Anthropic's prompt-injection mitigations in multi-tool systems
