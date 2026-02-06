# GPT-5.3-Codex System Card

| | |
|---|---|
| **Source** | OpenAI |
| **URL** | [openai.com/index/gpt-5-3-codex-system-card/](https://openai.com/index/gpt-5-3-codex-system-card/) |
| **Researched** | 2026-02-06 |

## Overview

GPT-5.3-Codex is OpenAI's first agentic coding model rated as "High capability" for cybersecurity tasks under their Preparedness Framework. It unifies frontier coding performance with extended reasoning, enabling long-horizon autonomous work across development and infrastructure tasks while running 25% faster than predecessors. This classification triggers OpenAI's strictest deployment safeguards, reflecting unprecedented concern about automated cyberattack potential.

## Key Points

- **First "High capability" cybersecurity classification**: Triggers formal safeguards; lacks definitive evidence of full attack automation but treats risk precautionarily
- **Performance:** 77.3% Terminal-Bench 2.0, 64.7% OSWorld-Verified, 77.6% Cybersecurity CTF; +13% to +26% gains over GPT-5.2-Codex on terminal and computer-use tasks
- **25% inference speedup** with fewer output tokens per patch—reduces cost and latency for agentic workflows
- **Dual-use tension**: Advanced reasoning enables both defense automation and offensive code synthesis at scale
- **Self-instrumental development**: Model assisted in debugging its own training pipeline and deployment

## Technical Details

**Capability Scope**: Operates as computer agent handling tool use, terminal execution, file operations, and multi-step debugging. Maintains context across long task chains with ability to adapt plans and resolve edge cases without reinitialization.

**Architecture**: Co-designed for and served on NVIDIA GB200 NVL72 systems; optimized for throughput over latency to support iterative human-AI interaction.

**Safety Mitigations**:
- Trusted Access program for vetted security researchers only
- Delayed API access to prevent large-scale automation
- Automated monitoring and threat intelligence-based enforcement
- Safety training across cybersecurity risk vectors

**Performance Trade-offs**: Strongest on terminal-heavy automation (77.3%) and cross-file refactoring; marginal gains (0.4%) on isolated single-file edits. Achieves gains with fewer tokens than prior versions—better inference economics despite expanded capability window.

## Implications

**For practitioners**: GPT-5.3-Codex trades broader attack surface for legitimate DevOps automation. The cybersecurity rating means access requires vetting and API credits (not open signup). This enforces a two-tier market: trusted defenders get full agentic capabilities; general developers wait for downstream safeguards.

**Architectural decision**: OpenAI's precautionary stance reflects genuine uncertainty—they don't claim definitive evidence the model can fully automate attacks, but structure deployment anyway. This inverts traditional risk frameworks where safeguards follow demonstrated harm.

**Competitive implications**: 25% speedup and extended reasoning window compress the gap between on-demand inference and agentic reasoning models, raising questions about whether separate "reasoning" products remain necessary.

**For your teams**: If building security tooling, enroll in Trusted Access pilot ($10M API credit commitment). If building general developer tools, wait for full API access and expect tighter usage patterns than prior Codex versions.

## Sources

- [OpenAI's new model leaps ahead in coding capabilities—but raises unprecedented cybersecurity risks](https://fortune.com/2026/02/05/openai-gpt-5-3-codex-warns-unprecedented-cybersecurity-risks/) - Fortune analysis of dual-use risks
- [OpenAI Releases GPT-5.3-Codex as Advanced Agentic...](https://mlq.ai/news/openai-releases-gpt-53-codex-as-advanced-agentic-coding-upgrade/) - ML Q benchmark coverage
- [GPT-5.3 Codex: Features, Benchmarks, and Migration Guide](https://www.digitalapplied.com/blog/gpt-5-3-codex-release-features-benchmarks-guide) - Technical specifications and benchmarks
- [Laravel News: GPT-5.3-Codex](https://laravel-news.com/gpt-5-3-codex) - Capabilities and performance metrics
- [MarkTechPost: OpenAI Just Launched GPT-5.3-Codex](https://www.marktechpost.com/2026/02/05/openai-just-launched-gpt-5-3-codex-a-faster-agentic-coding-model-unifying-frontier-code-performance-and-professional-reasoning-into-one-system/) - Comprehensive model overview
