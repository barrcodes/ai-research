# Template for Secure AI Multi-Agent Coding Workflows

| | |
|---|---|
| **Source** | AndrewAltimit/template-repo |
| **URL** | [github.com/AndrewAltimit/template-repo](https://github.com/AndrewAltimit/template-repo) |
| **Researched** | 2026-02-06 |

## Overview

This repository provides a reference architecture for orchestrating autonomous AI agents across shared codebases with explicit human supervision at each stage. The design decouples trust from capability—agents perform end-to-end development tasks (claims issues, writes code, opens PRs) but require administrator approval to proceed, ensuring auditability without operational friction. The architecture incorporates Anthropic's Sleeper Agents research for detecting hidden model misalignment.

## Key Points

- **Board-driven task orchestration**: GitHub Projects v2 enforces state transitions with strict approval comment formatting (`[Approved][Agent]`) to eliminate false positives and maintain human authorization checkpoints
- **Multi-provider agent abstraction**: Supports Claude, Gemini, OpenCode, and Crush interchangeably, preventing vendor lock-in and allowing provider selection based on API access
- **Container-first CI/CD**: All tools run in Docker on self-hosted GitHub Actions runners, eliminating environmental drift and cloud costs while maintaining offline-first operation
- **Eighteen modular MCP servers**: Segregate concerns (code quality, content, graphics, synthesis) to isolate failures and simplify debugging
- **Mechanistic interpretability integration**: Embeds Sleeper Agents framework to detect backdoors and deceptive behavior, addressing research-validated alignment risks

## Technical Details

**Trust Architecture**: Multi-layer verification gates prevent unauthorized agent execution:
- Pattern validation enforces strict syntax (`[Action][Agent]`)
- Admin-only configuration in `.agents.yaml` under `security.agent_admins`
- Timestamped claim comments prevent concurrent work conflicts
- Iteration limits restrict revision loops
- `git-guard` and `gh-validator` wrappers require elevated privileges for sensitive operations and mask secrets

**Infrastructure**: Self-hosted runners eliminate cloud dependencies; containerized tooling ensures reproducibility across development environments.

**Safety Requirement**: Developers must complete Blue Dot Impact AI safety training before use—non-negotiable prerequisite rather than optional onboarding.

## Implications

**For practitioners building autonomous agent systems**: This architecture demonstrates how to grant agents genuine autonomy (end-to-end task execution) while maintaining human observability. The key trade-off is operational overhead—approval gates add latency but make every agent decision reviewable and reversible. This pattern scales better for safety-critical work than pure automation.

**On multi-agent provider selection**: The template's abstraction layer validates the practical reality that LLM providers are interchangeable for coding tasks. This matters for teams managing agent fleets across multiple API contracts or hedging against provider changes.

**On safety research integration**: The inclusion of Sleeper Agents framework signals growing acceptance that alignment concerns aren't theoretical for production agents. For teams deploying agents with code execution capability, mechanistic interpretability testing becomes an operational requirement, not an academic exercise.

**When not to use this**: If you need fast agent iteration cycles or rapid prototyping, human approval gates will be bottlenecks. This template optimizes for auditability and safety over throughput.

## Sources

- [AndrewAltimit/template-repo](https://github.com/AndrewAltimit/template-repo) - Reference architecture for multi-agent orchestration with human-supervised autonomy
- [Anthropic Sleeper Agents Research](https://www.anthropic.com) - Detection framework for hidden backdoors in LLMs
- [Blue Dot Impact](https://bluedotimpact.org) - AI safety training coursework referenced as prerequisite
