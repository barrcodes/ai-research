---
title: Build with Nano Banana Pro, our Gemini 3 Pro Image model
source: Google Blog (Technology/Developers)
url: https://blog.google/technology/developers/gemini-3-pro-image-developers/
researched_at: 2026-01-26T00:00:00Z
---

# Nano Banana Pro: Gemini 3 Pro Image for Developers

## Overview

Gemini 3 Pro Image (codenamed "Nano Banana Pro") is Google's latest image generation and editing model designed for production-grade asset creation. Released November 2025, it combines advanced reasoning capabilities with superior text rendering and real-time knowledge grounding, available via Gemini API, Google AI Studio, and Vertex AI for enterprise workloads.

## Key Points

- **Reasoning-powered generation**: Uses internal "thinking" process to parse complex prompts, generates interim "thought images" for refinement before final output (not charged)
- **Superior text rendering**: ~94% accuracy rendering legible, stylized text directly in images across multiple languages—critical for UI mockups, infographics, document visuals
- **Reference image composition**: Mix up to 14 reference images (6 objects + 5 human images) for character/object consistency and high-fidelity composition
- **Grounding with Google Search**: Verifies facts and generates imagery based on real-time data—enables contextual visualizations without hallucinations
- **Resolution flexibility**: Native 1K, 2K, 4K support with adaptive aspect ratios (1:1, 3:2, 16:9, 9:16, 21:9 and more)
- **Fine-grained controls**: Localized edits, lighting/focus adjustments, camera transformations for iterative refinement
- **SynthID watermarking**: Embedded digital watermarks on all generated/edited images denote AI origin for provenance tracking

## Technical Details

| Aspect | Details |
|--------|---------|
| **Model IDs** | `gemini-3-pro-image-preview` (Nano Banana Pro), `gemini-2.5-flash-image` (faster fallback) |
| **Input modalities** | Text prompts, reference images, multi-turn conversation context |
| **Output modalities** | Images (configurable resolution/aspect ratio), text descriptions |
| **API access** | Gemini API, Google AI Studio (public), Vertex AI (enterprise) |
| **Reference images** | Up to 14 total: 6 for objects, 5 for humans, 3 for other uses |
| **Aspect ratios** | 1:1, 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9 |

**Integration ecosystem**: Adobe, Figma, and Google Antigravity (agentic UI generation platform) already support the model. Coding agents in Antigravity can directly invoke image generation for UI mockup production.

## Implications

**For product teams**: Text rendering accuracy makes this suitable for replacing manual design work on marketing materials, technical documentation, and UI prototypes. The grounding-with-search feature eliminates outdated training data constraints—visualizations stay contextually accurate.

**For enterprise workflows**: Vertex AI integration provides Provisioned Throughput and Pay-As-You-Go pricing models, advanced safety filters, and SynthID watermarking for compliance. Multi-image composition enables consistent character/product visualization across campaigns.

**Trade-offs**: The reasoning mode adds latency vs. faster alternatives like Gemini 2.5 Flash Image. Interim thought images aren't charged, but composition complexity increases token consumption. Reference image limits (5 humans) may constrain batch character generation.

**When to use**: Production asset generation, UI/UX mockups, marketing content with accurate text/branding, real-time news/data visualization. Not ideal for latency-sensitive applications or simple variations (use Gemini 2.5 Flash instead).

**Integration approach**: REST API with JSON payload for prompts, images, and config. SDKs available for Python, JavaScript, Go, Java. Batch processing support for enterprise volume workloads.

## Related

- [Nano Banana Pro Overview](https://deepmind.google/models/gemini-image/pro/) - DeepMind model card with architecture details
- [Gemini API Image Generation Docs](https://ai.google.dev/gemini-api/docs/image-generation) - Code examples and API reference
- [Vertex AI Gemini 3 Pro Image](https://docs.cloud.google.com/vertex-ai/generative-ai/docs/models/gemini/3-pro-image) - Enterprise deployment guide
- [Gemini 3 Reasoning Capabilities](https://blog.google/innovation-and-ai/technology/developers-tools/gemini-3-developers/) - Extended thinking feature documentation
