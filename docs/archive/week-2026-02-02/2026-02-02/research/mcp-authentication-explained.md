# MCP Authentication Explained: Bridging the Gap to Claude

| | |
|---|---|
| **Source** | Community research (Reddit r/ClaudeAI + OAuth/MCP specifications) |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1qdpdeh/](https://old.reddit.com/r/ClaudeAI/comments/1qdpdeh/claude_connector_mcp_authentication/) |
| **Researched** | 2026-02-02 |

## Overview

MCP (Model Context Protocol) authentication represents a sophisticated implementation of OAuth 2.1 standards, moving beyond the flexible-but-insecure 2012 baseline (RFC 6749) toward a modern, secure-by-default authorization stack. The system mandates PKCE (RFC 7636), JWT tokens (RFC 7519), and RFC 9728 Protected Resource Metadata discovery—creating a federated, decentralized authentication ecosystem. However, implementation faces real challenges: Claude Code has known bugs preventing proper scope negotiation during authorization, requiring workarounds for production deployments.

## Key Points

- **RFC 9728 is the architectural keystone**: Published April 2025, it enables resource servers (MCP servers) to advertise their authorization requirements via `/.well-known/oauth-protected-resource`, allowing clients to discover and dynamically register without hardcoded configuration.

- **PKCE mandatory for all clients**: Unlike the original OAuth 2.0 where PKCE was a patch for public clients, MCP/OAuth 2.1 mandates S256 challenge-response for all client types to prevent authorization code interception attacks.

- **JWT tokens replace opaque tokens**: Self-contained, cryptographically signed JWTs enable stateless validation at resource servers, eliminating the performance bottleneck of token introspection calls back to authorization servers and enabling true federation.

- **Dynamic Client Registration (RFC 7591) + Authorization Server Metadata (RFC 8414)**: Clients discover authorization server endpoints and capabilities at runtime, enabling true zero-configuration federation without manual credential exchange.

- **Claude Code has production bugs**: As of January 2026, Claude Code fails to send the `scope` parameter during OAuth2 authorization (open bug since July 2025), causing flows to fail when scope restrictions are required.

- **Resource Indicators (RFC 8707) solve "Confused Deputy"**: Tokens include an explicit `aud` (audience) claim restricting them to a specific resource, preventing tokens issued for one server from being misused against another.

## Technical Details

### The MCP Authorization Stack

MCP deliberately rejects OAuth 2.0's excessive flexibility in favor of a hardened, mandatory set of components:

**Authorization Flow**: Authorization Code + PKCE (RFC 7636, RFC 9700)
- Client generates a high-entropy `code_verifier`
- Client hashes it as SHA256(`code_verifier`) and base64url-encodes it → `code_challenge`
- Client sends `code_challenge` and `code_challenge_method=S256` to `/authorize`
- After user consent, authorization server redirects back with `authorization_code`
- Client exchanges `authorization_code` + original `code_verifier` at `/token` endpoint
- Server verifies SHA256(`code_verifier`) matches stored `code_challenge`; only then issues token

**Token Format**: JWT (RFC 7519) with Required Claims
```
Header: { "alg": "RS256", "typ": "JWT", "kid": "key-id" }
Payload: {
  "iss": "https://auth-server.example.com",
  "sub": "user-id",
  "aud": "https://mcp-server.example.com",  // RFC 8707: Resource Indicator
  "exp": 1234567890,
  "iat": 1234567800,
  "scope": "agentREAD agentWRITE"
}
Signature: <RSA signature>
```

**Discovery Flow** (RFC 9728):
1. Client fetches `https://mcp-server.example.com/.well-known/oauth-protected-resource`
2. Response includes `authorization_servers` array with AS URLs
3. Client fetches `https://auth-server.example.com/.well-known/oauth-authorization-server`
4. Response provides `authorization_endpoint`, `token_endpoint`, `scopes_supported`, `response_types_supported`, etc.
5. Client initiates auth flow with discovered endpoints

### Security Architecture

**Three-Layer Defense**:
1. **Mandatory PKCE**: Prevents authorization code interception at the device/browser level
2. **JWT + Audience Restriction**: Prevents token reuse across different resource servers (solves Confused Deputy problem)
3. **Sender-Constrained Tokens** (recommended, not yet mandatory): mTLS or DPoP prevents token theft/replay

**OAuth 2.1 vs. 2.0 Differences**:
- Implicit Flow: Deprecated (RFC 9700) - tokens exposed in URL fragments
- ROPC Flow: Discouraged - requires plaintext password handling, violates delegation principle
- PKCE: Now mandatory for all clients (was optional for confidential clients)
- Bearer Tokens: Must use HTTPS; URL-based token passing forbidden

### Known Implementation Gaps

**Claude Code Bug (#12077, open since July 2025)**:
```
Expected: GET /authorize?...&scope=agentREAD
Actual:   GET /authorize?...  // scope parameter missing entirely

Consequences:
- Resource server cannot enforce scope-based authorization
- Refresh tokens not issued (offline_access scope required)
- Token validation fails if scopes are mandatory
```

**HTTP Authentication Headers Ignored**:
Claude Code ignores `X-API-Key` and `Authorization: Bearer` headers in MCP server configuration, always attempting Dynamic Client Registration instead. This affects both traditional API key auth and bearer token scenarios.

**Scope in Dynamic Client Registration Missing**:
When performing RFC 7591 dynamic registration, Claude Code omits the `scope` parameter from the POST body, preventing authorization servers from understanding what scopes the client is requesting.

### Practical Deployment Patterns

**Pattern 1: Fully Federated (Zero-Config)**
```
1. Configure MCP server URL in Claude Desktop
2. Claude discovers /.well-known/oauth-protected-resource
3. Claude discovers auth server from metadata
4. Claude self-registers via RFC 7591
5. Claude initiates authorization code flow
6. User authenticates and consents
7. Tokens issued and attached to subsequent MCP requests
```

**Pattern 2: Pre-Registered Client (Anthropic Recommends)**
```
1. Manually register Claude Desktop as OAuth client
2. Provide client_id to MCP server config
3. Skip RFC 7591 registration step
4. Proceed to authorization code + PKCE
```

**Workaround for Scope Bug (Current Production Reality)**:
- Implement authorization server that doesn't require scope in initial authorization request
- Include scope validation only at token endpoint
- Or use refresh token flow where scope is re-negotiated
- Track GitHub issue #12077 for upstream fix

### RFC 9728 Well-Known Endpoint Schema

```json
{
  "authorization_servers": [
    "https://auth.example.com"
  ],
  "resource": "https://mcp.example.com"
}
```

Authorization server then serves at `/.well-known/oauth-authorization-server`:
```json
{
  "issuer": "https://auth.example.com",
  "authorization_endpoint": "https://auth.example.com/authorize",
  "token_endpoint": "https://auth.example.com/token",
  "registration_endpoint": "https://auth.example.com/register",
  "scopes_supported": ["agentREAD", "agentWRITE", "offline_access"],
  "response_types_supported": ["code"],
  "grant_types_supported": ["authorization_code", "refresh_token"],
  "code_challenge_methods_supported": ["S256"]
}
```

## Implications

### For MCP Server Developers

1. **Implement RFC 9728 discovery first**: Don't rely on clients having pre-configured endpoints. Publish well-known metadata so clients can self-discover and self-register.

2. **Mandate scope validation early**: Even with Claude Code's current bug, design your authorization server to require scopes at token endpoint. This forces compliance as upstream bugs are fixed.

3. **Use JWT tokens with explicit audience**: Never issue opaque tokens. The performance benefit of avoiding token introspection calls is massive at scale, and audience restriction prevents security incidents.

4. **Plan for RFC 7591 registration bugs**: Implement fallback for pre-registered clients. Don't assume DCR will work reliably until Claude Code fixes its implementation.

5. **Monitor GitHub issues #12077, #7744, #4540**: Claude Code's scope handling is actively broken. Subscribe to these issues—they're critical blockers for scope-based authorization.

### For Claude Code Users (Today)

1. **If you need scope-based access control now**: Use pre-registered OAuth clients (Pattern 2 above). Claude Desktop supports client_id/client_secret configuration; Claude Code (CLI) doesn't yet.

2. **Plan for SSO integration**: RFC 9728 + Dynamic Registration makes federated SSO finally viable for AI agents. Evaluate centralized identity providers (Okta, Auth0, Keycloak).

3. **Don't assume automated OAuth works**: Until the scope bugs are fixed, manually test your authorization flow. The happy path works (redirect → consent → token), but scope validation will fail silently.

4. **Token refresh is fragile**: Offline access and refresh tokens won't be issued until the scope parameter bug is fixed. Design for short-lived tokens in production.

### Architectural Significance

MCP's authentication design solves three decades of OAuth problems:
- **Flexibility without chaos**: OAuth 2.0 was a framework (too many choices); MCP + OAuth 2.1 is a protocol (few choices, all secure)
- **Federation without configuration**: RFC 9728 + RFC 7591 + RFC 8414 enable true zero-touch federation
- **Performance without compromise**: JWT tokens eliminate the synchronous AS query bottleneck that plagued early OAuth deployments

However, the gap between specification and implementation (as evidenced by Claude Code bugs) reveals the maturity challenge: the RFC stack is solid; the client tooling is still stabilizing.

## Sources

- [kane.mx/posts/2025/mcp-authorization-oauth-rfc-deep-dive/](https://kane.mx/posts/2025/mcp-authorization-oauth-rfc-deep-dive/) - Comprehensive technical breakdown of OAuth 2.1 and IETF RFCs powering MCP
- [modelcontextprotocol.io/specification/draft/basic/authorization](https://modelcontextprotocol.io/specification/draft/basic/authorization) - Official MCP Authorization specification
- [dev.to/composiodev/mcp-oauth-21-a-complete-guide-3g91](https://dev.to/composiodev/mcp-oauth-21-a-complete-guide-3g91) - Practical MCP OAuth 2.1 implementation guide
- [aembit.io/blog/mcp-oauth-2-1-pkce-and-the-future-of-ai-authorization/](https://aembit.io/blog/mcp-oauth-2-1-pkce-and-the-future-of-ai-authorization/) - Strategic analysis of MCP's role in AI authorization
- [datatracker.ietf.org/doc/rfc9728/](https://datatracker.ietf.org/doc/rfc9728/) - RFC 9728 OAuth 2.0 Protected Resource Metadata (published April 2025)
- [github.com/anthropics/claude-code/issues/12077](https://github.com/anthropics/claude-code/issues/12077) - Claude Code bug: missing scope parameter in authorization requests
- [github.com/anthropics/claude-code/issues/7744](https://github.com/anthropics/claude-code/issues/7744) - Claude Code bug: scopes_supported ignored during token issuance
- [github.com/anthropics/claude-code/issues/4540](https://github.com/anthropics/claude-code/issues/4540) - Claude Code bug: missing scope in Dynamic Client Registration
- [stytch.com/blog/oauth-for-mcp-explained-with-a-real-world-example/](https://stytch.com/blog/oauth-for-mcp-explained-with-a-real-world-example/) - Real-world OAuth implementation example for MCP
- [auth0.com/blog/an-introduction-to-mcp-and-authorization/](https://auth0.com/blog/an-introduction-to-mcp-and-authorization/) - Auth0's perspective on MCP authentication patterns
