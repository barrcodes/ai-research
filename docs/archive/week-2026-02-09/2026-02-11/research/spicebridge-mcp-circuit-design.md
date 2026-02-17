# SPICEBridge: MCP Server for AI Circuit Design via ngspice

| | |
|---|---|
| **Source** | GitHub (clanker-lover) |
| **URL** | [github.com/clanker-lover/spicebridge](https://github.com/clanker-lover/spicebridge) |
| **Researched** | 2026-02-11 |

## Overview

SPICEBridge is an MCP (Model Context Protocol) server that exposes ngspice circuit simulation as composable tools for language models. It enables end-to-end circuit design through natural language—from specification through netlist generation, simulation, measurement extraction, and design verification—without manual EDA tool interaction.

## Key Points

- **28 specialized tools** organized into functional pipelines: circuit templates, simulation execution, measurement extraction, design verification, multi-stage composition, and advanced analysis (Monte Carlo, worst-case tolerancing)
- **11 pre-built circuit templates** with automated component value calculation, eliminating manual netlist authoring
- **Full simulation stack**: AC sweeps, transient analysis, DC operating points, and tolerance analysis all accessible as discrete tool calls
- **Measurable outputs**: Tools extract meaningful metrics (gain, phase, cutoff frequency) rather than raw simulation data, enabling LLM-driven evaluation against specifications
- **Design iteration**: Agents can iteratively refine component values and verify against target parameters within a single conversation
- **KiCad/web export**: Generated designs export to schematics and interactive visualizations for downstream manufacturing

## Technical Details

The architecture decouples ngspice execution from the MCP interface. Each circuit task becomes a stateless tool call with measurable inputs/outputs, enabling agentic loops:

1. **Specification parsing** → Template selection (via LLM semantic matching)
2. **Parameter calculation** → Component value derivation from frequency, impedance, or other constraints
3. **Netlist synthesis** → Automatic multi-stage composition with port mapping
4. **Simulation execution** → Streaming results for cloud deployments via HTTP
5. **Measurement extraction** → Computed metrics vs. raw SPICE output
6. **Verification** → Automated pass/fail against design specs

Requirements: Python 3.12+, ngspice binary. Deployable locally or cloud via HTTP streaming for long-running simulations.

## Implications

**For agentic workflows**: This patterns the ideal MCP design—domain expertise (circuit simulation) exposed through high-level abstractions (design templates, computed metrics) that let LLMs reason semantically rather than syntactically. Agents don't need SPICE netlist grammar knowledge; they work in design space (target filter frequency, passband ripple).

**Architectural significance**: Demonstrates how to bridge specialized engineering tools with AI. The 28-tool taxonomy is neither a leaky abstraction (raw SPICE commands) nor over-simplified (one "design filter" call). It exposes meaningful intermediate steps—simulation, measurement, composition—so agents can fail gracefully and iterate.

**When to adopt**: Rapid prototyping, educational electronics instruction, design automation workflows, hardware-in-the-loop AI coding. Not suitable for production analog design requiring design rule checking, layout, electromagnetic simulation, or manufacturing process correlation.

**Trade-offs**: Ngspice is behavioral-only; layout parasitic extraction, crosstalk, and thermal effects are out of scope. Template-centric design constrains topology freedom but ensures convergence. Component libraries depend on ngspice SPICE models' accuracy.

## Sources

- [SPICEBridge GitHub Repository](https://github.com/clanker-lover/spicebridge) - MCP server implementation and tool definitions
