# Transformers.js v4 Preview: Now Available on NPM!

| | |
|---|---|
| **Source** | Hugging Face Blog |
| **URL** | [huggingface.co/blog/transformersjs-v4](https://huggingface.co/blog/transformersjs-v4) |
| **Researched** | 2026-02-11 |

## Overview

Transformers.js v4 is a major rewrite delivering 10x build speed improvements, WebGPU-accelerated inference across browsers and server runtimes, and support for advanced architectures like Mixture of Experts and state-space models. The library now handles models exceeding 8B parameters with performance reaching ~60 tokens/second on consumer hardware.

## Key Points

- **WebGPU runtime rewrite**: Complete C++ reimplementation with ONNX Runtime team collaboration enables cross-environment deployment (browser, Node.js, Bun, Deno) with ~4x speedup for attention operations via specialized CONTRIB operators
- **Build system overhaul**: Webpack to esbuild migration reduces build time from 2s to 200ms (10x) and bundle size by ~10% average, with `transformers.web.js` shrinking 53%
- **Extended model support**: 200+ architectures including Mamba (state-space models), MLA (Multi-head Latent Attention), and MoE patterns; now runs large models like GPT-OSS 20B (q4f16) at viable inference speeds
- **Tokenizers.js extracted**: Standalone 8.8kB gzipped library with zero dependencies for tokenization, decoupling from main library
- **Monorepo architecture**: PNPM workspaces split 8,000+ line `models.js` into focused modules; examples moved to separate repository

## Technical Details

WebGPU runtime optimization targets high-leverage attention operations. `com.microsoft.MultiHeadAttention` achieves ~4x speedup on BERT embeddings; `com.microsoft.MatMulNBits` and `com.microsoft.GroupQueryAttention` enable efficient quantized inference. M4 Pro Max achieves ~60 tokens/second with GPT-OSS 20B (q4f16), making real-time inference viable on consumer hardware.

Monorepo restructuring enables independent versioning of tokenizers and core inference. Tokenizers.js loads model configs directly from Hugging Face Hub with clean API:

```javascript
const tokenizer = new Tokenizer(tokenizerJson, tokenizerConfig);
const encoded = tokenizer.encode("Hello World");
// { ids: [9906, 4435], tokens: ['Hello', 'ĠWorld'], ... }
```

WASM caching enables offline operation; library handles model exceeding 8B parameters through architecture improvements.

## Implications

This release enables **browser-native AI applications** without server dependency—a significant shift for privacy-conscious deployments. The 10x build speedup reduces friction for framework integration and prototyping. However, architects should evaluate WebGPU driver support across target browsers; fallback to WASM remains necessary for older clients.

The monorepo + independent tokenizers pattern improves dependency hygiene. Teams can now update tokenization logic without pulling entire model library. Quantized model support (q4f16) makes >8B parameter models practical on edge devices, though you'll need to validate memory and performance for your specific hardware targets.

Consider this for: offline-capable PWAs, edge inference nodes, privacy-first LLM UIs, and resource-constrained deployments. Server-side Node.js usage competes directly with Python inference frameworks—weigh startup overhead, cold start performance, and team familiarity.

## Sources

- [Transformers.js v4 Blog Post](https://huggingface.co/blog/transformersjs-v4) - Official Hugging Face announcement with technical specifications
- [Transformers.js Repository](https://github.com/xenova/transformers.js) - Main library with ONNX Runtime integration
- [Tokenizers.js Package](https://www.npmjs.com/package/@huggingface/tokenizers) - Standalone tokenization library
