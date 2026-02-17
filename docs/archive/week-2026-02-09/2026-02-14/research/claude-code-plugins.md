# Claude Code Plugins Guide

| | |
|---|---|
| **Source** | Anthropic Official Documentation & GitHub Repository |
| **URL** | [github.com/anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) |
| **Researched** | 2026-02-14 |

## Overview

Claude Code plugins provide a standardized extensibility framework for distributed, composable capabilities across development environments. The official repository contains 28+ curated plugins—both internal Anthropic-maintained tools and third-party integrations—organized into skills, agents, hooks, and MCP servers. Plugins enable architectural patterns for team-wide capability sharing with versioning, namespacing, and marketplace distribution.

## Key Points

- **Plugin Architecture**: Plugins decouple functionality through a manifest-based structure (`plugin.json`) with four composition layers: skills (model-invoked capabilities), commands (slash commands), agents (custom subagents), and hooks (event-driven automation).

- **Namespacing & Conflict Prevention**: Skills are namespaced (e.g., `/plugin-name:skill-name`) preventing collisions when multiple plugins define similar functions. Standalone `.claude/` configuration uses direct names (`/skill-name`) for single-project use.

- **Official Plugin Categories**:
  - **Language Servers (LSP)**: TypeScript, Python, Rust, Go, Java (jdtls), C# (csharp-lsp), Kotlin, Swift, Lua, PHP
  - **Code Intelligence**: clangd (C/C++), pyright (Python)
  - **Development Utilities**: code-review, code-simplifier, feature-dev, commit-commands, hookify, plugin-dev, agent-sdk-dev
  - **Output Styling**: explanatory-output-style, learning-output-style
  - **Infrastructure**: claude-code-setup, claude-md-management, pr-review-toolkit, security-guidance, frontend-design, ralph-loop (REPL environment), playground (artifact runner)

- **Distribution Model**: Internal plugins (Anthropic-maintained) live in `/plugins/`, third-party plugins in `/external_plugins/`. Installation via `/plugin install {name}@claude-plugin-directory` or discovery UI. Submission requires security review and quality standards.

- **Plugin Development Lifecycle**: Migrate from standalone `.claude/` configuration to plugins for team sharing. Plugins support versioned releases (semantic versioning), local testing with `--plugin-dir` flags, and multiple simultaneous plugin loading.

## Technical Details

**Plugin Structure**:
```
plugin-name/
├── .claude-plugin/
│   └── plugin.json          # Metadata: name, description, version, author
├── commands/                 # Slash command skills
├── skills/                   # Agent skills with SKILL.md frontmatter
├── agents/                   # Custom agent definitions
├── hooks/                    # Event handlers (hooks.json)
├── .mcp.json                # Model Context Protocol server config
├── .lsp.json                # Language server protocol config
└── README.md                # Documentation
```

**Skill Definition Pattern**:
- Markdown files with YAML frontmatter (description, disable-model-invocation flag)
- `$ARGUMENTS` placeholder captures user input
- Progressive disclosure pattern for complex skill prompts
- Model-invoked vs. command-invoked distinction

**Hook Integration**:
- Declarative event handlers in `hooks/hooks.json`
- PostToolUse, PreToolUse matchers for tool-specific automation
- Integrates shell commands and external processors (e.g., linters via `npm run lint:fix`)

**MCP Server Integration**:
- Plugins expose external APIs and services via `.mcp.json`
- Standard configuration for connecting third-party tools
- Example: Context7 (live docs lookup), official GitHub MCP server (repo management, PR workflows)

## Implications

**For Architects & Leads**:

1. **Capability Modularity**: Plugins establish a clear separation between project-local configuration (`.claude/`) and distributable, versioned capabilities. This enables composable agent ecosystems and reduces configuration sprawl.

2. **Team Standardization**: Namespaced skills prevent naming conflicts when consolidating tools from multiple sources. Organizations can enforce a plugin marketplace policy with curated, security-reviewed extensions rather than ad-hoc scripting.

3. **Language Server Strategy**: The 10+ built-in LSP plugins provide code intelligence across major languages. This positions Claude Code as language-agnostic for IDE-like code analysis, allowing selective LSP deployments without heavy dependency management.

4. **Agentic Patterns**: The skill → agent → plugin composition mirrors microservice decomposition. Teams can publish domain-specific agent plugins (code-review, security-guidance, feature-dev) that other agents consume, enabling hierarchical agent systems at scale.

5. **Hooks as Glue**: Event-driven hooks allow plugins to automate CI/CD integration (lint-on-write, commit-on-save) without modifying core Claude Code. This reduces the need for custom wrapper scripts and enables uniform automation policies.

6. **Migration Path**: Organizations using Claude Code can incrementally convert `.claude/` local skills to versioned plugins without disruption, enabling change control and progressive rollout to teams.

7. **Third-Party Ecosystem**: The external_plugins directory and submission form signal Anthropic's commitment to a verified partner ecosystem. Quality gates reduce risk of supply-chain vulnerabilities compared to unvetted package registries.

## Sources

- [Anthropic GitHub - claude-plugins-official](https://github.com/anthropics/claude-plugins-official) - Official, Anthropic-managed directory of high quality Claude Code Plugins
- [Claude Code Docs - Create plugins](https://code.claude.com/docs/en/plugins) - Complete plugin authoring, distribution, and architecture guide
