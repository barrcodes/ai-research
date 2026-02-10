# RL Post-Training Frameworks at Scale: Practical Recommendations

| | |
|---|---|
| **Source** | r/MachineLearning Discussion |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qrer61/](https://old.reddit.com/r/MachineLearning/comments/1qrer61/d_what_framework_do_you_use_for_rl_posttraining/) |
| **Researched** | 2026-02-02 |

## Overview

A technical discussion on r/MachineLearning reveals a fragmented RL post-training ecosystem. Researchers evaluating frameworks for function-calling RL at scale encounter consistent trade-offs between maturity, flexibility, and engineering burden. No single framework offers both ease-of-use and full algorithmic control without significant customization.

## Framework Landscape

### Mature Production-Oriented Options

**verl** (ByteDance)
- Actively maintained with daily fixes; latest release last month
- Uses Ray for distributed inference/training separation (critical architectural pattern)
- Integrates vLLM for inference
- Supports mainstream models (Qwen, Mistral) out-of-the-box
- Trade-off: Import errors from deprecated transformers dependencies; optional dependency groups rely on unmaintained packages; requires repo-level fixes
- Community consensus: Most mature framework despite friction; used in production at scale

**OpenRLHF**
- Lightweight, researcher-friendly design philosophy
- Lacks FSDP support (primarily DeepSpeed-focused)
- Lower maintenance velocity: many open issues, infrequent PRs
- Preference for constrained hardware scenarios

### Early-Stage Research Frameworks

**torchforge** (Meta PyTorch)
- Designed for Pytorch Titan + vLLM integration
- Uses Monarch (not Ray) for distributed training; unfamiliar to most practitioners
- Unstable: test suite fails; requires extensive workarounds
- Architectural appeal: ideal design goals, insufficient maturity

**RLYX** (Research Project)
- Lightweight, modular codebase; easy to understand internals
- Clean abstraction design without over-layering
- Used in published research; validates implementation quality
- Trade-off: Hobby-project risk profile; limited community support

### Not Recommended For This Use Case

**trl** (Hugging Face)
- Well-suited for SFT; not appropriate for complex RL loops with custom reward functions
- Opinionated design constrains algorithm flexibility

**NVIDIA NeMo (Gym + RL)**
- Gym provides infrastructure; RL module handles optimization
- Requires manual pipeline orchestration between components
- Distribution installation issues; historical CUDA/dependency problems
- Not available via standard package managers (`uv add`, `pip install`)

## Architectural Insights from Discussion

### The Distributed Training/Inference Separation Problem

Ray's critical role emerges: it cleanly separates rollout (inference) from optimization (training), enabling asynchronous execution and resource utilization. Monarch (used by torchforge) purports similar capabilities but lacks battle-tested validation.

### Custom Reward Functions & Agent Loops

All mature frameworks assume opinionated RLHF loops. Stepping outside this path (custom environments, function-call-specific rewards, non-standard agent execution) introduces friction:
- Dependency abstraction layers not designed for extension
- Import compatibility issues (especially transformers deprecation cycles)
- No framework eliminates glue code entirely

### The Flexibility vs. Maturity Trade-Off

Practitioners converge on a pattern:
1. Use framework for rollout, sharding, and distributed logging (proven machinery)
2. Keep RL algorithmic logic thin and local (own code)
3. Accept inevitable glue code layer; optimize for deletability

Recommendation: Choose framework that makes it easiest to bypass or replace components later, not for initial setup smoothness.

## Specific Technical Preferences

### Parallelism Preferences
- **FSDP preferred**: Full Sharded Data Parallel (PyTorch native)
- **DeviceMesh custom code**: Next preference for fine-grained control
- **Ray + vLLM**: Proven combination for inference/training separation
- **DeepSpeed**: Acceptable but less preferred by FSDP-familiar teams

### Model Support
Qwen and Mistral treated as baseline requirements. Custom model support acceptable if scaffolding present; full support write-ups required otherwise.

## Emerging Frameworks Not Yet Evaluated

**slime** (THUDM/Z.ai)
- Used by Z.ai for GLM models
- Uses Megatron-LM and SGLang natively
- Reduces overhead vs. alternative integrations
- Concern: Additional learning curve (Megatron, SGLang unfamiliar to typical practitioners)
- Candidate for teams already invested in Megatron ecosystem

**Function-Calling RL Maturity Gap**

Function calling and agentic RL are only recently gaining traction. This domain is the "secret sauce" of large labs:
- Meituan's recent "LongCat" paper discusses function-call-specific data; proprietary implementation (DORA) not open-sourced
- Most production approaches remain closed; public implementations lag 12+ months behind deployed systems
- Tool environment abstraction and dataset design critical but rarely published

## Practical Recommendations for Engineers

1. **Starting Point**: verl (ByteDance) despite dependency friction
   - Justification: Most battle-tested; Ray separation pattern proven; active maintenance
   - Mitigation: Plan for repository forking; budget 20-30% extra time for dependency resolution

2. **Research Flexibility Priority**: RLYX
   - Justification: Clean codebase; publication-backed; minimal abstraction overhead
   - Trade-off: Smaller community; self-support required

3. **Long-term Deployment**: Custom scaffolding around Ray + vLLM + PyTorch FSDP
   - Justification: All frameworks eventually converge here
   - Effort: 8-12 weeks of engineering; offset by algorithmic flexibility
   - Framework as documentation/reference, not constraint

4. **Avoid**: Building on trl or NeMo for custom RL unless constrained to existing Hugging Face/NVIDIA tooling

## Implications

**No framework abstraction layer is mature enough to eliminate distributed systems engineering.** Success requires:

1. **Distributed systems expertise**: Ray, sharding, fault tolerance, logging at scale
2. **Patience with friction**: All frameworks have maturity gaps; budget contingency time
3. **Documentation discipline**: Custom implementations require team documentation offsetting framework documentation
4. **Algorithmic specificity**: Function-calling RL has insufficient public IP; expect 3-6 month exploration phase to find optimal environment/reward design

**For teams with 15+ person-years infrastructure experience**: Custom scaffolding likely faster than fighting framework abstractions. For smaller teams: accept framework constraints; focus algorithmic innovation on algorithm selection and hyperparameter tuning within existing bounds.

## Sources

- [verl GitHub](https://github.com/verl-project/verl) - ByteDance RL training framework
- [torchforge GitHub](https://github.com/meta-pytorch/torchforge) - Meta PyTorch RL framework
- [OpenRLHF GitHub](https://github.com/OpenRLHF/OpenRLHF) - Lightweight RL framework
- [RLYX GitHub](https://github.com/goddoe/RLYX) - Research RL framework
- [slime GitHub](https://github.com/THUDM/slime) - SGLang-native post-training framework
- [Ray Project](https://www.ray.io/) - Distributed computing framework
- [vLLM GitHub](https://github.com/vllm-project/vllm) - Inference optimization
