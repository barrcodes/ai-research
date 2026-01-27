---
title: Agents Rule of Two - A Practical Approach to AI Agent Security
source: Meta AI Blog
url: https://ai.meta.com/blog/practical-ai-agent-security/
researched_at: 2026-01-26T00:00:00Z
---

# Agents Rule of Two: A Practical Approach to AI Agent Security

## Overview

Meta introduces the **Agents Rule of Two**, a deterministic security framework that constrains AI agent capabilities to mitigate prompt injection vulnerabilities. Rather than relying on detection or mitigation, this approach architecturally limits which combinations of properties an agent can simultaneously possess, reducing the blast radius of successful prompt injection attacks.

## Key Points

- **Three Constrained Properties**: Agents must NOT simultaneously satisfy all three: (A) process untrustworthy inputs, (B) access sensitive systems/data, (C) autonomously change state or communicate externally
- **Configuration-Driven Security**: Design agents to satisfy maximum two properties; third property is restricted or gated behind human approval
- **Practical Configurations**:
  - *Travel Agent* [AB]: Web data + booking system access, but requires human confirmation before state changes
  - *Research Assistant* [AC]: Web browsing + request execution, but runs in sandboxed environment without sensitive data
  - *Internal Coder* [BC]: Production system access + code changes, but restricts untrustworthy input sources via author-lineage filtering

## Technical Details

**Threat Model**: Attackers embed malicious instructions within untrustworthy data (emails, web content, user input) to hijack agent behavior—exfiltrate sensitive data, send phishing messages, or execute unauthorized transactions.

**Implementation Approach**:
- Identify which two properties your agent legitimately needs
- Architecturally disable or gate the third property
- Layer with human-in-the-loop approval for high-risk operations
- Combine with existing protections (Llama Guard, Prompt Guard, Code Shield) for defense-in-depth

**Scope Limitation**: Framework addresses prompt injection consequences only. Does not prevent hallucinations, agent mistakes, or over-privileging through other vectors.

## Implications

**Architectural Decision Point**: This framework shifts security from detection/response (hard) to constraint-by-design (practical). For teams building multi-capability agents, the Rule of Two forces explicit trade-off decisions early in design—which properties matter most, and what gates/approvals replace the disabled property.

**When to Apply**: Essential for production agents handling untrustworthy external data AND sensitive operations. Less critical for read-only research assistants or fully-controlled internal systems.

**Limitations**: Treat as necessary but insufficient. Combine with principle-of-least-privilege, session isolation, and complementary guardrails. The framework is a supplement to, not substitute for, comprehensive security practices.

## Related

- [Meta's Llama Guard](https://ai.meta.com/research/llama-guard/) - Input/output filtering for LLM safety
- [Prompt Guard](https://huggingface.co/meta/Prompt-Guard) - Prompt injection detection
- [Code Shield](https://ai.meta.com/research/code-shield/) - Code generation safety
