# Claude Code Token Usage Dashboard

| | |
|---|---|
| **Source** | Claude Code Documentation & Support Center |
| **URL** | [support.claude.com/en/articles/12157520-claude-code-usage-analytics](https://support.claude.com/en/articles/12157520-claude-code-usage-analytics) |
| **Researched** | 2026-02-18 |

## Overview

Claude Code provides built-in usage analytics and token tracking mechanisms for organizations to monitor developer productivity and control costs. The platform offers three complementary approaches: organization-level dashboards with contribution metrics, programmatic access via admin APIs, and in-session token accounting through `/cost` and `/stats` commands. These enable teams to measure code shipping impact, enforce spend limits, and optimize token consumption through context management strategies.

## Key Points

- **Tiered Dashboard Access**: Team/Enterprise plans get organization-level analytics showing lines of code accepted, suggestion acceptance rates, and activity trends; contribution metrics require GitHub integration and track PRs/commits with and without Claude Code assistance
- **Programmatic Usage API**: The `/v1/organizations/usage_report/claude_code` endpoint provides daily aggregated metrics including model breakdown, token consumption (input/output/cache), tool action acceptance rates, and per-user accountability for API-based customers
- **In-Session Cost Tracking**: `/cost` command shows session-level spending and token usage; `/stats` provides usage patterns for subscribers; `/context` visualizes what's consuming context space (MCP servers, codebase, conversation history)
- **Average Cost Profile**: ~$6/day per developer with 90% of users staying below $12/day; API customers see ~$100-200/developer/month on Sonnet 4.6, with significant variance based on concurrent instances and automation use
- **Context Optimization Architecture**: Automatic prompt caching reduces repeated content costs; auto-compaction summarizes conversation history; tool search defers MCP definitions until invoked; hooks/skills move processing out of Claude's context window

## Technical Details

### Dashboard Metrics Architecture

The analytics API returns structured data with clear separation of concerns:

- **Core metrics**: Session counts, commits, lines added/removed, PRs created—directly tied to developer productivity
- **Model breakdown**: Per-model token accounting (cache creation, cache read, input, output) and estimated costs in minor currency units
- **Tool action telemetry**: Acceptance/rejection rates by tool type—signals relevance of suggestions to developer workflow
- **Terminal context**: Tracks environment type for deployment-specific usage patterns

### Cost Control Mechanisms

**Token-per-minute (TPM) rate limits** scale with organization size:
- 1-5 users: 200k-300k TPM per user
- 100-500 users: 15k-20k TPM per user
- 500+ users: 10k-15k TPM per user

Rate limiting applies at organization level, allowing individual burst usage when others are idle—important for training events or deadline-driven sprints.

### Context Management Strategies (Architectural)

1. **Hook-based preprocessing**: PreToolUse hooks intercept tool calls and filter output before Claude sees it (e.g., test runners showing only failures, reducing input from 10k→100s of tokens)
2. **Skill-based knowledge injection**: Avoid repeated codebase exploration by encoding architecture, naming conventions, and key concepts in skills—invoked on-demand
3. **Automatic tool deferral**: When MCP server definitions exceed configurable threshold (default 10%), tool descriptions move out of base context and load on-demand via tool search
4. **Subagent model selection**: Agent teams can specify cheaper models (Haiku) for coordination tasks while reserving Opus for complex reasoning

## Implications

**For engineering leaders:**
- Contribution metrics provide quantifiable business impact measurement—move beyond "lines accepted" to shipping velocity with/without Claude Code
- Workspace spend limits allow cost boundaries without sacrificing productivity; per-user TPM scaling prevents resource hoarding in large teams
- Export capability enables integration with internal dashboards (Datadog, Grafana, custom BI tools)

**For platform architects implementing cost control:**
- API endpoint pagination (limit/page parameters) and cursor-based navigation enable efficient bulk metrics collection and custom aggregation
- Model breakdown gives fine-grained accounting for cost attribution and optimization decisions (when to recommend Sonnet vs. Opus)
- Tool action telemetry surfaces workflow gaps—high rejection rates on refactoring suggestions indicate prompt clarity issues or model limitations for that task class

**For DevOps/SRE teams:**
- Hook architecture enables centralized data filtering without code changes—validate compliance (no secrets in tool output), reduce noise, lower token costs in one place
- LiteLLM integration path for enterprises on Bedrock/Vertex/Foundry (though unaudited for security)—addresses gap in vendor-provided metrics on alternative platforms

**Technical debt consideration:** Organization-level rate limiting (not per-user enforcement) requires shared budgeting discipline and monitoring—teams should track actual vs. allocated consumption to detect runaway usage patterns early.

## Sources

- [Claude Code usage analytics - Support Center](https://support.claude.com/en/articles/12157520-claude-code-usage-analytics) - Dashboard metrics, GitHub contribution tracking setup
- [Get Claude Code Usage Report API](https://platform.claude.com/docs/en/api/admin/usage_report/retrieve_claude_code) - Programmatic access for custom dashboards
- [Manage costs effectively - Claude Code Docs](https://code.claude.com/docs/en/costs) - Rate limits, context optimization, cost reduction strategies
