# State of Brain Emulation Report 2025

| | |
|---|---|
| **Source** | arXiv |
| **URL** | [arxiv.org/abs/2510.15745](https://arxiv.org/abs/2510.15745) |
| **Researched** | 2026-01-26 |

## Overview

The 2025 report reassesses whole brain emulation progress since Sandberg and Bostrom's 2008 roadmap, evaluating three capability areas: neural dynamics recording, connectomics mapping, and computational emulation. The core finding: structural imaging scales faster than functional recording, shifting the bottleneck to solving the structure-to-function problem. Human-scale emulation remains constrained by acquisition and computation, not fundamental physics.

## Key Points

- **Three orders of magnitude improvement** in neural recording since the 1980s; connectomics costs dropped from $16,500 to ~$100 per neuron
- **No organism has >90% whole-brain single-neuron functional coverage**—C. elegans at 50%, Drosophila at 68%, larval zebrafish at 80%, mouse at ~1%
- **Memory, not processing power, is the primary computational constraint** for mammalian-scale emulation
- **Automation is the strategic priority**—AI-driven segmentation and proofreading could reduce connectomics costs by orders of magnitude
- **Structure-to-function inference** remains unsolved; molecular annotation unavailable in electron microscopy

## Technical Details

### Progress Metrics
| Organism | Connectome | Functional Coverage | Behavioral Fidelity |
|----------|-----------|---|---|
| *C. elegans* | Complete | ~50% | Good |
| *Drosophila* | Complete CNS | ~68% | Rich |
| Larval zebrafish | Partial | ~80% | Diverse |
| Mouse | Partial | ~1% | Complex |
| Human | Milestones only | <0.1% | N/A |

### Computational Requirements
- **Small organisms**: Single GPU feasible
- **Mouse brain**: GPU clusters required
- **Human brain (86B neurons)**: Exascale systems comparable to frontier AI models
- **Bottleneck transition**: Data acquisition → memory/bandwidth at scale

## Implications

**For computational neuroscience teams**: Invest in automation infrastructure (segmentation, proofreading) rather than manual throughput. The field's trajectory depends on reducing per-neuron costs by 1-2 orders of magnitude, not incremental sensor improvements.

**For systems architecture**: Structure-to-function bridging demands portfolio approaches—combine dense small-organism connectomes with sparse mammalian recordings using biophysical priors. Standardized benchmarks (neural prediction + behavioral testing + perturbation experiments) must guide validation.

**For resource planning**: Human-scale emulation computational requirements resemble modern large-scale AI training, not modest HPC clusters. Organizational structure shifts from university labs to specialized research entities better suited for long-term integration work.

**Trade-off consideration**: Complete functional recording at mammalian scale remains technically impractical. Strategic focus should be minimal emulation (connectome + key cell types + plasticity + neuromodulation) rather than exhaustive data acquisition.

## Related

- [Sandberg & Bostrom, 2008 Whole Brain Emulation Roadmap](https://www.fhi.ox.ac.uk/brain-emulation-roadmap/) - Original framework guiding this reassessment
- [C. elegans Connectome](https://www.wormatlas.org/) - Reference minimal emulation target
- [Foresight Institute Brain Emulation Discussion](https://diyhpl.us/wiki/transcripts/foresight-institute/niccolo-zanichelli-state-of-brain-emulation-2025/) - Author talk on report findings
