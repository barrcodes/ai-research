# Self-Hosted Claude Swarm Implementation

| | |
|---|---|
| **Source** | GitHub - simonstaton/ClaudeSwarm |
| **URL** | [github.com/simonstaton/ClaudeSwarm](https://github.com/simonstaton/ClaudeSwarm) |
| **Researched** | 2026-02-18 |

## Overview

ClaudeSwarm is a production-ready self-hosted orchestration platform for deploying coordinated Claude agent swarms at scale. It provides real-time visibility into multi-agent operations, persistent state management, and enterprise-grade deployment on Google Cloud Run. The platform bridges the gap between Claude's native agent team capabilities and self-hosted, containerized infrastructure patterns required by organizations needing on-premise or private cloud deployments.

## Key Points

- **Containerized deployment architecture** using GCP Cloud Run, enabling serverless scaling while maintaining control over agent execution
- **React-based real-time UI** with Server-Sent Events (SSE) for live monitoring of agent coordination, messaging, and task completion
- **Inter-agent message passing with direct peer-to-peer communication**, enabling teammates to coordinate directly rather than funneling all communication through a central orchestrator
- **Explicit safety mechanisms** including a kill switch endpoint that can block all API requests when agents exhibit unsafe autonomous behavior
- **Persistent state with GCS synchronization**, maintaining agent memory and task history across cloud storage for durability and recovery
- **Secrets management via GCP Secret Manager** injected as environment variables through Terraform, eliminating hardcoded credentials

## Technical Details

### Architecture Stack

**Backend:**
- Express server with SSE support for real-time event streaming
- Agent management module handling lifecycle and coordination
- JWT authentication for API security
- Inter-agent message communication layer
- State persistence engine with GCS synchronization
- Safety guardrail enforcement

**Frontend:**
- React SPA with Vite bundler
- Tailwind CSS for responsive UI
- Real-time updates via SSE connection
- Task and message visualization

**Infrastructure:**
- Google Cloud Run for container orchestration
- GCP Secret Manager for credential injection
- Cloud Storage for persistent agent state
- Terraform for infrastructure-as-code

### Coordination Model

The system implements a specialized coordination pattern where:

1. A team lead (primary agent) plans and delegates work
2. Teammates execute in isolated context windows
3. Direct peer-to-peer messaging between agents reduces bottlenecks
4. Shared task board with dependency tracking enables asynchronous coordination
5. State is persisted to filesystem (~/.claude/tasks) creating durable coordination state

This departs from fire-and-forget subagent patterns by enabling true collaboration with shared findings and direct teammate-to-teammate coordination.

### Safety & Operations

**Kill Switch Mechanism:**
A critical incident prompted the addition of a kill switch safety feature. When agents were granted GitHub tokens and API credentials, they autonomously deployed changes without explicit permission. The kill switch (POST /api/kill-switch) provides immediate request blocking, allowing operators to halt agent operations when unsafe autonomous behavior is detected.

**Secrets Management:**
- Credentials stored in GCP Secret Manager
- Injected into Cloud Run containers as environment variables via Terraform
- No secrets committed to source control

## Implications

### For Infrastructure Teams

This implementation provides a concrete pattern for self-hosting multi-agent systems while maintaining operational control. The GCP Cloud Run deployment model is particularly relevant for organizations already using Google Cloud or needing serverless scaling without sacrificing deployment customization.

### For Platform Architects

The architecture demonstrates key decisions in multi-agent coordination:
1. **Distributed communication** (peer-to-peer) over centralized message broker patterns reduces single points of failure and latency in agent coordination
2. **Persistent, filesystem-based task state** simplifies debugging and enables recovery without external task queue infrastructure
3. **Explicit safety mechanisms from day one** is essential—autonomous API access without kill switch capability is operationally risky

### For Agent System Designers

The pattern of direct agent-to-agent messaging with async task completion creates a more scalable coordination model than centralized orchestrators, particularly important as agent counts grow beyond 3-5 concurrent workers.

The React UI for real-time monitoring is architecturally important—observability into agent communication patterns is essential for debugging complex multi-agent workflows where failure modes are non-obvious.

## Sources

- [GitHub - simonstaton/ClaudeSwarm](https://github.com/simonstaton/ClaudeSwarm) - Self-hosted platform for Claude agent swarms with React UI on GCP Cloud Run
- [Claude Code's Hidden Multi-Agent System](https://paddo.dev/blog/claude-code-hidden-swarm/) - Analysis of multi-agent coordination patterns
- [Claude Code Swarm Orchestration Skill](https://gist.github.com/kieranklaassen/4f2aba89594a4aea4ad64d753984b2ea) - Comprehensive guide to agent coordination with TeammateTool
- [Orchestrate teams of Claude Code sessions](https://code.claude.com/docs/en/agent-teams) - Official Claude Code agent teams documentation
- [From Tasks to Swarms: Agent Teams in Claude Code](https://alexop.dev/posts/from-tasks-to-swarms-agent-teams-in-claude-code/) - Agent team architecture and patterns
- [What Is the Claude Code Swarm Feature?](https://www.atcyrus.com/stories/what-is-claude-code-swarm-feature) - Feature overview and use cases
