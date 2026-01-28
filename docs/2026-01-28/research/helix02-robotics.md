---
title: Helix 02 - Full-Body Autonomy in Robotics
source: Figure AI
url: https://www.figure.ai/news/helix-02
researched_at: 2026-01-28T00:00:00Z
---

# Helix 02: Full-Body Autonomy in Robotics

## Overview

Figure AI's Helix 02 demonstrates unified end-to-end learning for humanoid robotics, replacing ~110k lines of hand-engineered control code with a 10M-parameter neural network that directly maps visual input to whole-body motor control. The system achieves 4-minute continuous autonomous kitchen tasks (61 loco-manipulation actions) with no human intervention, establishing pixels-to-actuators scalability.

## Key Points

- **Unified visuomotor architecture**: Single neural network directly connects all sensors (vision, touch, proprioception) to all actuators—the first demonstrated pixels-to-whole-body control on humanoid robots
- **System 0 foundation model**: 10M-parameter network trained on 1,000+ hours of human motion data across 200k+ parallel simulation environments, replacing hand-engineered C++ code almost entirely
- **Four orders of magnitude motor range**: Same network produces millimeter-scale finger precision and room-scale locomotion without mode switching
- **New sensing modalities**: Palm cameras provide in-hand visual feedback during occlusion; tactile sensors detect forces ≥3 grams (paperclip sensitivity)
- **Long-horizon task execution**: 4-minute dishwasher task integrating navigation, manipulation, and continuous balance with zero resets

## Technical Details

**Three-tier hierarchical control:**

| Layer | Frequency | Function |
|-------|-----------|----------|
| System 0 | 1 kHz | Balance + full-body coordination |
| System 1 | 200 Hz | Perception → joint targets |
| System 2 | Slower | Semantic reasoning + language grounding |

**System 0 design implications**: The foundation model encodes low-level motor primitives while allowing higher tiers to focus on task semantics. Domain randomization across 200k simulation environments enabled sim-to-real transfer without fine-tuning.

**Sensing innovation**: Palm cameras solve the occlusion problem during grasping. Tactile feedback at 3-gram threshold enables fine-grained manipulation without visual confirmation.

## Implications

**For robotics practitioners:**
- End-to-end learning is now viable for full-body control—the engineering burden shifts from inverse kinematics/dynamics to data generation and simulation fidelity
- Unified networks eliminate behavioral mode-switching, reducing failure modes at manipulation/locomotion transitions
- The 1 kHz System 0 layer proves low-level learning can maintain hard real-time constraints needed for balance and safety

**Trade-offs:**
- Requires massive simulation + human motion data (1000+ hours); not practical for one-off problems
- Four orders of magnitude motor range may sacrifice precision or locomotion efficiency compared to specialized controllers
- Foundation model approach creates dependency on simulation fidelity; poor domain randomization breaks real-world deployment

**When to apply:**
- Long-horizon tasks mixing navigation and manipulation (warehouse, hospitality, domestic)
- Where hand-engineered control code maintenance becomes a bottleneck
- Systems where sensing variability is high (occlusion, diverse environments)

## Related

- [Figure AI Research](https://figure.ai) - Company advancing humanoid robotics with learning-based control
- Sim-to-real transfer techniques - Critical dependency for the 200k environment training pipeline
- Neural network-based motion control - Foundation model approach scales beyond single-task learning
