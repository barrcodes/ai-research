# YouAM: Address, Contact Card, and Encrypted Inbox for AI Agents

| | |
|---|---|
| **Source** | Hacker News (Show HN) |
| **URL** | [news.ycombinator.com/item?id=47140699](https://news.ycombinator.com/item?id=47140699) |
| **Researched** | 2026-02-24 |

## Overview

YouAM solves a foundational infrastructure gap in the emerging AI agent ecosystem: agents lack a standardized way to discover and communicate with each other. The system provides routable identities (addresses), cryptographic credentials (contact cards), and encrypted message delivery (inbox), enabling framework-agnostic agent-to-agent communication comparable to how email functions for humans.

## Key Points

- **Standardized Identity Layer**: Agents receive routable addresses in the format `name::youam.network`, creating a DNS-like namespace for agent discovery across different frameworks (LangGraph, CrewAI, etc.)

- **End-to-End Encryption**: Messages use NaCl Box encryption, ensuring relay servers cannot access plaintext—a critical privacy requirement when agents exchange sensitive information or make autonomous decisions

- **Flexible Deployment**: Supports both self-hosted relays and public network infrastructure, enabling organizations to maintain data sovereignty while benefiting from standardization

- **SDK Support**: Provides Python and TypeScript SDKs with Apache 2.0 licensing, reducing friction for framework integration

## Technical Details

The architecture mirrors email: agents have persistent addresses tied to cryptographic contact cards containing identifying information and encryption credentials. Store-and-forward delivery handles offline recipients, eliminating tight coupling requirements between agents. The NaCl Box primitive (Curve25519 + ChaCha20-Poly1305) provides authenticated encryption without revealing identity to infrastructure operators.

Interoperability across frameworks is the core value—agents built with different orchestration tools can now communicate through a single protocol rather than point-to-point integrations.

## Implications

This addresses a critical agentic architecture bottleneck: as AI systems move from single-agent workflows to multi-agent coordination, identity and messaging become infrastructure problems rather than application concerns. The design trades off immediate centralization (simpler coordination) for decentralization (operator flexibility), positioning it as a base layer rather than a turnkey solution.

Practitioners should evaluate this for: (1) multi-framework agent networks requiring secure coordination, (2) scenarios where agent identity must persist across deployments, (3) use cases needing audit trails of agent interactions. The self-hosted relay option makes this viable for regulated industries. However, for tightly coupled single-system deployments (e.g., internal LangGraph agents), the overhead may not justify adoption.

This represents foundational infrastructure thinking—solving identity and communication before those become technical debt in scaled agent systems.

## Sources

- [Show HN: YouAM – An address, contact card, and encrypted inbox for AI agents](https://news.ycombinator.com/item?id=47140699) - Original announcement on Hacker News
- [Identity Management for Agentic AI](https://openid.net/wp-content/uploads/2025/10/Identity-Management-for-Agentic-AI.pdf) - OpenID Foundation guidance on agentic identity patterns
- [Identity and Access Management for AI Agents](https://curity.io/blog/identity-and-access-management-for-AI-agents/) - Curity's perspective on agent IAM requirements
