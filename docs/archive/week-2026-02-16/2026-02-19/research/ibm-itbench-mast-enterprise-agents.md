# IBM and UC Berkeley Diagnose Why Enterprise Agents Fail Using IT-Bench and MAST

| | |
|---|---|
| **Source** | IBM Research & UC Berkeley |
| **URL** | [huggingface.co/blog/ibm-research/itbenchandmast](https://huggingface.co/blog/ibm-research/itbenchandmast) |
| **Researched** | 2026-02-19 |

## Overview

IBM and UC Berkeley analyzed 310 SRE (Site Reliability Engineering) execution traces across frontier, mid-tier, and open-source LLMs to understand why enterprise agents fail. They introduced MAST (Multi-Agent System Failure Taxonomy), a diagnostic framework that maps 14 distinct failure patterns into 3 categories, revealing that failure modes are model-class specific and partially recoverable through architectural fixes rather than scale alone.

## Key Points

- **Model-class failure signatures differ dramatically**: Frontier models (Gemini-3-Flash) fail with isolated, localized bottlenecks (2.6 failure modes/trace). Open models (GPT-OSS-120B) suffer cascading collapse (5.3 modes/trace) where early reasoning errors poison context and trigger compounding hallucinations.

- **Not all failures are equally fatal**: Step repetition, incorrect verification, and reasoning-action mismatches appear in successful runs. Fatal patterns are termination-related (FM-1.5: unaware of termination conditions, FM-3.1: premature termination) and context loss (FM-1.4).

- **Verification is the frontier model weakness**: Gemini-3-Flash correctly identifies signals in 52% of failed traces but terminates prematurely, confident in incomplete diagnosis. Open models struggle with action-reasoning alignment (92% failure rate) and context degradation over long traces.

- **Deterministic control beats better reasoning**: Solutions are architectural, not statistical. Frontier models need external verification gates; mid-tier models need finite state machines for task completion; open models need aggressive context hygiene and early error detection.

## Technical Details

**MAST Framework Structure:**
- **FC1 (System Design)**: Step repetition, conversation history loss, termination awareness
- **FC2 (Inter-Agent Alignment)**: Clarification failures, task derailment, reasoning-action mismatch
- **FC3 (Task Verification)**: Premature termination, incorrect verification

**Failure Mode Density by Model:**

| Model | Task Recall | Modes/Failed Trace | Critical Bottleneck |
|-------|-------------|--------------------|---------------------|
| Gemini-3-Flash | 75.5% | 2.6 | Incorrect verification (52% increase) |
| Kimi-K2 | 28.6% | 4.7 | Termination confusion (46-43% increase) |
| GPT-OSS-120B | 12.4% | 5.3 | Context loss + action mismatch (24% traces) |

IT-Bench evaluates agents on realistic IT automation: incident triage, log/metrics analysis, Kubernetes state changes in long-horizon tool loops. The corpus includes 1600+ annotated execution traces.

## Implications

**For Architecture Decisions:**

1. **Never trust self-termination in frontier models**—require hard external evidence (alert clearance, infrastructure state change) before marking tasks complete. Overconfidence in partial diagnosis is predictable at scale.

2. **Use finite state machines for mid-tier models (Kimi-K2 class)**—explicit termination control eliminates 46% of failures. This trades reasoning flexibility for reliability in production SRE contexts.

3. **Implement context windowing and early error detection for open models**—prevent minor misalignments from cascading. Once FM-2.6 (reasoning-action mismatch) appears, model behavior degrades rapidly.

4. **Force clarification prompts**—FM-2.2 (failing to ask for clarification) is preventable through explicit guards on ambiguous inputs.

**Practical Trade-offs:**

- Frontier models scale to complex reasoning but require guardrails against overconfidence
- Open models fail systemically; local fixes don't prevent downstream collapse
- Mid-tier models are viable with explicit control flow but lose adaptive reasoning
- Context length matters less than context quality—aggressive filtering prevents cascading failure better than larger windows

**When to Use Which Model:**
- **Gemini-3-Flash**: High-complexity diagnosis with external verification gates
- **Kimi-K2**: Medium-complexity tasks where termination logic is deterministic
- **GPT-OSS-120B**: Cost-critical tasks with minimal branching, short context windows

## Sources

- [IBM Research & UC Berkeley - MAST Paper](https://arxiv.org/abs/2503.13657)
- [ITBench: Industry benchmark for IT automation agents](https://arxiv.org/pdf/2502.05352)
- [ITBench GitHub](https://github.com/itbench-hub/ITBench)
- [MAST GitHub](https://github.com/multi-agent-systems-failure-taxonomy/MAST)