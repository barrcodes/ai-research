# Anthropic Official Policy Update: Subscription Auth Ban

| | |
|---|---|
| **Source** | Anthropic Code Documentation |
| **URL** | [code.claude.com/docs/en/legal-and-compliance](https://code.claude.com/docs/en/legal-and-compliance) |
| **Researched** | 2026-02-19 |

## Overview

Anthropic explicitly prohibits third-party developers from routing Claude API requests through consumer plan OAuth tokens (Free, Pro, Max). The policy enforces a hard boundary between consumer authentication (OAuth) and developer infrastructure (API keys), with enforcement mechanisms and no prior notice requirement for violations.

## Key Points

- **OAuth tokens are exclusive to Claude Code and Claude.ai** - OAuth tokens obtained through consumer plans cannot be used in any other product, tool, or service. This constitutes a violation of Consumer Terms of Service.

- **Third-party developers must use API keys, not subscription auth** - Developers building products or services must authenticate through Claude Console or cloud provider integrations (AWS Bedrock, Google Vertex), not by routing consumer credentials on behalf of users.

- **No "subscription login" shortcuts for third-party apps** - Anthropic explicitly prohibits third-party developers from offering "Claude.ai login" functionality or accepting user credentials from Free/Pro/Max plans as a workaround for authentication.

- **Enforcement is unilateral and preemptive** - Anthropic reserves the right to take enforcement measures without prior notice, giving them ability to detect and disable violating integrations immediately.

## Technical Details

| Authentication Method | Permitted Use | Consumer Terms Apply |
|---|---|---|
| OAuth (Claude Free/Pro/Max) | Claude Code, Claude.ai only | Yes |
| API Keys (Claude Console) | Third-party apps, Agent SDK, production services | No |
| AWS Bedrock/Vertex Auth | Enterprise integrations via cloud providers | Depends on commercial agreement |

BAA compliance automatically extends to Claude Code for customers with existing Business Associate Agreements and Zero Data Retention enabled.

## Implications

**For integration builders:** Routing user credentials through consumer plans is explicitly forbidden and undetectable until enforcement. Architecture must use dedicated API keys from Claude Console or cloud provider SDKs. This eliminates "free tier bypass" patterns common in other platforms.

**For security:** The policy creates enforcement leverage—Anthropic can detect and disable third-party OAuth token usage without customer notification, making this a contractual firewall rather than a technical one.

**For product strategy:** This prevents the "subscription as infrastructure" pattern where consumer plans fund production workloads. It forces clear separation between individual usage and automated/commercial use cases.

## Sources

- [Anthropic Commercial Terms](https://www.anthropic.com/legal/commercial-terms) - licensing for Team, Enterprise, Claude API
- [Consumer Terms of Service](https://www.anthropic.com/legal/consumer-terms) - applicable to Free, Pro, Max users
- [Anthropic Usage Policy](https://www.anthropic.com/legal/aup) - general usage restrictions
- [Claude Console](https://platform.claude.com/) - official API key management for developers
