# NTCompanion - Dataset Creation Tool with Intelligent Quality Filtering

| | |
|---|---|
| **Source** | r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning/comments/1qwamdv/](https://old.reddit.com/r/MachineLearning/comments/1qwamdv/p_dataset_creation_tool_with_intelligent_quality/) |
| **Researched** | 2026-02-06 |

## Overview

NTCompanion addresses a critical bottleneck in LLM fine-tuning: extracting high-quality training data from web-scale sources. The tool combines quality filtering heuristics with configurable crawling and template formatting to produce curated datasets suitable for domain-specific model training. Demonstrated effectiveness with Python documentation yielding ~2,000 examples achieving 75+ quality threshold.

## Key Technical Components

### Quality Scoring System (6 Factors)

1. **Information Density** - Distinguishes tutorials and explanations from filler content
2. **Educational Value** - Weights technical depth and learning applicability
3. **Structure Quality** - Evaluates formatting rigor (headers, lists, organization)
4. **Noise Filtering** - Removes navigation, ads, boilerplate systematically
5. **Length Optimization** - Targets 800-5000 character sweet spot for training data
6. **URL Pattern Recognition** - Prioritizes blog posts and documentation over homepages

### Architectural Features

- **Multi-threaded crawling** with configurable rate limiting and depth control (1-3 levels)
- **Content-type specific extraction** - Recognizes structural differences (recipes vs documentation vs code)
- **Repository support** - Processes GitHub repos and local codebases natively
- **Chat template formatting** - Out-of-box support for Llama, Mistral, Qwen, Phi, and Gemma families
- **Threshold-based filtering** - Configurable quality gates for output dataset
- **GUI interface** - Built on DearPyGUI for accessibility without CLI expertise

## Technical Insights

### Data Quality Problem Framing

The core insight is recognizing the gap between "web-scale data" and "training-quality data." Raw web scraping captures navigation elements, advertisements, and poorly written content that degrades model performance. The solution implements a multi-signal scoring approach rather than binary inclusion/exclusion.

### Practical Trade-off: Coverage vs Quality

The tool exposes a fundamental tension in dataset construction:
- **Low thresholds** (e.g., 50) → higher coverage, noisier dataset, potential quality degradation
- **High thresholds** (e.g., 75+) → smaller dataset, higher per-sample quality

The demonstrated case (Python docs at 75+ threshold yielding 2,000 examples) suggests reasonable yields even at strict quality gates.

### Implementation Choice: GUI-First Design

Using DearPyGUI instead of pure CLI/library interface indicates targeting practitioners without deep Python infrastructure experience. This lowers friction for adoption in applied settings but may constrain advanced scripting scenarios.

## Practical Applications

1. **Domain-Specific Model Tuning** - Organizations can curate datasets from proprietary documentation, reducing off-topic hallucination
2. **Language-Specific Optimization** - Demonstrated Python documentation use case; model showed marked improvement on Python-specific queries
3. **Rapid Prototyping** - Multi-threaded crawling with configurable depth enables 1-3 week dataset construction cycles vs months of manual curation
4. **Cost Optimization** - Quality filtering reduces training on low-signal examples, improving token efficiency for LoRA fine-tuning

## Limitations and Considerations

- **Threshold calibration** - No principled methodology disclosed for setting quality thresholds; likely requires domain-specific tuning
- **Evaluation metrics** - Scoring system uses heuristics; no comparison to human annotation or downstream task performance metrics
- **Scalability** - Multi-threading approach; no discussion of distributed crawling for large-scale corpus construction
- **Extraction reliability** - Content-type detection depends on URL patterns; may fail on dynamically routed or unusual content architectures

## Implications

For engineering organizations building specialized LLMs:

1. **Data pipeline commoditization** - Quality filtering is becoming accessible to smaller teams, reducing reliance on specialized data infrastructure
2. **Rapid iteration on domain coverage** - Organizations can experiment with dataset composition in weeks rather than quarters
3. **LoRA viability improves** - Smaller, higher-quality datasets may be more cost-effective with parameter-efficient tuning than larger noisy datasets with full fine-tuning
4. **Emerging best practice** - Quality scoring over simple scale is becoming mainstream; aligns with recent literature on data scaling laws showing quality trumps quantity

## Sources

- [NTCompanion GitHub Repository](https://github.com/noosed/NTCompanion) - Open source implementation
- [r/MachineLearning Discussion](https://old.reddit.com/r/MachineLearning/comments/1qwamdv/p_dataset_creation_tool_with_intelligent_quality/) - Original project post and community engagement
