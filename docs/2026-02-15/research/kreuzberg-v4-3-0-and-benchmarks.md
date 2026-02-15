# Kreuzberg v4.3.0 and Benchmarks

| | |
|---|---|
| **Source** | r/LocalLLaMA |
| **URL** | [old.reddit.com/r/LocalLLaMA/comments/1r59th1/kreuzberg_v430_and_benchmarks/](https://old.reddit.com/r/LocalLLaMA/comments/1r59th1/kreuzberg_v430_and_benchmarks/) |
| **Researched** | 2026-02-15 |

## Overview

Kreuzberg v4.3.0 represents a significant update to an MIT-licensed, polyglot document intelligence framework written in Rust. The release introduces native PaddleOCR integration for Asian language support, eliminates LibreOffice as a system dependency, and ships with comprehensive comparative benchmarks demonstrating superior throughput and performance characteristics across multiple document formats.

## Key Points

- **Polyglot Framework**: Pure Rust implementation with bindings for Python, TypeScript/JavaScript (Node, Bun, WASM), Ruby, Java, Go, PHP, Elixir, and C#. Available as CLI, Docker image, REST API server, and MCP server.

- **Core Capability**: Extracts text, metadata, tables, and structured information from 75+ document and image formats. Enables OCR and data preparation for search, embeddings, and LLM pipelines.

- **PaddleOCR Integration**: Native Rust integration added in v4.3.0 supporting 6 languages—English, Chinese, Japanese, Korean, German, and French. Includes automatic model downloading and caching, critical for East Asian language workflows.

- **Dependency Reduction**: Removed LibreOffice as a system dependency by implementing native extraction for legacy formats (.doc, .ppt). Reduces installation footprint and simplifies containerized deployments.

- **Reproducible Benchmarks**: Interactive benchmarks compare Kreuzberg against Apache Tika, Docling, Unstructured, PDFPlumber, PyMuPDF4LLM, MarkItDown, and Mineru. All tests run in identical GitHub Actions environment on standardized Linux.

## Technical Details

**Performance Metrics Measured:**
- Throughput (documents/second across document types)
- Single-file latency and cold start times
- Batch processing throughput and parallelism characteristics
- Memory consumption and CPU utilization
- Tail latencies (p50, p95, p99)
- Success rates and extraction quality metrics

**Reported Performance Advantages:**
- Processing times in milliseconds rather than seconds for common formats (PDFs, DOCX, PPTX, HTML)
- Significantly higher throughput across document types
- Lower cold start times than most alternatives
- Smaller installation footprint compared to competing solutions

**Architecture Notes:**
- Pure Rust codebase with single optional system dependency: onnxruntime (for embeddings)
- Structured document data extraction capabilities added in v4.3.0
- Native handling of legacy MS Office formats eliminates need for LibreOffice, Pandoc, or external converters

**Language Support Expansion:**
- PaddleOCR backend now supports mixed-language documents (supports seamless language detection)
- Parallel processing capabilities for batch operations

## Implications

1. **LLM Pipeline Quality**: Document extraction quality directly impacts downstream LLM results. Kreuzberg's transparent, reproducible benchmarking establishes a baseline for practitioners evaluating ingestion layers.

2. **Deployment Simplification**: Eliminating external dependencies (LibreOffice) and offering containerized deployment options reduces DevOps complexity and system resource requirements—important for cost optimization.

3. **Asian Language Support**: Native PaddleOCR integration lowers barriers for teams building document AI systems for Chinese, Japanese, and Korean-language corpora. Eliminates need to maintain separate OCR pipelines.

4. **Benchmark Transparency**: The interactive, automatically-executed benchmark suite establishes what reproducible performance comparison should look like. This model contrasts with marketing claims and enables informed tool selection.

5. **Architectural Trade-offs**: The focus on reducing external dependencies (native .doc/.ppt parsing instead of LibreOffice) suggests optimization for containerized/serverless deployment over feature parity with older formats. Teams should verify format compatibility against their document corpus.

6. **Performance Engineering**: Processing documents in milliseconds rather than seconds enables real-time document ingestion pipelines and embedded document processing. Architectural choice (pure Rust, minimal dependencies) suggests memory efficiency and predictable latency profiles suitable for production workloads.

## Community Feedback

Discussion raised several practical considerations:
- One reviewer questioned benchmark credibility, noting discrepancies between reported metrics and real-world experience with competing tools (specifically flagging MarkItDown's reported 99.5% accuracy vs. pdfminer-based extraction)
- Interest in mixed-language document handling and configuration options
- Deployment feasibility questions for self-hosted scenarios (e.g., 100k pages on modest hardware)
- Requests for server requirement calculators and custom embedding endpoint support

The LICENSE confirmation is explicit: Kreuzberg remains MIT forever with no BSL (Business Source License) planned.

## Sources

- [r/LocalLLaMA Discussion: Kreuzberg v4.3.0 and benchmarks](https://old.reddit.com/r/LocalLLaMA/comments/1r59th1/kreuzberg_v430_and_benchmarks/) - Posted by Eastern-Surround7763, 2026-02-15
- [Kreuzberg Benchmarks](https://kreuzberg.dev/benchmarks) - Interactive benchmark results with raw data export
- [Kreuzberg GitHub Repository](https://github.com/kreuzberg-dev/kreuzberg) - Source code and CHANGELOG
- [Kreuzberg Changelog](https://github.com/kreuzberg-dev/kreuzberg/blob/main/CHANGELOG.md) - Detailed release history
