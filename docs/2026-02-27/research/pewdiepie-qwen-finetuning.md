# PewDiePie Fine-tuned Qwen2.5-Coder-32B to Beat ChatGPT 4o on Coding

| | |
|---|---|
| **Source** | YouTube / Dexerto |
| **URL** | [youtube.com/watch?v=aV4j5pXLP-I](https://www.youtube.com/watch?v=aV4j5pXLP-I) |
| **Researched** | 2026-02-27 |

## Overview

PewDiePie conducted a months-long AI fine-tuning experiment that demonstrates the practical accessibility of customizing state-of-the-art coding models. Rather than building from scratch, he fine-tuned an existing LLM using custom datasets and coding benchmarks, initially achieving marginal gains over baseline before discovering critical dataset contamination issues that required retraining. The project revealed both the viability of independent model optimization and the methodological pitfalls that prevent claims of outperforming production systems like ChatGPT 4o.

## Key Points

- **Fine-tuning approach**: Started with an existing large language model and applied supervised fine-tuning (SFT) on custom coding datasets rather than training from scratch, making the project accessible with modest hardware compared to pre-training
- **Iterative performance gains**: Initial benchmark scores of 8% improved to 16% through format adjustments, then reached 19.6% after introducing reasoning data—demonstrating the effectiveness of targeted dataset design
- **Dataset contamination discovery**: Discovered that training data overlapped with benchmark test sets, invalidating initial results and requiring complete retraining—a critical lesson in evaluation methodology
- **Final validated results**: Achieved 39.1% after removing contaminated data, with performance competitive but not definitively superior to ChatGPT 4o on the chosen benchmark
- **Accessibility demonstrated**: Used 256 GB total VRAM (2x RTX 4000 Ada + 8x modified RTX 4090s) to run models locally, proving that meaningful fine-tuning doesn't require hyperscaler resources

## Technical Details

**Hardware Stack**: 2x RTX 4000 Ada cards (48 GB each) plus 8x modified RTX 4090s (48 GB each) = 256 GB aggregate VRAM, sufficient for running 70B+ parameter models locally with fine-tuning capability.

**Benchmark Methodology**: Chose a specific coding format used by AI coding agents as the evaluation target, recognizing that general-purpose benchmarks may not capture specific use-case performance.

**Qwen2.5-Coder-32B Baseline**: The base model already achieves competitive parity with GPT-4o on multiple code generation benchmarks (EvalPlus, LiveCodeBench, BigCodeBench) and scores 73.7 on Aider code repair tasks—making it an excellent candidate for domain-specific fine-tuning.

**Optimization Tools**: Unsloth framework enables 2x faster fine-tuning with 60% lower memory consumption versus Flash Attention 2 + Hugging Face, demonstrating that efficiency gains unlock fine-tuning at scale.

**Configuration Gotchas**: Qwen base model has undertrained chat template tokens (`<|im_start|>`, `<|im_end|>`) that should not be used during fine-tuning to avoid degradation; pad tokens must not use `<|endoftext|>` to prevent infinite generation loops.

## Implications

**For practitioners**: Fine-tuning existing 32B models is now accessible without massive infrastructure. The Qwen2.5-Coder line already matches or exceeds GPT-4o on general code tasks, so ROI comes from domain specialization—not beating production systems. Focus fine-tuning efforts on task-specific formats and reasoning patterns rather than general coding ability.

**Methodological rigor required**: The benchmark contamination discovery underscores that leaderboard claims require strict train/test separation. Custom benchmarks are powerful but demand rigorous holdout validation. PewDiePie's experience shows even well-intentioned projects can accidentally contaminate datasets at scale.

**Model selection**: Qwen2.5-Coder-32B with YaRN context extension to 128K tokens offers a strong baseline. Unsloth's efficiency gains make T4 GPU fine-tuning feasible in Google Colab for prototyping.

**Trade-offs**: Local self-hosting via fine-tuned open models trades API dependency for operational complexity—you own inference latency, update cycles, and failure modes. Worth it for proprietary codebases or consistent workloads; risky for low-volume, variable tasks where managed APIs absorb operational overhead.

**Competitive landscape**: Qwen 3 has since emerged with higher benchmark scores, indicating rapid iteration in open-source coding models. Fine-tuning approach must account for model churn; version-locking datasets mitigates retraining overhead.

## Sources

- [Dexerto: PewDiePie reveals months-long AI experiment](https://www.dexerto.com/youtube/pewdiepie-reveals-months-long-ai-experiment-that-he-says-tops-chatgpt-3326532/) - Core project details including dataset contamination discovery and final validated results
- [Tom's Hardware: PewDiePie goes all-in on self-hosting AI](https://www.tomshardware.com/tech-industry/artificial-intelligence/pewdiepie-goes-all-in-on-self-hosting-ai-using-modded-gpus-with-plans-to-build-own-model-soon-youtuber-pits-multiple-sentient-chatbots-against-each-other-to-find-the-best-answers) - Hardware configuration and broader AI experimentation context
- [Qwen Official: Qwen2.5-Coder Family Blog](https://qwenlm.github.io/blog/qwen2.5-coder-family/) - Baseline model benchmarks and training methodology
- [Unsloth: Qwen Coder Fine-tuning](https://unsloth.ai/blog/qwen-coder) - Optimization techniques and configuration pitfalls
- [Dexerto: World's Biggest Gaming YouTuber Makes Transition](https://eu.36kr.com/en/p/3536983566850944) - Extended context on AI model training experiments
