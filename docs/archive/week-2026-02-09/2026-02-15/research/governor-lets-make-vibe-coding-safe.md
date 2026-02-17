# Governor: Let's Make Vibe Coding Safe

| | |
|---|---|
| **Source** | GitHub (ulsc/governor) |
| **URL** | [github.com/ulsc/governor](https://github.com/ulsc/governor) |
| **Researched** | 2026-02-15 |

## Overview

Governor is an extensible CLI for security auditing AI-generated code that treats this problem differently from traditional code review. It provides repeatable, machine-readable security assessments through a hybrid architecture combining AI-powered analysis with deterministic rule-based checks, enabling organizations to scale security policies across multiple AI-assisted projects.

## Key Points

- **Hybrid check engine**: Combines AI-powered analysis with deterministic rule-based checks in a single framework, recognizing that different vulnerability patterns require different detection methods
- **Staged workspace isolation**: Constrains input through allow-lists, file-count/size limits, and staged processing before any checks execute, reducing attack surface
- **Process and container hardening**: Worker processes run with bounded concurrency, restricted environment variables, and optional containerized execution with read-only filesystems and capability restrictions
- **Operationalizable security policies**: Extractors convert internal security documents into executable checks, addressing the practical gap between security teams' knowledge and deployment
- **Machine-readable output**: Generates results in markdown, JSON, and HTML for integration with CI/CD and reporting pipelines

## Technical Details

**Architecture**: Governor uses a modular worker-based design where checks run in isolated subprocesses with configurable concurrency. AI checks use binary attestation—CLI tools are only resolved and verified when selected. Per-check timeouts (default 4 minutes) prevent resource exhaustion.

**Containerized mode**: When enabled, provides read-only root filesystem, restricted Linux capabilities, minimal auth bundles mounted read-only, and network isolation. Workspace constraints apply across all modes (configurable file limits and byte thresholds).

**Input handling**: Accepts folders or ZIP files with extensible allow-list filtering. Staged workspace model ensures content is constrained before worker execution begins, separating staging from analysis phases.

## Implications

Governor addresses a real architectural gap: AI-generated code introduces different risk profiles than human-written code (broader code surface, potential encoding attacks, LLM hallucinations). For engineering leaders, this enables:

- **Policy as code**: Security teams encode institutional knowledge once, reuse across projects
- **CI/CD integration**: Machine-readable output enables automated gates in deployment pipelines
- **Scalable auditing**: Move from manual review bottleneck to repeatable, containerized scanning

**Trade-offs**: The framework requires upfront investment in defining checks and policies. AI-powered analysis depends on LLM availability and quality. Organizations should evaluate whether rule-based checks alone suffice before adopting the full hybrid approach.

**Alternatives**: Traditional SAST tools (Semgrep, CodeQL) work on generated code but don't address AI-specific vulnerabilities. Governor complements rather than replaces existing security scanning.

## Sources

- [ulsc/governor](https://github.com/ulsc/governor) - Extensible security auditing CLI for AI-generated code
