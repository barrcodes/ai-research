# Open-Source Policy Engine for Claude Code: Using --dangerously-skip-permissions with Actual Guardrails

| | |
|---|---|
| **Source** | GitHub / Rulebricks Community |
| **URL** | [github.com/rulebricks/claude-code-guardrails](https://github.com/rulebricks/claude-code-guardrails) |
| **Researched** | 2026-02-17 |

## Overview

The Rulebricks Claude Code Guardrails project represents a pragmatic solution to the fundamental tension in agentic AI tooling: enabling unrestricted execution (`--dangerously-skip-permissions`) for productivity while maintaining enforceable security boundaries through real-time policy evaluation. Rather than choosing between autonomy or safety, this approach implements a cloud-based policy engine that intercepts tool calls at runtime, enabling granular, auditable governance without requiring process restarts or git workflows.

## Key Points

- **Cloud Hook Architecture**: The system uses Claude Code's `PreToolUse` hook to intercept tool calls before execution and route them to a Rulebricks API endpoint for real-time decision evaluation (allow/deny/ask)

- **No Process Restart Governance**: Policy changes apply instantly across teams without restarting Claude Code sessions or deploying new configuration files, addressing a critical friction point in traditional `settings.json` workflows

- **Three Core Policy Templates**: Bash Command Guardrails (shell operations), File Access Policy (read/write/edit permissions), and MCP Tool Governance (MCP server operations) cover the primary risk surface

- **Audit Trail by Design**: Every tool call decision is logged with searchable metadata (tool type, approval decision, user/team), enabling security teams to identify patterns (e.g., "which tool gets blocked most frequently?")

- **Five-Minute Setup**: Automated installation via `./install.sh` with rules published through a web UI (rulebricks.com), eliminating the friction of JSON editing and manual configuration management

- **Conditional Logic at Scale**: Unlike basic pattern matching (`rm:*`), policies support conditional rules such as "allow `rm -rf` only on node_modules, deny elsewhere," enabling nuanced permission models

- **Private Infrastructure Option**: While cloud-hosted by default, can run on private infrastructure with custom logging providers, addressing enterprise air-gap and data residency requirements

## Technical Details

**Architecture**:
```
Claude Code → PreToolUse hook → Rulebricks API → allow / deny / ask decision
```

The hook intercepts all tool calls (bash, file operations, MCP calls) before execution. The guardrail script sends tool context to the Rulebricks cloud API, which evaluates against published decision tables, and returns an immediate decision.

**Configuration**:
```json
{
  "env": {
    "RULEBRICKS_API_KEY": "your-api-key",
    "RULEBRICKS_VERBOSE": "1"
  }
}
```

Rules are versioned through the Rulebricks web platform. Publishing a new rule version immediately applies to all connected Claude Code instances—no synchronization lag, no configuration drift.

**Governance Surface**:
| Template | Control Layer | Use Case |
|----------|---------------|----------|
| Bash Guardrails | Shell command execution | Prevent `rm -rf /`, enforce read-only `ls`, whitelist dangerous tools |
| File Access Policy | File I/O operations | Restrict writes to specific directories, audit sensitive file access |
| MCP Tool Governance | MCP server operations | Control which external tools/APIs can be invoked by Claude Code |

**Data Privacy**:
Rules can be customized to redact sensitive data before it reaches the Rulebricks platform. This is critical for organizations processing PII or proprietary code—the guardrail script sanitizes before transmission.

## Implications

**For Engineering Leads**:
The gap between `--dangerously-skip-permissions` (maximum autonomy, zero visibility) and locked-down defaults (minimum risk, high friction) has been the unsolved problem in agentic tooling. Rulebricks solves it by decoupling policy from execution, making it possible to deploy autonomous AI workflows that are simultaneously transparent and auditable.

**For Security/Compliance Teams**:
Instant policy propagation, searchable audit logs, and decision rollup analytics (e.g., "bash executions denied 47 times this week") provide the operational visibility needed to govern autonomous agents at scale. This shifts the control model from "prevent all tool calls" to "prevent specific dangerous patterns while logging everything."

**For DevOps/Platform Teams**:
The five-minute setup and zero-friction policy updates remove the operational burden that typically accompanies permission management. However, organizations with sensitive workloads should evaluate the private infrastructure option rather than default to cloud evaluation.

**The Broader Pattern**:
This represents a maturing pattern in agentic tooling: instead of asking "should we let AI agents be autonomous or constrained?", the question becomes "how do we architect policy evaluation so autonomy and governance are orthogonal concerns?" The answer is external, versioned, auditable policy engines decoupled from execution.

**Risk Calibration**:
The `--dangerously-skip-permissions` flag is now meaningfully less dangerous if backed by Rulebricks guardrails. However, "dangerous" remains accurate—a misconfigured rule allowing `rm -rf /` would still be catastrophic. This approach trades complexity (policy-as-code) for control, which is appropriate for teams with engineering/security resources but may overburden smaller organizations.

## Sources

- [GitHub - rulebricks/claude-code-guardrails](https://github.com/rulebricks/claude-code-guardrails) - Official repository with setup instructions and templates
- [Show HN: Control Claude permissions using cloud hooks](https://www.pulse.bot/ai/news/show-hn-control-claude-permissions-using-cloud-hooks-5f5f40ed-3ac7-4e1d-ab0c-aa56d0e9dd3c/) - Community discussion on governance architecture
- [Claude Code Changelog (January 2026)](https://www.gradually.ai/en/changelogs/claude-code/) - Timeline of Claude Code capability releases and policy changes
