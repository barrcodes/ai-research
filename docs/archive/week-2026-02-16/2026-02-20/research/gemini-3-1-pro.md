# Gemini 3.1 Pro

| | |
|---|---|
| **Source** | Google DeepMind / Google Blog |
| **URL** | [blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-1-pro](https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-3-1-pro) |
| **Researched** | 2026-02-20 |

## Overview

Gemini 3.1 Pro is Google's upgraded reasoning model featuring 2X+ performance improvement on ARC-AGI-2 (77.1% vs 38% for 3 Pro) and enhanced agentic capabilities. The model introduces adjustable "thinking levels" for cost-performance trade-offs and substantially improves 3D spatial reasoning, making it architecturally significant for agent development and complex problem-solving workflows.

## Key Points

- **Reasoning leap**: Verified 77.1% on ARC-AGI-2, demonstrating 2X improvement in ability to solve novel logic patterns—a critical metric for agentic autonomy
- **Thinking levels**: Low, Medium, and High thinking modes allow runtime control of reasoning depth vs latency, enabling cost-optimized agentic dispatch
- **3D spatial reasoning**: Substantially improved understanding of 3D transformations—critical for code generation in animation pipelines and spatial computation
- **Agentic improvements**: Enhanced software engineering behavior, spreadsheet automation, and financial domain reasoning; 80.6% on SWE-Bench Verified for code task autonomy
- **Multimodal at scale**: 1M token context window processing text, code, images, audio, and video simultaneously

## Technical Details

| Metric | Score |
|--------|-------|
| ARC-AGI-2 (reasoning) | 77.1% |
| GPQA Diamond (science) | 94.3% |
| SWE-Bench Verified (code autonomy) | 80.6% |
| MRCR v2 Long Context (128k) | 84.9% |
| LiveCodeBench Pro | Elo 2887 |
| MMMLU (multimodal knowledge) | 92.6% |

**Architecture**: Natively multimodal transformer-based model with 1M token input context and 64K token output limit. Builds directly on Gemini 3 Pro architecture and training approach.

**Availability**: Gemini API via AI Studio, Vertex AI, Gemini Enterprise, Gemini CLI, GitHub Copilot (public preview), and Android Studio.

## Implications

**For agent architects**: The 2X reasoning boost on ARC-AGI-2 signals meaningful progress toward novel problem-solving without explicit task specification. Thinking levels enable agentic routing strategies—delegate simple queries to Low thinking (cost/speed), reserve High thinking for decomposition and planning phases.

**For code generation**: 3D reasoning improvement directly impacts practical automation—animation pipeline code generation, CAD task automation, and spatial algorithm development become feasible where previous models failed.

**For deployment**: 1M context window supports few-shot agent prompting with complete codebases or extensive documentation without truncation. Compare this to agents requiring chunking/retrieval in prior architectures.

**Trade-offs**: Higher thinking levels introduce latency—critical for interactive agentic workflows. Benchmark focus on reasoning suggests this release optimizes for complexity over speed; estimate actual inference latency requirements for your use case.

## Sources

- [Google DeepMind Gemini 3.1 Pro Model Card](https://deepmind.google/models/model-cards/gemini-3-1-pro/) - Official technical specifications
- [Google Cloud Blog: Gemini 3.1 Pro availability](https://cloud.google.com/blog/products/ai-machine-learning/gemini-3-1-pro-on-gemini-cli-gemini-enterprise-and-vertex-ai) - Deployment options
- [VentureBeat: Gemini 3.1 Pro reasoning performance analysis](https://venturebeat.com/technology/google-launches-gemini-3-1-pro-retaking-ai-crown-with-2x-reasoning) - Benchmark context
- [GitHub Changelog: Gemini 3.1 Pro in GitHub Copilot](https://github.blog/changelog/2026-02-19-gemini-3-1-pro-is-now-in-public-preview-in-github-copilot/) - Developer tool integration
