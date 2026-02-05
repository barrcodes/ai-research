# NTTuner - GUI to Locally Fine-Tune AI Models with Unsloth

| | |
|---|---|
| **Source** | r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qwbc9d](https://old.reddit.com/r/MachineLearning/comments/1qwbc9d/p_nttuner_gui_to_locally_finetune_ai_models_with/) |
| **Researched** | 2026-02-05 |

## Overview

NTTuner is a desktop GUI application that abstracts away the operational complexity of local LoRA fine-tuning and model deployment. Combined with NTCompanion (a dataset builder), it attempts to reduce a multi-step, tool-fragmented workflow into a drag-and-drop process: scrape data → fine-tune → deploy to Ollama. The implementation leverages Unsloth for GPU acceleration (2-5x speedups) with CPU fallback, DearPyGUI for responsive rendering, and includes automatic GGUF conversion.

## Key Points

- **End-to-end workflow abstraction**: Consolidates LoRA fine-tuning, quantization, and Ollama deployment into a single application—addressing the genuine friction point of bouncing between CLI tools, Python scripts, and manual conversion steps.

- **Dual-platform acceleration strategy**: Unsloth-accelerated training on compatible GPUs with transparent CPU fallback. This pragmatic approach maximizes accessibility while retaining performance gains where available.

- **Dataset tooling as architectural necessity**: NTCompanion solves the unglamorous but critical problem of training data curation—multi-threaded web scraping with junk filtering (navigation, boilerplate) and 6-factor quality scoring. Directly outputs JSONL in the format NTTuner expects.

- **Infrastructure choices**: DearPyGUI for GPU-accelerated GUI (avoids Electron overhead), BeautifulSoup for parsing, optional Bloom filters for large-scale crawls. Non-blocking training UI prevents the common "app freeze during training" UX failure mode.

- **Reproducibility built-in**: JSON config persistence enables experiment tracking and re-runnable pipelines—important for iterating on fine-tuning strategies.

## Technical Details

**NTTuner Components:**
- Drag-and-drop JSONL ingestion with GPU auto-detection and dependency installation
- Background training process with real-time non-blocking logs
- One-click GGUF export with quantization options
- Config-based reproducibility (saves training parameters as JSON)

**NTCompanion Components:**
- Configurable multi-threaded crawling (1-50 workers)
- Content extraction intelligence to filter DOM cruft (menus, banners, ads)
- Preset templates for common content archetypes (recipes, tutorials, documentation, blogs)
- Support for major chat templates (Llama, Qwen, Phi, Mistral, Gemma)

**Hardware Requirements:**
- Minimum 8GB RAM (16GB recommended)
- NVIDIA GPU with 8GB+ VRAM recommended but not required
- Python 3.10+
- Cross-platform: Windows, Linux, macOS

## Architectural Insights

**What's novel here isn't the individual components—Unsloth, GGUF conversion, Ollama integration already exist.** The novel contribution is the **operational pattern**: recognizing that local LLM fine-tuning adoption is bottlenecked not by model availability but by UX friction and workflow fragmentation. By providing a cohesive, stateful GUI that keeps models in Ollama (the local runtime standard), NTTuner positions itself as a productivity multiplier for practitioners who iterate on domain-specific models.

The dataset scraper aspect is equally important—fine-tuning is only useful if you have clean, domain-relevant data. Building this directly into the tool avoids the common anti-pattern of exporting data, manually cleaning, and re-importing.

## Known Limitations

- JavaScript-heavy sites lack proper headless browser support, limiting scraping fidelity
- CPU-only training paths require some manual GGUF conversion steps
- Quality scoring optimized for English; multi-language support not yet mature

## Implications for Practitioners

1. **Accessibility of local fine-tuning**: This tool lowers the barrier for teams without ML infrastructure to customize models on proprietary data—relevant for enterprises building domain-specific assistants without cloud dependencies.

2. **Data→Model velocity**: By coupling scraping, cleaning, and training in one tool, iteration cycles on fine-tuned models should compress significantly. This matters for experimentation workflows.

3. **Deployment simplicity**: Direct Ollama integration means trained models are immediately consumable by existing local inference stacks—no format translation or additional infrastructure.

4. **What's still missing**: Vision models, advanced data augmentation, DPO/ORPO fine-tuning variants, and better handling of JS-rendered content are acknowledged gaps. The roadmap signals ongoing development.

## Sources

- [GitHub: NTTuner](https://github.com/noosed/NTTuner) - Fine-tuning GUI application
- [GitHub: NTCompanion](https://github.com/noosed/NTCompanion) - Dataset builder and scraper
