# Show HN: AI agents play SimCity through a REST API

| | |
|---|---|
| **Source** | Hallucinating Splines / Hacker News |
| **URL** | [hallucinatingsplines.com](https://hallucinatingsplines.com) |
| **Researched** | 2026-02-11 |

## Overview

Hallucinating Splines is a headless city simulation platform that deploys AI agents as autonomous mayors managing virtual cities. Built on Micropolis (the open-source SimCity engine), the platform exposes REST API and MCP endpoints, allowing Claude or any LLM to directly control city development in seconds. Cities run in Cloudflare Durable Objects and are publicly browsable with real-time stats.

## Key Points

- **Dual integration paths**: REST API for general access; MCP (Model Context Protocol) server for direct Claude/Cursor integration enabling rapid agent deployment
- **Stateful simulation in serverless**: Each city runs as a single Durable Object instance, handling Micropolis engine state without per-request initialization overhead
- **Public-first design**: All cities are browsable, ranked by population/score, with live metrics (33 mayors, 433 cities, 8.1M population at launch)
- **LLM spatial weakness exposed**: Agents struggle with spatial reasoning—scattering buildings randomly, failing to chain infrastructure (roads, power lines). Creator notes this mirrors "dealing with a toddler"

## Technical Details

**Deployment architecture:**
- Micropolis engine (GPL v3, original SimCity code) compiled and deployed to Cloudflare
- One Durable Object per city for persistent state management
- Frontend polls API every 15 seconds with staggered updates for perceived responsiveness

**Agent interaction model:**
- Agents receive city state (population, budget, year, tile grid) via REST endpoints
- Agents issue commands (zone residential/commercial/industrial, build infrastructure, adjust tax rates)
- Creator seeded agents with historical SimCity FAQs/guides as instructions—better than zero context

**Zero-friction onboarding:**
- No signup required for API key
- MCP integration allows agents to control cities with minimal configuration
- Agents typically "building in seconds" once pointed at the endpoint

## Implications

**For agentic systems practitioners:**
- Demonstrates that structured, turn-based environments (simulators, games) can work well as agent playgrounds, especially with REST/MCP bindings
- Shows trade-off between agent autonomy and environmental complexity—LLMs handle high-level decisions but fail at spatial/temporal reasoning
- MCP emerges as practical binding layer for tool-use, replacing ad-hoc API wrappers

**For tool design:**
- Spatial domains expose blind spots in current LLM reasoning; consider explicit state representation or constraint-based action spaces
- Real-time UI feedback (leaderboards, stats) creates engagement loop that drives continued agent experimentation

**When to use this pattern:**
- Stateful simulators exposed via serverless (Durable Objects, Lambda) work for read-heavy public systems
- MCP binding is more efficient than prompt injection for repeated tool calls
- Historical domain knowledge (FAQs, guides) bootstraps better agent behavior than generic instructions

## Sources

- [Hallucinating Splines](https://hallucinatingsplines.com/) - Live platform with city browser and API
- [Show HN: AI agents play SimCity through a REST API](https://news.ycombinator.com/item?id=46946593) - HN discussion with creator commentary on architecture and LLM limitations
- [GitHub - smearle/gym-city](https://github.com/smearle/gym-city) - Micropolis OpenAI gym environment (reference implementation)
