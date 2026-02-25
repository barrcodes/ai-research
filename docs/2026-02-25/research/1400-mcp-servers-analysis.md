# I Probed 1400 MCP Servers

| | |
|---|---|
| **Source** | Bloomberry |
| **URL** | [bloomberry.com/blog/we-analyzed-1400-mcp-servers-heres-what-we-learned](https://bloomberry.com/blog/we-analyzed-1400-mcp-servers-heres-what-we-learned/) |
| **Researched** | 2026-02-25 |

## Overview

A large-scale analysis of 1,412 MCP servers reveals the ecosystem growing 232% in six months but facing critical security gaps. Most servers operate without authentication, minimal rate limiting, and generic tool abstractions—suggesting MCP adoption as first-machine-readable interface rather than API wrapper, fundamentally shifting how organizations expose functionality to AI agents.

## Key Points

- **232% growth in 6 months** (Aug 2025–Feb 2026) with accelerating monthly adoption, yet still under 1% penetration vs. traditional APIs
- **38.7% of servers lack authentication**, exposing sensitive operations (fund transfers, candidate data, payments) without credentials
- **50% of MCP-deploying companies have no public API**, indicating MCP serves as primary programmatic interface, not a secondary wrapper
- Median server exposes only 5 tools (52% read, 25% write, 23% unclassified), dominated by generic utilities (`search`, `fetch`, `ping`)
- **Vercel, Railway, Render collectively 9% of MCP deployments** vs. 2.4% for traditional APIs—speed-first infrastructure choices drive early adoption

## Technical Details

**Security audit findings:**
- 22.9% unrestricted CORS
- Only 2.4% implement rate limiting
- Authentication missing in 38.7% of deployments

**Infrastructure preferences diverge from traditional APIs:**
- AWS: 60% (MCP) vs. implied dominance in traditional APIs
- Azure: 7% (MCP) vs. 19% (APIs)—enterprise adoption lagging
- Vercel/Railway/Render: 9% collective (MCP) vs. 2.4% (APIs)

**Tool distribution patterns:**
- Median 5 tools per server
- Generic utilities dominate (search, fetch, ping)
- Suggests ecosystem still in capability exploration phase

## Implications

**For practitioners:**

1. **Security-first deployment non-negotiable**: If exposing MCP servers, authentication and rate limiting are table-stakes despite ecosystem defaults. The 38.7% gap indicates companies shipping for speed; production deployments must harden immediately.

2. **MCP as primary interface, not wrapper**: Since 50% lack public APIs, design MCP servers with native semantics—don't force-fit REST patterns. This is how non-technical teams will access functionality.

3. **Architectural divergence ahead**: Generic tools indicate teams still figuring out ideal tool granularity. Start with coarse-grained operations that correspond to actual business workflows, not fine-grained CRUD mappings.

4. **Infrastructure choices**: If building MCP servers, Vercel/Railway/Render adoption suggests serverless/edge appeal over traditional cloud. Evaluate cold-start and latency requirements against agent use cases (async acceptable vs. real-time).

5. **Enterprise adoption delayed**: Azure's low MCP penetration vs. APIs suggests larger organizations waiting for governance/compliance tooling. Early movers have breathing room but standardization on security/observability will follow quickly.

## Sources

- [Bloomberry MCP Server Analysis](https://bloomberry.com/blog/we-analyzed-1400-mcp-servers-heres-what-we-learned/) - Large-scale dataset analysis of ecosystem patterns, security, and infrastructure choices
