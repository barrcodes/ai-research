# LAD-A2A: Local Agent Discovery for AI Agents

| | |
|---|---|
| **Source** | Reddit r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qpmxvk/p_lada2a_how_ai_agents_find_each_other_on_local](https://old.reddit.com/r/MachineLearning/comments/1qpmxvk/p_lada2a_how_ai_agents_find_each_other_on_local/) |
| **Researched** | 2026-01-29 |

## Overview

LAD-A2A (Local Agent Discovery for A2A) is an open protocol specification that solves a critical gap in the AI agent ecosystem: how agents discover and bootstrap trust with other agents on local networks. While Google's A2A protocol defines agent-to-agent communication and MCP defines tool integration, neither addresses the fundamental discovery problem. LAD-A2A fills this gap with a deliberately minimal specification that enables agents to find each other when joining networks—hotels, offices, hospitals, cruise ships—without requiring prior knowledge or central configuration.

## Key Technical Approach

### The Discovery Layer Gap

The protocol ecosystem has three distinct layers:
- **LAD-A2A**: Discovery & trust bootstrap (the missing layer)
- **A2A**: Agent-to-agent communication (defined by Google 2025)
- **MCP**: Agent-to-tools/data integration

LAD-A2A is intentionally designed as just the "first handshake" that answers "who's here?" so that A2A can answer "what can you do?"

### Discovery Mechanisms (Ordered Fallback)

LAD-A2A mandates a three-tier discovery strategy with graceful degradation:

1. **mDNS/DNS-SD (Primary for Consumer Networks)**
   - Service type: `_a2a._tcp.local`
   - Required TXT records: `path` (AgentCard location), `v` (LAD-A2A version)
   - Optional: `org` (organization), `id` (agent DID)
   - Zero-config like AirDrop; requires no manual setup on consumer Wi-Fi

2. **Well-Known HTTP Endpoint (Universal Fallback)**
   - Standard path: `/.well-known/lad/agents`
   - Returns JSON discovery response with agent metadata
   - CORS-enabled for browser-based agent clients
   - Includes `capabilities_preview` field for lightweight UI display
   - Response format includes network realm, agent URLs, and capability summaries

3. **DHCP Option + Captive Portal (Enterprise/Restricted)**
   - DHCP option provides discovery URL for controlled networks
   - Fallback to network's captive portal domain for discovery
   - Bridges gap between consumer Wi-Fi and enterprise segmentation

### Trust Bootstrap (Defense in Depth)

The specification assumes local networks are hostile by default and mandates multiple verification layers:

**Transport Layer:**
- All endpoints MUST use TLS 1.2+ (self-signed certs NOT acceptable for production)
- Client-side certificate verification required

**Identity Verification (One of Three Required):**
1. JWS Signature: AgentCard wrapped in signed envelope with cryptographic proof
2. DID Binding: AgentCard references Decentralized Identifier with resolvable verification keys
3. Domain Verification: Agent served from domain matching claimed organization (did:web-like model)

**User Consent:**
- Explicit user consent required before initiating first contact
- Consent UI presents: agent name, verified organization, capabilities preview
- Least-privilege enforcement based on AgentCard declarations

**Authentication for Privileged Actions:**
- OAuth 2.0 or OIDC required for operations beyond read-only public info
- Auth requirements declared in AgentCard

### Protocol Design Philosophy

The specification is deliberately **minimal and composable**:
- Does NOT extend or redefine A2A AgentCard format; only standardizes discovery
- AgentCard endpoint always points to standard A2A paths (`.well-known/agent.json` or `.well-known/agent-card.json`)
- Hands off to A2A for actual communication after discovery completes
- Includes reference implementations in Python for immediate adoption

## Implementation Details

### Discovery Response Format

```json
{
  "version": "1.0",
  "network": {
    "ssid": "HotelNetwork",
    "realm": "example.com"
  },
  "agents": [
    {
      "name": "Hotel Concierge",
      "description": "Hotel services",
      "role": "hotel-concierge",
      "agent_card_url": "https://concierge.example.com/.well-known/agent.json",
      "capabilities_preview": ["property-info", "amenities", "housekeeping"]
    }
  ]
}
```

The lightweight `capabilities_preview` enables discovery UI without fetching full AgentCards upfront.

### End-to-End Flow

```
1. Client joins network
2. mDNS query for _a2a._tcp.local (or fallback to well-known endpoint)
3. Receive provider advertisement with AgentCard URL
4. Fetch and cryptographically verify AgentCard identity
5. User consent confirmation (display verified agent, capabilities)
6. Initiate A2A session with verified provider
```

## Threat Model & Security Decisions

The specification treats local networks as inherently adversarial:
- Rogue access points
- mDNS spoofing
- Captive portal injection
- MITM attacks

This threat assumption drives all security requirements: mandatory TLS, identity verification, and explicit user consent before any agent contact. The design acknowledges that discovery is "the easy part" and focuses on the hard problem: **trust and intent verification** across untrusted networks.

## Architectural Implications

### For Multi-Agent Systems

1. **No Central Registry Required**: Agents self-advertise on local networks; no cloud dependency for discovery on local LANs

2. **Composition with Existing Standards**: Deliberately positions as a shim layer that doesn't reinvent A2A or MCP, enabling rapid adoption without ecosystem coordination

3. **Enterprise vs. Consumer Graceful Degradation**: DHCP option for controlled networks; mDNS/well-known endpoints for open networks. Single implementation handles both deployment models

4. **Capability Advertisement Decoupling**: Preview capabilities in discovery response separate from authoritative AgentCard capabilities. Enables lightweight discovery UI without performance penalty

### Design Trade-offs Embedded in Spec

**Accepted Limitations** (per Reddit discussion feedback):
- mDNS breaks on segmented Wi-Fi, captive portals, enterprise setups → mitigated by HTTP fallback but not eliminated
- mDNS spoofing risk → compensated by cryptographic identity verification in AgentCard
- User consent introduces UX friction → necessary for security; spec doesn't try to eliminate

**The Spec Intentionally Stops at Discovery**: Authentication, authorization, policy enforcement, and safe capability scoping are explicitly pushed to A2A layer. This maintains separation of concerns but means practitioners must implement auth/policy at the A2A level.

## Practical Use Cases

The specification is designed for real-world scenarios where agents need to adapt to local context:

| Environment | Scenario |
|---|---|
| Hotels | Guest's AI assistant discovers hotel concierge agent for spa booking, breakfast times, late checkout |
| Hospitals | Patient's agent discovers navigation/wayfinding agent for radiology directions |
| Offices | Employee's agent discovers IT helpdesk and facilities booking agents on campus network |
| Cruise Ships | Passenger agent discovers ship services agent for shows, dining, excursions |
| Stadiums | Attendee agent discovers venue wayfinding and services agent |
| Smart Cities | Visitor agent discovers public transportation and local service agents |

## Implications for Practitioners

### Immediate Adoption Points

1. **For Agent Framework Developers**: LAD-A2A is specification-only (v0.1.0-draft); reference Python implementation provided. Low implementation burden if A2A support already exists.

2. **For Network Operators**: Well-known endpoint is minimal HTTP serving; mDNS is optional but recommended. Enterprise deployments get DHCP option path.

3. **For Security Teams**: This is a *trust bootstrap* mechanism, not a complete security solution. Full authentication/authorization must be implemented in A2A layer and AgentCard declarations. TLS, identity verification, and user consent are minimums.

### Integration Challenges

1. **Captive Portal Problem Not Solved**: mDNS fails on many hotel/public networks; HTTP fallback requires network access that may be blocked until auth completes. Spec acknowledges but doesn't fully resolve this chicken-egg problem.

2. **User Consent UX Burden**: Discovery is fast; consent UI becomes the bottleneck. High volume of discovered agents creates decision fatigue. Practitioners will need intelligent filtering strategies.

3. **Enterprise Segmentation**: DHCP option helps but doesn't address zero-trust networks. Organizations using network segmentation must provision discovery separately per segment.

4. **Capability Preview Staleness**: Lightweight capability preview could diverge from actual AgentCard capabilities if not kept in sync. Practitioners need discipline around AgentCard lifecycle management.

### Risk Considerations

- **Rogue Agent Injection**: If mDNS or well-known endpoint is spoofed, unsuspecting users could connect to malicious agents. Mitigation depends on TLS CA chain and user consent UI quality.
- **Lateral Movement Attack Surface**: Discovered agents become potential pivot points in network. Requires proper isolation and capability scoping at A2A level.
- **Privacy Disclosure**: Discovery responses reveal what agents are available on network; this information itself may be sensitive in some environments.

## Related Work & Context

LAD-A2A sits in the emerging ecosystem of AI agent coordination protocols:

- **A2A (Agent-to-Agent)**: Google's 2025 protocol for agent communication; defines task format and JSON-RPC 2.0 messaging
- **MCP (Model Context Protocol)**: Anthropic's protocol for agent-to-tool/data integration
- **Security Analysis**: Unit42 PaloAlto Networks documented "Agent Session Smuggling" attacks in A2A systems, highlighting why trust bootstrap (LAD-A2A's domain) is critical

The spec emphasizes it's not trying to solve authentication/authorization—those are A2A concerns. LAD-A2A is purely "who's here?" → A2A is "what can you do?"

## Sources

- [LAD-A2A Official Specification](https://lad-a2a.org/spec/spec/)
- [LAD-A2A Protocol Overview](https://lad-a2a.org/)
- [Reddit r/MachineLearning Discussion](https://old.reddit.com/r/MachineLearning/comments/1qpmxvk/p_lada2a_how_ai_agents_find_each_other_on_local/)
- [Reference Implementation (GitHub)](https://github.com/franzvill/lad)
