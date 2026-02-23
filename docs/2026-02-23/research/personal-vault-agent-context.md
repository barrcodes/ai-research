# Personal Vault: Own Your Personal Context, Let AI Agents Query It

| | |
|---|---|
| **Source** | GitHub - lovincyrus/personal-vault |
| **URL** | [github.com/lovincyrus/personal-vault](https://github.com/lovincyrus/personal-vault) |
| **Researched** | 2026-02-23 |

## Overview

Personal Vault is a reference implementation of the Personal Context Protocol—an encrypted local storage system that lets users maintain a single source of truth for personal data (identity, documents, preferences, financial info) and grant scoped access to AI agents via HTTP API, CLI, or MCP server integration. It solves the repetitive disclosure problem where users must re-provide context to every new agent without continuity or control.

## Key Points

- **Encrypted-by-default architecture**: Per-field AES-256-GCM encryption with Argon2id key derivation (64MB, 3 iterations); profile passwords never stored
- **MCP server integration**: Agents like Claude access the vault through standardized tools, enabling seamless context queries within agentic workflows
- **Scoped access control**: Agents request specific fields; users explicitly approve/deny; sensitivity tiers (public, standard, sensitive, critical) classify data access risk
- **Service tokens**: Long-lived, time-bounded credentials allow third-party applications to authenticate without storing plaintext secrets
- **Audit trail**: Complete access logging tracks which agents accessed what and when—critical for compliance and user trust
- **Local-only operation**: Runs at localhost:7200; no data leaves user's device without explicit approval

## Technical Details

**Encryption Flow**: Profile Password + 128-bit Secret Key → Argon2id KDF → 256-bit Vault Key (in-memory only)

**Storage**: SQLite backend manages encrypted field storage, scoped tokens, and immutable audit logs. Pure Go implementation with no external C dependencies minimizes supply chain risk.

**Agent Integration Methods**:
- HTTP API with bearer token authentication
- CLI for direct user interaction
- MCP server (TypeScript) for Claude/compatible agents

**Demo**: Shopping scenario with two MCP servers—vault provides personal context, mock shop processes orders autonomously—illustrates realistic multi-agent coordination.

## Implications

**For practitioners building agentic systems**:
- Solves the context management bottleneck: agents no longer require users to re-authenticate or repeatedly supply identity/preferences
- MCP integration pattern is architecturally significant—enables agents to treat personal context as a queryable resource rather than a parameter
- Scoped token design prevents privilege escalation; agents hold minimal credentials
- Audit logging becomes a USP for consumer-facing agents competing on privacy/transparency

**Trade-offs and considerations**:
- Requires user to run local server (operational friction vs. cloud convenience)
- Sensitivity tiers require thoughtful schema design upfront
- Scaling beyond single-device usage (multi-device sync, backup recovery) not addressed in reference implementation

**When to adopt**: Critical for applications where user data privacy is a differentiator, agentic workflows require persistent identity context, or regulatory compliance demands audit trails. Less critical for ephemeral, single-use agent interactions.

**Architectural pattern value**: Demonstrates how to decouple agent authentication from data access—agents get scoped tokens, not master credentials. This pattern is portable to other sensitive contexts (financial APIs, healthcare records, enterprise knowledge bases).

## Sources

- [GitHub: lovincyrus/personal-vault](https://github.com/lovincyrus/personal-vault) - Full source code, architecture documentation, and MCP integration examples
