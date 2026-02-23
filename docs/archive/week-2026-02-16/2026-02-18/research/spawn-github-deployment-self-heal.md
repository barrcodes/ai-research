# Spawn: Deploy and Self-Heal GitHub Repos

| | |
|---|---|
| **Source** | GitHub - runnerelectrode/spawn |
| **URL** | [github.com/runnerelectrode/spawn](https://github.com/runnerelectrode/spawn) |
| **Researched** | 2026-02-18 |

## Overview

Spawn is an AI-driven deployment platform that automates the entire lifecycle from code push to production operation. Claude Opus 4.6 analyzes GitHub repositories to generate deployment configurations, Fly.io provisions infrastructure, and a self-healing agent continuously monitors health and fixes issues without human intervention. The system executes 30-second health checks with graduated recovery strategies—from restarts to intelligent resource scaling to code-level fixes.

## Key Points

- **Full automation cycle**: Repository analysis → Dockerfile generation → deployment → continuous healing. Developers push code; Claude handles configuration decisions (memory allocation, service structure).
- **Intelligent self-healing**: Progressive remediation escalates from machine restarts to Claude-driven diagnostics that distinguish between memory exhaustion, code failures, transient issues, and unknowns—applying targeted fixes accordingly.
- **Proactive scaling**: System monitors memory utilization and auto-scales when crossing 85% threshold, preventing many failure classes before they occur.
- **Webhook-driven architecture**: GitHub webhooks trigger BullMQ job queuing, enabling asynchronous processing and reliable retry mechanisms.

## Technical Details

**Architecture**: TypeScript/Bun runtime, BullMQ for job queue management, Supabase for persistence, Fly.io Machines API for infrastructure control, Redis for queue backing.

**Self-healing flow**: Failed health check → restart machine → if persistent, send crash logs to Claude → Claude diagnoses root cause and applies targeted remediation (scale RAM, patch code, restart, or alert operator).

**Health monitoring**: 30-second interval checks with multi-level escalation. Memory proactively scales at 85% utilization threshold.

**Prerequisites**: Fly.io account, Supabase project, Redis instance, GitHub App (Contents/Metadata permissions), ngrok for local webhook tunneling.

## Implications

**Operational efficiency**: Eliminates toil around Dockerfile authorship, configuration generation, and routine recovery tasks. Meaningful for teams with limited DevOps capacity.

**Trade-offs**: Vendor lock-in (Fly.io, Supabase, Redis required). Complex local development setup. Health remediation quality depends entirely on Claude's ability to diagnose application failures from logs. Not suitable for applications requiring human judgment in failure scenarios.

**When to use**: Greenfield projects, small-to-medium services, teams prioritizing deployment speed over maximum control. Strong fit for typical web apps and services where Claude can reliably infer configuration needs.

**Architectural significance**: Demonstrates agentic pattern at system level—AI not just assisting developers but autonomously managing production operations via feedback loops. Shows practical application of Claude's code analysis capabilities beyond coding assistance.

## Sources

- [runnerelectrode/spawn on GitHub](https://github.com/runnerelectrode/spawn) - Complete source code and setup documentation
