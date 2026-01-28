---
title: High-End Workstation Inference Benchmarks - GPU vs CPU+GPU Trade-offs
source: r/LocalLLaMA
url: https://www.reddit.com/r/LocalLLaMA/comments/1qorbdk/dual_rtx_pro_6000_workstation_with_115tb_ram/
researched_at: 2026-01-28T00:00:00Z
fetch_method: concurrent-browser
---

# High-End Workstation Inference Benchmarks: GPU vs CPU+GPU Trade-offs

## Overview

Enterprise-scale inference workstations face a fundamental architectural trade-off between throughput and concurrency. A dual RTX PRO 6000 workstation with 1.15TB RAM and 96-core EPYC processor demonstrates that pure GPU inference (int4 quantization) achieves 2-4x higher prefill speeds but caps at 3 concurrent requests due to KV-cache saturation, while CPU+GPU offloading (native fp8) scales to 10+ users at the cost of lower decode performance. Queue time—not raw token throughput—becomes the dominant bottleneck at scale.

## Key Points

- **Hardware spec**: 2x RTX PRO 6000 Max-Q (192GB VRAM), AMD EPYC 9645 (96-core), 1.15TB DDR5 ECC RAM costs $30K-$50K and serves as a practical upper bound for local team inference
- **GPU-only (int4)**: 2-4x faster prefill (GPU+CPU approach slower at 110-260 tok/s on short context), but hits hard 3-request ceiling with full-VRAM model; decode remains fast (~25-35 tok/s single-user) but becomes unreliable under load
- **CPU+GPU offloading (fp8)**: Lower prefill speed, but massive KV-cache advantage (580K tokens tested) scales gracefully to 10+ concurrent users; decode degrades to 3-8 tok/s at 10 users but remains submillisecond-latency queuing
- **Context scaling matters**: KTransformers uses layer-wise prefill on GPU—at ~4K token threshold, computation shifts GPU-bound, *improving* throughput despite CPU memory bandwidth limitations
- **Queue time dominates SLA**: Beyond saturation, queue time explodes exponentially; TTFT and E2E latency are driven by queueing, not compute speed—a $50K workstation serving 50 agents sees 30+ second queue delays with int4

## Technical Details

### Measurement Methodology
- **sglang metrics**: Per-request TTFT, prefill speed, decode speed, E2E latency
- **Concurrent load**: 1, 2, 4, 6, 8, 10 simultaneous users via threaded API calls
- **Context workloads**: Short (~140 input, 300 output) and long (~64K input, 300 output)
- **No prefix caching**, but KV-cache explicitly tuned for 600K token capacity

### Quantization Trade-off

**INT4 (SGLang + VRAM-resident)**
- Fits entirely in 192GB (MiniMax-M2.1 ~128GB)
- Single-user: 15s for 64K prefill, ~25-35 tok/s decode
- Multiuser limitation: Model + KV-cache contention; maxes at 3 parallel requests
- Beyond 3 users: requests queue; queue dominates latency (hits 20+ seconds at 10 users)

**FP8 (KTransformers + CPU Offload)**
- 64-bit weights streamed layer-wise from DDR5 (614GB/s theoretical bandwidth)
- Single-user: ~30s for 64K prefill (2x int4), ~8-3 tok/s decode at 1-10 users
- Scales to 10+ requests; KV-cache holds entire session history without collision
- Prefill recompute cost: grows with concurrent sessions; mitigation via batch-oriented scheduling

### Operational Reality

KTransformers configuration optimizes for concurrency over speed:
- `--max-total-tokens 600000` (KV-cache pooling)
- `--chunked-prefill-size 60000` (layer-wise GPU processing)
- `--max-running-requests 12` (admission control)
- `--mem-fraction-static 0.85` (reserved for KV-cache, not model)

SGLang int4 config assumes single-digit concurrent users:
- `--max-running-requests 16` (theoretical; KV-cache limits practical concurrency to ~3)
- `--mem-fraction-static 0.93` (starves KV-cache for model fit)
- Tensor parallelism across both GPUs (`--tp 2`)

## Implications

**For practitioners deploying local coding agents:**

1. **Do not optimize for raw token/s blindly.** A single 64K-token request forces queue time to dominate latency. Prefill speed matters only for few-user scenarios; scaling 4+ simultaneous agents requires KV-cache overhead budgeting.

2. **CPU+GPU offloading is undervalued for team scenarios.** The conventional wisdom ("quantize aggressively for GPU residency") breaks at ~4+ concurrent users. Fp8 with CPU offloading trades 30-40% decode speed for 3-10x concurrency ceiling and predictable latency.

3. **Context scaling inverts performance intuition.** Larger inputs (>4K tokens) favor CPU+GPU offloading because KTransformers layer-wise compute shifts work to GPU while amortizing memory overhead. Short contexts favor GPU-only.

4. **Queue time is an underreported SLA killer.** Even a $50K workstation serving 50 agents can hit 30+ second queue delays. Admission control (`--max-running-requests`) is a hard bound, not a soft target—exceeded load requires request shedding, not graceful degradation.

5. **Model choice drives deployment cost.** MiniMax-M2.1 fp8 (~128GB) fits two RTX 6000s; larger models (Qwen3-235B, DeepSeek V3.2) tested on same hardware show wider trade-offs. Preliminary results: GPT-OSS-120B hits 215-230 tok/s on GPU-only, vs 35 tok/s for MiniMax fp8—pure throughput metrics mask concurrency penalties.

**Recommended configuration by use case:**

- **Single-user, long-context analysis**: GPU-only int4 (VRAM-resident) + 1x RTX 6000 Pro
- **4-10 concurrent agents, mixed context**: CPU+GPU fp8 offload + 2x RTX 6000 Pro + 768GB DDR5
- **20+ agents or production SLA**: 4x RTX 6000 Pro (rack server) with server-edition blower cards + aggressive admission control

## Related

- [GPU Power Limiting Measurements Update](https://old.reddit.com/r/LocalLLaMA/comments/1nkycpq/gpu_power_limiting_measurements_update/) - Thermal/power trade-offs at 300W cap; minimal performance hit for TG workloads
- [KTransformers Documentation](https://github.com/kvcache-ai/ktransformers) - Layer-wise CPU offloading implementation; critical for long-context concurrency
- [SGLang Server Benchmarks](https://sglang.ai/) - Multi-GPU tensor parallelism and attention backend optimization (FlashInfer)
