# ContextCache: Persistent KV Cache with Content-Hash Addressing

| | |
|---|---|
| **Source** | Reddit r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1rglj2n/](https://old.reddit.com/r/MachineLearning/comments/1rglj2n/r_contextcache_persistent_kv_cache_with/) |
| **Researched** | 2026-02-27 |

## Overview

ContextCache is a persistent KV cache system designed for tool-augmented LLM deployments that eliminates redundant prefill computation for static tool schema tokens. By indexing cached KV states with content hashes and restoring them on cache hits, the system achieves 29x TTFT (time-to-first-token) speedup at 50 tools on Qwen3-8B with zero quality degradation. The key architectural insight is that tools must be cached as a group rather than individually because they require cross-attention dependencies during prefill.

## Key Points

- **Persistent content-hash addressed cache**: KV states are indexed by SHA-256 hash of sorted schema texts, enabling cache reuse across sessions and users with identical tool sets
- **Group caching requirement**: Per-tool independent caching catastrophically fails (tool selection accuracy drops from 85% to 10%) because models rely on cross-tool and tool-to-system attention during prefill; group caching (system + all tools as single block) preserves full quality exactly
- **Massive TTFT improvement**: Cached TTFT remains constant ~200ms from 5 to 50 tools while full prefill grows from 466ms to 5,625ms (29x speedup at 50 tools)
- **Quality preservation**: Zero quality degradation with group_cached matching full_prefill on tool selection accuracy (TSA), partial function match (PF1), and exact match (EM) across all evaluation splits
- **Disk persistence and serving**: Production-ready FastAPI serving layer with browser-based UI, disk persistence across server restarts for cache recovery
- **Practical scalability constraint**: Eager attention causes OOM at 75+ tools on 24GB GPU; Flash attention integration would extend range

## Technical Details

### Core Mechanism

ContextCache operates on a straightforward principle: tool schemas are prepended to every tool-calling request but rarely change between calls. The system caches the complete KV states produced during initial prefill of all tools combined, then on subsequent requests with the same tool set, restores cached KV states and runs forward pass only on the user query suffix (skipping 99% of prompt tokens).

### Content-Hash Addressing Strategy

Cache keys are generated using SHA-256 of sorted schema texts. This approach:
- Enables deterministic cache hits across different session orderings
- Supports multi-user deployments where 1000s of users may share identical tool sets
- Works transparently without requiring modifications to user queries or explicit cache management

### Critical Insight: Group vs Per-Tool Caching

The paper reveals a non-obvious architectural requirement: models develop strong cross-attention patterns during tool schema prefill:

1. **Per-tool caching failure**: Caching each tool independently achieves only 10% tool selection accuracy (vs 85% baseline), an 8.5x degradation
2. **Root cause**: During prefill, the model attends across tool definitions to discriminate between similar options, and to the system prompt for context
3. **Group caching solution**: Caching all tools as a single block preserves complete prefill quality—group_cached exactly matches full_prefill on TSA, PF1, and EM across held-out and unseen tool splits

This suggests that tool schema processing isn't simply independent token-sequence compression but involves substantive cross-tool reasoning in attention heads.

### Performance Characteristics (Qwen3-8B, 4-bit NF4)

**TTFT (Time-to-First-Token):**
- 5 tools: cached ~200ms vs full ~466ms (2.3x)
- 50 tools: cached ~200ms vs full ~5,625ms (29x)
- Token skip rate: 99% of prompt tokens on cache hit

**Quality Metrics:**
- TSA (Tool Selection Accuracy): 85% across all conditions
- PF1 (Partial Function Match): Consistent across splits
- EM (Exact Match): Preserved with group caching

**Practical Limitations:**
- GPU memory: 75+ tools cause OOM with eager attention on 24GB VRAM
- Flash attention integration could extend practical tool count significantly

### Disk Persistence and Deployment

The system includes:
- Production FastAPI serving layer with integrated cache management
- Browser-based UI for inspection and control
- Disk-backed cache with recovery on server restart
- Two modes: demo (pre-recorded responses) and live (real GPU inference)

## Implications

### Agentic System Performance

For large-scale tool-calling systems (e.g., 1000s of concurrent users with shared tool sets), ContextCache addresses a fundamental efficiency bottleneck:

1. **Multi-user amortization**: First user incurs full prefill cost; subsequent users with identical tools see 29x faster responses. At 50 tools, this difference is 5.4 seconds saved per request.

2. **Tool set stability**: Many agentic systems maintain relatively stable tool repertoires (CRM APIs, retrieval functions, etc.), making content-hash based caching highly effective. Schemas only change during deployments.

3. **Request latency budget**: For tool-calling agents, TTFT dominates latency budget in low-concurrency scenarios. Reducing from 5.6s to 200ms enables sub-second tool selection, critical for interactive agents.

4. **Architecture-aware optimization**: The group caching requirement suggests practitioners cannot simply apply generic KV cache optimization techniques—tool schema caching specifically needs to preserve cross-tool dependencies in attention.

### Implementation Considerations

**Immediate applicability:**
- Drop-in optimization for existing tool-calling deployments (vLLM, TGI, local inference servers)
- Requires minimal changes to inference stack (KV cache restoration hooks)
- Works transparently to client code

**Scaling trade-offs:**
- Memory overhead grows with cache size and tool counts; 75+ tools hit GPU memory walls
- Flash attention integration (mentioned as future work) would be critical for agents with large tool repertoires
- Cache invalidation strategy needed when tool schemas change (content-hash handles this automatically)

**Quality considerations:**
- Group caching preserves quality exactly on tool-calling tasks; no accuracy-speed tradeoff observed
- Doesn't apply to single-tool scenarios (no cross-tool dependencies to preserve)
- Benefits are orthogonal to other LLM optimization techniques (quantization, speculative decoding, etc.)

### Strategic Value for Agent Systems

For agentic systems at scale:
1. **Latency reduction** directly improves user experience and enables synchronous agent interactions
2. **Throughput improvement** through reduced GPU compute per request lets identical tool sets serve more users
3. **Cost reduction** at inference providers (fewer prefill tokens = reduced compute billing)
4. **Stable cache keys** mean deployments can pre-warm caches with common tool combinations

## Sources

- [GitHub - spranab/contextcache: Persistent KV cache with content-hash addressing for tool-augmented LLMs](https://github.com/spranab/contextcache)
- [r/MachineLearning - ContextCache research post](https://old.reddit.com/r/MachineLearning/comments/1rglj2n/r_contextcache_persistent_kv_cache_with/)
- [Zenodo paper record](https://zenodo.org/records/18795189)
