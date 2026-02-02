# Heimdall – Open Source Observability Platform for MCP servers and apps

| | |
|---|---|
| **Source** | GitHub (hmdl-inc/heimdall) |
| **URL** | [github.com/hmdl-inc/heimdall](https://github.com/hmdl-inc/heimdall) |
| **Researched** | 2026-01-29 |

## Overview

Heimdall is a self-hosted observability platform purpose-built for Model Context Protocol (MCP) servers and AI applications. It implements OpenTelemetry standards to provide real-time tracing, metrics, and dashboarding without vendor lock-in—critical for teams that need debugging visibility into agentic systems without external telemetry pipelines.

## Key Points

- **OpenTelemetry-native**: Uses OTLP/HTTP as the standard ingestion protocol (port 4318), making it compatible with existing observability instrumentation
- **Minimal integration overhead**: SDK decorators for Python and JavaScript/TypeScript require single-line annotations—no scaffold code
- **Self-hosted architecture**: Backend processes traces locally, frontend dashboard runs on port 5173—suitable for privacy-sensitive deployments
- **MCP-specific visibility**: Captures tool call execution, resource access patterns, and prompt processing traces at the protocol level
- **Three core observables**: Real-time tracing (execution flows), dashboard analytics (latency/errors/usage), and integrated debugging

## Technical Details

**Integration Pattern:**
```python
@heimdall.trace
def tool_handler(resource_id, action):
    # Automatic parameter extraction via introspection
    pass
```

**Architecture Flow:**
- MCP servers emit OTLP telemetry → Heimdall backend processes and stores → React frontend queries via API → visualization

**SDK Differences:**
- Python SDK auto-extracts parameter names from function signatures
- JavaScript/TypeScript requires explicit parameter mapping
- Both support batch flushing and configurable intervals to minimize overhead

**Deployment**: Single-instance model suitable for teams; active development suggests planned scaling features.

## Implications

**When to Use:**
- Production MCP environments requiring observability without SaaS dependencies
- Debugging agentic tool chains where execution traces are critical to understanding failures
- Organizations with data residency requirements preventing cloud observability platforms

**Trade-offs:**
- Self-hosted increases operational burden vs. managed services
- Active development status means feature velocity but potential breaking changes
- Parameter introspection (Python) is convenient but masks intent; JavaScript's explicit mapping is more maintainable at scale

**Considerations:**
- Evaluate against Datadog/New Relic/Honeycomb if managed service reliability is a priority
- OpenTelemetry compatibility enables migration pathways to other backends
- Dashboard feature set (planned LLM evaluation, user-level analytics) suggests this targets both infrastructure and application-level insights

## Related

- [OpenTelemetry Protocol Specification](https://opentelemetry.io/docs/specs/otel/protocol/) – Standard Heimdall uses for trace ingestion
- [MCP Server Specification](https://modelcontextprotocol.io/) – Protocol context for observability instrumentation
- [Model Context Protocol GitHub](https://github.com/anthropics/mcp) – Core MCP implementation reference
