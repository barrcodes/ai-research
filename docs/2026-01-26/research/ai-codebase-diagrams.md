---
title: I built this to turn AI-generated codebases into interactive diagrams (D2 + overlay)
source: Reddit r/ClaudeAI / GitHub (Noodles)
url: https://github.com/unslop-xyz/noodles
researched_at: 2026-01-26T00:00:00Z
fetch_method: web-search
---

# Building Interactive Diagrams for AI-Generated Codebases with D2

## Overview

Noodles is a tool that solves a critical problem: understanding AI-generated code. Instead of reading every line, it analyzes your codebase, identifies entry points, generates D2 diagrams showing code flow, and renders an interactive overlay for exploration. The project leverages Claude AI to understand code architecture and D2's ELK layout engine for publication-quality diagrams.

## Key Points

- **Problem Statement**: AI-generated codebases are difficult to understand without reading every line of code
- **Solution**: Automated diagram generation that visualizes how code actually flows from entry to outcome
- **D2 Integration**: Uses D2 as the diagram format, leveraging its superior layout engine and export capabilities
- **Interactive Overlay**: Renders diagrams with clickable interactive overlay for exploring code structure
- **Entry Point Detection**: Uses LLMs (particularly Claude/OpenAI) to identify user-facing entry points (CLI commands, routes, UI components)
- **Multi-format Export**: D2 supports SVG, PNG, PDF, and PowerPoint export formats

## Technical Details

### Architecture and Workflow

The Noodles pipeline consists of several distinct phases:

1. **Manifest Generation**: Scans the codebase folder and builds a manifest of the code structure
2. **Entry Point Identification**: Uses Claude/OpenAI API to analyze code and identify user-facing entry points:
   - CLI commands and arguments
   - HTTP routes and endpoints
   - UI components and page boundaries
3. **Diagram Generation**: Creates D2 diagrams showing code flow from entry point through to outcome
4. **Interactive Rendering**: Generates an interactive overlay allowing users to click components and explore relationships

### D2 Advantages

- **Layout Engine**: Uses ELK (Eclipse Layout Kernel) for aesthetic, readable diagrams
- **LLM Familiarity**: GPT-4o, Claude, and Gemini all have strong knowledge of D2's text-based syntax
- **Format Support**: Exports to SVG, PNG, PDF, PowerPoint
- **Configuration**: Can integrate with D2 binary and environment variables for customization

### Configuration and Integration

- CLI can point to D2 binary location
- Configuration available through environment variables
- Programmatic integration with D2 for diagram generation and rendering

## Implications

### For Architecture and Design

1. **Rapid Codebase Understanding**: Teams can immediately visualize AI-generated code structure without extensive manual documentation
2. **Entry Point Focus**: By identifying entry points first, diagrams focus on user-visible functionality rather than implementation details
3. **Living Documentation**: Potential for automated updates as codebases change, similar to Eraserbot's approach

### For Development Workflow

1. **Onboarding Acceleration**: New team members can understand codebase topology in minutes rather than days
2. **Code Review Enhancement**: Architects can review code flow visually before diving into implementation
3. **Technical Debt Visibility**: Interaction patterns and dependencies become visible, highlighting complexity hotspots

### For AI-Generated Code Specifically

1. **Trust and Verification**: Visual representation allows developers to verify AI output makes architectural sense
2. **Quality Assessment**: Odd patterns or disconnected components reveal potential AI hallucinations
3. **Refactoring Targets**: Overly complex code flows become obvious candidates for improvement

### Architectural Trade-offs

- **Accuracy vs. Simplicity**: Showing all code flows vs. focusing on entry points (Noodles chooses entry-point focus)
- **Static vs. Dynamic**: Generated from static analysis vs. runtime behavior (static approach is faster but may miss dynamic patterns)
- **Real-time Updates**: Keeping diagrams synchronized with code changes (requires tooling integration)

## Related Resources

- [GitHub - unslop-xyz/noodles](https://github.com/unslop-xyz/noodles) - Original project repository
- [Show HN: Instantly visualize any codebase as an interactive diagram](https://news.ycombinator.com/item?id=42521769) - Hacker News discussion
- [Home | D2 Documentation](https://d2lang.com/) - D2 diagram language documentation
- [Diagrams as Code: Supercharged by AI Assistants](https://simmering.dev/blog/diagrams/) - Paul Simmering on AI-assisted diagrams
- [Transform Your Codebase to Intuitive Diagrams in a Few Seconds](https://newsletter.systemdesigncodex.com/p/transform-your-codebase-to-intuitive) - System Design Codex newsletter

## Competitive Landscape

### Similar Solutions

- **GitDiagram**: Transforms GitHub repos into interactive system architecture diagrams using Claude 3.5 Sonnet, with clickable components linking to files
- **Swark**: Automatically creates architecture diagrams using LLMs, integrates with GitHub Copilot
- **Eraserbot**: AI agent generating and maintaining multiple diagram types (system architecture, sequences, ERDs, cloud architecture)

### Key Differentiators for Noodles

- D2 format provides superior aesthetics and LLM compatibility
- Entry-point focused approach reduces diagram complexity
- Interactive overlay bridges visual and code navigation
- Purpose-built for AI-generated code comprehension
