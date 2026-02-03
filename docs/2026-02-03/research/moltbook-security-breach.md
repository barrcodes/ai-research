# AI Social Network Moltbook Exposed 6000 Users

| | |
|---|---|
| **Source** | Reuters / Wiz Blog |
| **URL** | [reuters.com/legal/litigation/moltbook-social-media-site-ai-agents-had-big-security-hole](https://www.reuters.com/legal/litigation/moltbook-social-media-site-ai-agents-had-big-security-hole-cyber-firm-wiz-says-2026-02-02/) |
| **Researched** | 2026-02-03 |

## Overview

Moltbook, a Reddit-like social network for AI agents, exposed 6,000+ user email addresses, 1.5 million API authentication tokens, and private agent messages through a single critical architectural failure: a Supabase database lacking Row Level Security (RLS) policies with its public API key hardcoded in client-side JavaScript. The platform was entirely built via "vibe coding"—AI-assisted development with minimal human oversight—exposing how automation can eliminate fundamental security architecture decisions.

## Key Points

- **Vulnerability**: Missing RLS policies on exposed Supabase public API key allowed unauthenticated full read/write access to production database
- **Exposed data**: 1.5M API keys, 35K email addresses, private agent messages, and authentication credentials
- **Root cause**: No human code review, security architecture decisions delegated entirely to AI during platform construction
- **Detection**: Wiz researchers discovered hardcoded credentials in production JavaScript bundle within minutes of exploration
- **Response timeline**: Vulnerability reported Jan 31, 2026; fully patched by Feb 1, 2026
- **Scale revelation**: 1.5M registered agents backed by only 17K human accounts (88:1 ratio)

## Technical Details

**Architectural Failure**: Supabase is designed for safe public key exposure when RLS policies restrict database access. Moltbook bypassed this protection entirely—policies were never implemented.

**Exposure Vector**:
```
Hardcoded API key discovered in:
https://www.moltbook.com/_next/static/chunks/18e24eafc444b2b9.js
```

**Access Control Gaps**:
- No authentication enforcement on REST API endpoints
- Write access uncontrolled (PATCH requests modified platform content)
- All tables exposed (agents, owners, messages)
- Unauthenticated users could edit live posts across the platform

**Development Model Risk**: "Vibe coding" eliminated standard security gates: no code review, no permission-based access control design, no authentication architecture validation, no data protection framework specification.

## Implications

**For AI Agent Architecture**: Platforms designed to give agency to AI systems require *stronger* not weaker human security governance. Delegating architecture decisions to code-generation tools introduces systematic blind spots in threat modeling.

**For Infrastructure Design**: Exposing API keys is recoverable only with proper access control layers. The mistake wasn't exposing the key—it was assuming infrastructure defaults provided that layer without explicit configuration.

**For Development Practices**: "No-code" and AI-assisted development eliminate security review stages that catch foundational architecture errors. A single human architect doing threat modeling would have caught this in the design phase before implementation.

**Practical Guidance**:
- RLS policies should be mandatory infrastructure-as-code requirements, not optional features
- API key exposure audits must verify access control policies exist, not just key rotation
- AI platforms require explicit human-led security architecture review separate from implementation

This breach is not about AI agents being dangerous—it's about infrastructure defaults assuming human oversight that never occurred.

## Sources

- [Wiz Blog: Exposed Moltbook Database Reveals Millions of API Keys](https://www.wiz.io/blog/exposed-moltbook-database-reveals-millions-of-api-keys) - Technical analysis of RLS configuration failure
- [Engadget: Moltbook Exposed Due to Vibe-Coded Security Flaw](https://www.engadget.com/ai/moltbook-the-ai-social-network-exposed-human-credentials-due-to-vibe-coded-security-flaw-230324567.html) - Analysis of "vibe coding" development model risks
- [Fortune: AI Leaders Warn Moltbook is a Disaster Waiting to Happen](https://fortune.com/2026/02/02/moltbook-security-agents-singularity-disaster-gary-marcus-andrej-karpathy/) - Industry response and warnings
- [AI CERTs: Moltbook Data Leak Cybersecurity Lessons](https://www.aicerts.ai/news/moltbook-data-leak-exposes-6000-users-cybersecurity-lessons/) - Breach analysis and lessons
