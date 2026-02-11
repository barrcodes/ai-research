# Ex-GitHub CEO Launches a New Developer Platform for AI Agents

| | |
|---|---|
| **Source** | TechCrunch, OpenSource For You, and related coverage |
| **URL** | [entire.io/blog/hello-entire-world](https://entire.io/blog/hello-entire-world/) |
| **Researched** | 2026-02-11 |

## Overview

Entire, founded by ex-GitHub CEO Thomas Dohmke, addresses a critical architectural gap: managing fleets of AI coding agents within traditional human-centered development workflows. The platform unifies AI-generated code through a Git-compatible database layered with semantic reasoning, solving the problem of code velocity outpacing human comprehension.

## Key Points

- **Massive funding validation**: $60M seed round at $300M valuation—largest seed for a dev tool startup, led by Felicis with backing from M12 (Microsoft Ventures), Madrona, and prominent angels (Jerry Yang, Garry Tan, Olivier Pomel)

- **Three-layer architecture**: Git-compatible database (source control), universal semantic reasoning layer (agent coordination), and AI-native interface (multi-agent + GitHub/GitLab integration)

- **Open-source foundation**: Checkpoints, an open-source CLI tool that captures code provenance—original prompts, agent reasoning, implementation logic. Currently supports Anthropic Claude Code and Google Gemini CLI

- **Traceability as product**: Core differentiator is explainability: teams understand not just what changed but *why*—critical for compliance and human oversight before production deployment

## Technical Details

**Checkpoints CLI Architecture**
Captures and records the complete reasoning chain behind AI-generated code:
- Original prompts/instructions that generated code
- Agent decision steps and alternatives considered
- Implementation context and assumptions

**Multi-Agent Coordination**
The semantic reasoning layer enables orchestration across multiple coding agents, positioned as a workflow layer between generative models and version control systems.

**Workflow Integration**
Designed to slot between GitHub/GitLab and AI models, creating an audit trail that satisfies security/compliance teams demanding human review gates before merge.

## Implications

**For architects:**
- This signals demand for "agentic middleware"—platforms that manage agent fleets rather than individual agents
- The Git-compatible database choice suggests treating AI agents as collaborative contributors rather than code generation APIs
- Traceability addresses a genuine pain point: teams need governance layers that don't bottleneck velocity

**Trade-offs:**
- Checkpoints adds CLI overhead to agentic workflows; adoption depends on standardization across major LLM platforms
- Open-source foundation may limit monetization; watch for enterprise features (advanced analytics, compliance integrations)
- Semantic reasoning layer complexity may struggle with heterogeneous agent architectures

**When to care:**
- Organizations running multiple AI agents on same codebases
- Teams with strict compliance/audit requirements (financial services, healthcare)
- Companies viewing agentic coding as operational infrastructure, not experiments

**Alternative approaches:**
- Custom wrapper solutions around existing Git workflows
- LLM-native audit logging (Claude, GPT-4 integration)
- Third-party code review platforms extended for AI agents

## Sources

- [TechCrunch: Former GitHub CEO raises record $60M dev tool seed round](https://techcrunch.com/2026/02/10/former-github-ceo-raises-record-60m-dev-tool-seed-round-at-300m-valuation/)
- [OpenSource For You: AI Code Transparency Platform Entire Unveiled](https://www.opensourceforu.com/2026/02/ai-code-transparency-platform-entire-unveiled-by-ex-github-ceo-with-60m-seed/)
- [StartupNews: GitHub's former CEO launches a developer platform for agentic coding](https://startupnews.fyi/2026/02/11/githubs-former-ceo-launches-a-developer-platform-for-the-age-of-agentic-coding/)
- [WinBuzzer: Ex-GitHub CEO Launches Platform For AI Agent Transparency](https://winbuzzer.com/2026/02/10/ex-github-ceo-launches-ai-platform-record-60m-seed-xcxwbn/)
