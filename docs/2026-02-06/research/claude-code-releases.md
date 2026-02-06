# Claude Code Releases v2.1.32 & v2.1.33

| | |
|---|---|
| **Source** | GitHub - Anthropics |
| **URL** | [github.com/anthropics/claude-code/releases](https://github.com/anthropics/claude-code/releases) |
| **Researched** | 2026-02-06 |

## Overview

Anthropic released two consecutive Claude Code updates (v2.1.32 on Feb 5 and v2.1.33 on Feb 6) bringing foundational multi-agent capabilities, memory persistence, and significant stability improvements. These releases mark the transition from research preview to production-ready agent teams, alongside model updates to Claude Opus 4.6 and improvements to session management, error handling, and VSCode integration.

## Key Points

### v2.1.32 (February 5, 2026)
- **Claude Opus 4.6 availability** - Newest frontier model integrated
- **Agent teams feature** - Multi-agent collaboration for complex workflows (experimental, requires `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`)
- **Automatic memory recording** - Claude captures and recalls context across sessions
- **Partial conversation summarization** - "Summarize from here" added to message selector
- **Skill auto-loading** - Discovers skills in additional directories via `--add-dir`
- **Improved bash tool reliability** - Fixed heredoc parsing issues with JavaScript template literals
- **Skill budget scaling** - Description limits now scale to 2% of context window size
- **VSCode enhancements** - Remote session support for OAuth users, git branch info in session picker

### v2.1.33 (February 6, 2026)
- **Multi-agent workflow hooks** - New `TeammateIdle` and `TaskCompleted` events for orchestration
- **Agent spawning restrictions** - `Task(agent_type)` syntax for controlled sub-agent creation
- **Agent memory persistence** - Frontmatter field support with user/project/local scope options
- **Plugin discoverability** - Plugin names added to skill descriptions and `/skills` menu
- **Extended thinking stability** - Fixed interruptions during model thinking phase
- **API compatibility improvements** - Fixed proxy/streaming endpoint 404 fallback behavior
- **Enhanced error diagnostics** - Connection failures now show specific causes (ECONNREFUSED, SSL errors) instead of generic messages
- **Session picker fixes** - Raw XML markup replaced with clean titles; scroll optimization on load/switch
- **Proxy settings application** - Fixed environment variable configuration for WebFetch and HTTP requests in Node.js build

## Technical Details

### Multi-Agent Architecture
The agent teams feature represents a shift toward compound AI systems where Claude Code can spawn and coordinate multiple specialized agents. The `Task(agent_type)` restriction syntax provides granular control over agent permissions, critical for production deployments where task complexity requires multiple specialists but spawning must remain bounded.

Agent memory with scoped persistence (user/project/local) addresses a fundamental limitation in previous versions: context loss across sessions. Project-scoped memory enables persistent state within a workspace; user-scoped enables cross-project recall; local keeps isolated state—this tiering provides flexibility for different use cases.

### Hook System for Orchestration
`TeammateIdle` and `TaskCompleted` events enable reactive multi-agent workflows. Combined with restrictions on agent spawning, this creates a controlled coordination model where:
1. Parent agent spawns specific sub-agents via `Task(agent_type)`
2. Sub-agents perform isolated work, signaling completion
3. Parent reacts to events (idle/completion) to trigger next steps

This prevents unbounded agent proliferation and provides implicit waits for multi-stage tasks.

### Stability & Reliability Improvements
The extended thinking interruption fix addresses a critical issue: Claude's reasoning phase (available in recent models) was being cut short when new messages arrived. The API error with whitespace+thinking blocks suggests the model was generating edge-case outputs that violated JSON schema validation—now normalized before transmission.

Proxy compatibility fix (404 on streaming endpoints) indicates support for enterprise proxy configurations that intercept all HTTP traffic. Previous fallback to non-streaming was problematic for streaming-first clients.

Connection error diagnostics (ECONNREFUSED, SSL errors) are architecturally significant: they enable users to quickly identify root causes (port mismatch, cert issues) rather than retrying against opacity.

### Performance & UX Refinements
Skill character budget scaling (2% of context) dynamically adapts to model capabilities—larger context windows can list more skills without truncation, reducing friction in discovery.

Bash tool heredoc fix prevents silent failures in infrastructure-as-code workflows where deployment scripts commonly use heredocs with template literals.

## Implications

### For Practitioners
1. **Agent team adoption now viable** - Experimental flag suggests production timeline is near; teams should evaluate multi-agent decomposition patterns now rather than waiting for GA
2. **Memory persistence changes debugging workflows** - Cross-session memory recall reduces need for explicit context re-entry; impacts prompt structure and session design
3. **Proxy enterprise deployments** - Fixes enable secure corporate deployment in proxy-intercepted networks; IT teams should test with your proxy stack
4. **Error diagnosis overhead reduced** - Specific connection errors cut troubleshooting time; improves reliability in CI/CD contexts

### For Architecture Decisions
1. **Agent spawning bounds** - Designing for `Task(agent_type)` upfront prevents refactor pain; consider agent registry and type safety
2. **Memory scope strategy** - Project vs. user vs. local scope choice impacts data governance and multi-tenant safety; establish policy early
3. **Extended thinking workflows** - Model reasoning is now interruptible-safe; long-running analysis can safely accept user interruption without losing coherence
4. **Proxy-aware clients** - Enterprise deployments must account for proxy behavior; streaming fallback removal means clients must handle streaming-only endpoints

### Model Availability Impact
Claude Opus 4.6 availability in Claude Code immediately raises the baseline capability for code-gen tasks. For teams currently bound to older models via API limits, this update provides a clear path to frontier capabilities for local development workflows.

## Sources
- [github.com/anthropics/claude-code/releases/tag/v2.1.33](https://github.com/anthropics/claude-code/releases/tag/v2.1.33) - v2.1.33 release notes (multi-agent hooks, memory, error handling)
- [github.com/anthropics/claude-code/releases/tag/v2.1.32](https://github.com/anthropics/claude-code/releases/tag/v2.1.32) - v2.1.32 release notes (agent teams, Opus 4.6, memory recording)
