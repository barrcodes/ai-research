# Jim Fan: The Second Pre-training Paradigm

| | |
|---|---|
| **Source** | NVIDIA Robotics Research / X (Twitter) |
| **URL** | [old.reddit.com/r/singularity/comments/1qva47p/](https://old.reddit.com/r/singularity/comments/1qva47p/nvidia_director_of_robotics_dr_jim_fan_article/) |
| **Researched** | 2026-02-04 |

## Overview

Dr. Jim Fan, NVIDIA Director of Robotics, articulates a fundamental paradigm shift in AI pre-training away from next-token prediction (language models) toward world modeling—predicting the next physical state conditioned on actions. This transition has profound implications for embodied AI, robotics, and multimodal systems, positioning 2026 as the critical inflection point where large world models begin laying real foundations rather than serving as novelty video generation platforms.

## Key Points

- **First Paradigm**: Next-token prediction (LLMs) enabled rapid scaling and breakthroughs in language understanding, but remains fundamentally language-first
- **Second Paradigm**: World modeling (next physical state prediction) shifts focus to visual/sensorimotor understanding through video foundation models and physics simulation
- **Vision-First Architecture**: Humans devote ~33% of cortex to visual processing; language occupies relatively compact regions. Vision is the highest-bandwidth channel linking brain, motors, and physical world
- **Biological Proof**: Apes demonstrate sophisticated physical intelligence with minimal language capability (BERT/GPT-1 level), yet possess robust mental models of counterfactuals and physical consequences
- **Current VLA Limitations**: Vision-Language-Action models are "LVAs" (language-first) by architecture and training, with vision treated as a second-class citizen grafted onto pre-trained language backbones
- **VLM Knowledge Gap**: Language models allocate parameters primarily to knowledge retrieval (semantic understanding), not physics—knowing "Coca-Cola bottle" differs fundamentally from understanding how liquid spreads and stains
- **Scaling Advantage**: Training data availability heavily favors vision—YouTube and smart glasses capture visual streams at scales exceeding all text training data combined

## Technical Details

**World Modeling Definition**: Predicting plausible next world state(s) conditioned on action, spanning:
- RGB video sequences (8-10 seconds to minutes)
- Spatial 3D motion and trajectories
- Proprioceptive feedback
- Tactile sensing (emerging)

**Core Mechanism**: World models function as learnable physics simulators and rendering engines, capturing counterfactuals—reasoning about how futures would diverge under alternative actions without language mediation.

**Training Approach**: Models billions of hours of video pixels to develop intuitive physics understanding, contrasting with VLMs that optimize for semantic knowledge through language as the primary abstraction layer.

**Research Challenges Ahead**:
1. Motor action decoding from predicted video states—unclear if pixel-space prediction is optimal objective
2. Latent space alternatives—exploring representations beyond pixel reconstruction
3. Robot data scaling requirements—determining volume needed and optimal teleoperation scaling strategies
4. Architecture design—whether unified vision-first models outperform modular approaches

## Implications

**For Practitioners**:
- Robot learning strategies must move beyond VLA frameworks toward native world modeling approaches
- Investment in video/visual foundation models will outpace language-only approaches for embodied tasks
- Compute requirements for world models exceed LLMs—scaling advantages emerge only now with sufficient hardware availability
- Robotics faces parallel research challenges to LLM breakthroughs; the "GPT-3 moment" for embodied AI remains unachieved

**For Architecture Decisions**:
- Vision-first designs eliminate language bottleneck for sensorimotor loops (the critical feedback loop for physical control)
- Multi-stage architectures (VLM→decoder) may be architecturally inferior to unified vision-grounded systems
- Scaling laws for world models require empirical validation—LLM recipes don't directly transfer
- Physical intelligence doesn't require linguistic reasoning chains; geometric/spatial reasoning operates independently

**For AGI Timelines**:
- Shifts focus from pure language scaling toward complementary visual/embodied reasoning paradigms
- Highlights that "AGI has not converged" and research methodology matters as much as scale
- Emphasizes open research problems rather than engineering-only approaches—fundamental architectural questions remain unsolved
- Suggests multimodal AGI requires solving robotics not as an application problem but as a core training paradigm

## Sources

- [NVIDIA Robotics Reddit Discussion](https://old.reddit.com/r/singularity/comments/1qva47p/nvidia_director_of_robotics_dr_jim_fan_article/) - Original Reddit post containing full article text from Jim Fan's X/Twitter thread
- [Sequoia Capital Podcast with Jim Fan](https://sequoiacap.com/podcast/training-data-jim-fan/) - Discussion on robot training approaches
- [The Physical Turing Test: Jim Fan on Nvidia's Roadmap for Embodied AI](https://inferencebysequoia.substack.com/p/the-physical-turing-test-jim-fan) - Additional perspective on embodied AI strategy
- [NVIDIA R²D² Blog](https://developer.nvidia.com/blog/r2d2-training-generalist-robots-with-nvidia-research-workflows-and-world-foundation-models/) - Research workflows for world foundation models
- [GR00T N1.6 NVIDIA Research](https://research.nvidia.com/labs/gear/gr00t-n1_6/) - Embodied AI research platform