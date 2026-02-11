# Claude.ai Opus 4.6 Prompt Caching Time Limits

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1r1g0fn/](https://old.reddit.com/r/ClaudeAI/comments/1r1g0fn/claudeai_is_using_very_short_prompt_caching_time/) |
| **Researched** | 2026-02-11 |

## Overview

Claude.ai's web interface implements aggressive prompt caching expiration on Opus 4.6, flushing cached context after just 5 minutes of inactivity. This forces expensive context reloads on subsequent messages, rapidly consuming rate-limited token budgets (e.g., 5-hour monthly limits exhausted in days). The short cache window appears intentional backend behavior, not a bug.

## Key Points

- **5-minute cache expiration**: If you step away from a conversation for >5 minutes, prompt context cache is automatically flushed
- **Expensive reloads**: Next message forces context reloading as fresh input tokens, potentially consuming 10-100K tokens for large contexts
- **Affects rate limits disproportionately**: Users report burning through monthly limits 5-10x faster than expected with 4.6
- **Systemic issue**: The 5-minute timeout applies across all models (not 4.6-specific), but feels more pronounced with 4.6
- **Active workaround in use**: Users are sending dummy "test" messages every 3-4 minutes to keep cache alive—much cheaper than context reloads
- **API vs. web app mismatch**: The API supports configurable cache TTLs (5 min to 1 hour), but web app doesn't offer user control

## Technical Details

### Cache Mechanics
- Prompt caching on claude.ai uses HTTP protocol-level caching with 5-minute TTL (Time To Live)
- When cache expires, subsequent requests require reprocessing the full cached prompt as new input tokens
- Token consumption on reload: Full conversation history + system prompts + any attachments must be re-tokenized

### Current Limitations
- **No configuration**: Web app users cannot adjust cache duration (no settings exposed)
- **API asymmetry**: `claude-3-5-opus-20250514` on API supports `cache_control: {"type": "ephemeral"}` with TTL options (5 min to 1 hour), but web app hardcoded to 5 min
- **Silent failures**: Cache expiration is invisible—users discover the issue through surprise token consumption

### Behavioral Observations
From community reports:
- Cache expiration hits hardest in "long research sessions" where users reference 10+ prior messages
- Cost multiplier: Single context reload can consume 10-50% of a 5-hour limit
- Timing: `YertletheeTurtle` notes "frustrating number" of insights reports showing token usage spikes just after 5-minute mark

## Implications

### For Practitioners Using claude.ai Web App

1. **Rate limit strategy changes**: Can't rely on persistent conversation state. Budget aggressively for context reload costs
2. **Session management patterns shift**: Longer think-chains become expensive. Shorter, self-contained prompts more cost-effective
3. **Workaround necessity**: Dummy message pattern (no-op test messages every 3-4 min) becomes standard practice for research/analysis work
4. **Model selection impacts**: Opus 4.6's higher speed doesn't offset the cache penalty—users considering downgrade to 4.5 for sustained sessions

### For API Users
- Good news: No equivalent restriction on API (configurable cache TTL)
- Can implement client-side keepalive logic to reset server-side cache without additional token cost
- Consider `cache_control` with 1-hour TTL for analysis workloads

### For Anthropic Product Team
- **Tension point**: Web app cost control (short cache → more token consumption → higher billing) conflicts with UX (frustrating, unpredictable costs)
- **Gap in parity**: API and web app have incompatible cache behavior
- Low-hanging improvement: Expose cache TTL selector (5 min / 15 min / 1 hour) in web settings

## User Impact Examples

**From comments:**

- `Acceptable-Lynx1169`: "That's why I might be out of weekly tokens 5 days in, 4.6 is sucking me dry even without running swarms"
- `prakersh` (separate thread): "Burning through Opus 4.6 limits 10x faster than 4.5"
- `bishopLucas`: "Set effort to high by default, changed default model back to Sonnet 4.5" (cost mitigation)

## Architectural Context

This appears intentional rather than a bug:

1. YertletheeTurtle's comment "Yeah, it's 5 min on the other models as well" → systematic policy
2. Insights report integration shows Anthropic is aware (tracking cache expiration events)
3. No public acknowledgment of "fixing" this—suggests it's a deliberate rate-limit mechanism

The 5-minute window likely balances:
- **Server cost**: Long-lived cache consumes memory per active session
- **Revenue**: Forcing frequent context reloads increases token billing
- **Competitive positioning**: Discourages heavy free/cheap-tier usage of Opus 4.6

## Sources

- [old.reddit.com/r/ClaudeAI/comments/1r1g0fn/](https://old.reddit.com/r/ClaudeAI/comments/1r1g0fn/claudeai_is_using_very_short_prompt_caching_time/) - Original post by BurdensomeCountV3, 2026-02-10
- YertletheeTurtle comment confirming 5-min limit across all models and insights report visibility
- Community discussion on workarounds and model alternatives
