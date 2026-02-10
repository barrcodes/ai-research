# Opus 4.6 vs 4.5 on 3D VoxelBuild Benchmark

| | |
|---|---|
| **Source** | Reddit r/ClaudeAI |
| **URL** | [old.reddit.com/r/ClaudeAI/comments/1qx3war](https://old.reddit.com/r/ClaudeAI/comments/1qx3war/difference_between_opus_46_and_opus_45_on_my_3d/) |
| **Researched** | 2026-02-06 |

## Overview

A developer benchmarked Claude Opus 4.6 against Opus 4.5 using a custom 3D voxel building task (comparable to Minecraft construction). Opus 4.6 demonstrates significant improvements in spatial reasoning and structured JSON generation, with the author noting it now rivals GPT-4.5-Pro in this domain. The benchmark represents a practical, interpretable evaluation of architectural and spatial design capabilities.

## Key Points

- **Benchmark Design**: Custom task where models generate 3D voxel structures via JSON schema without reference images—purely text-prompt driven architectural reasoning
- **Performance Gap**: Visual comparison across 6 test scenarios (astronaut, flying aircraft carrier, fighter jet, medieval castle, cozy cottage, steam locomotive) shows Opus 4.6 produces materially better structural coherence
- **Author Assessment**: "Definitely a huge improvement! In my opinion it actually rivals ChatGPT 5.2-Pro now"
- **Cost**: ~$22 API spend for 7 completed builds (15 total builds benchmarked, with 8 additional pending)
- **Accessibility**: Complete benchmark system (prompts, JSON schema, evaluation harness) open-sourced on GitHub; interactive evaluation platform available for community testing

## Technical Details

**Benchmark Architecture:**
- System prompt defining voxelBuilder JSON schema
- Models receive no reference images—pure text-to-3D constraint
- Direct API evaluation (not web UI, which would include external tools)
- Models given only a specialized voxelBuilder function; no code execution or compilation
- Results displayed in interactive web arena at minebench.vercel.app

**Methodology Implications:**
- Isolates raw model capability vs. tool-augmented performance
- JSON-structured output forces explicit spatial reasoning (cannot rely on natural language ambiguity)
- Test prompts range from simple objects (astronaut) to complex multi-component systems (aircraft carrier with engines, radar, planes)

## Implications

**For Practitioners:**
- Opus 4.6 shows meaningful improvement in constrained, structured reasoning tasks—relevant for code generation, configuration management, and procedural system design
- Benchmark methodology demonstrates value of task-specific evaluation over general benchmarks; visual comparison communicates model gaps better than aggregate metrics
- Open-source approach invites community participation and iterative refinement, establishing a replicable pattern for domain-specific LLM evaluation

**Architectural Significance:**
- The shift toward specialized, domain-specific benchmarks reflects market maturation—general benchmarks (MMLU, coding) now less informative for practical deployment decisions
- JSON-schema constraint as evaluation technique directly applicable to real-world API usage where structured output is operational requirement
- Cost-transparent benchmarking ($22/7 builds) makes comparative evaluation accessible to individual developers, not just large labs

**Future Work:**
- Benchmarked 7 of 15 planned builds; author pending additional API credits for completion
- Codex 5.3 xhigh not yet available via API but slated for testing when released
- Benchmark system designed to accept community submissions and comparative model runs

## Sources

- [GitHub: minebench](https://github.com/Ammaar-Alam/minebench) - Complete benchmark repository with system prompts and evaluation harness
- [minebench.vercel.app](https://minebench.vercel.app/) - Interactive benchmark results and arena
- [minebench.vercel.app/local](https://minebench.vercel.app/local) - Local evaluation mode with copy-paste system prompt for web UI testing
