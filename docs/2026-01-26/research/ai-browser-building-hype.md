---
title: When AI 'builds a browser,' check the repo before believing the hype
source: The Register
url: https://www.theregister.com/2026/01/26/cursor_opinion/
researched_at: 2026-01-26T00:00:00Z
---

# When AI 'builds a browser,' check the repo before believing the hype

## Overview

Cursor's CEO claimed their AI built a functional web browser ("3M+ lines of code in Rust"), but independent verification revealed the repository barely compiles, runs with abysmal performance (page loads taking ~60 seconds), and relies on pre-existing dependencies contradicting "from-scratch" claims. The experiment cost millions in tokens while producing largely non-functional output—exemplifying a critical gap between code generation volume and shipped, working software.

## Key Points

- **Compilation Failure**: Recent commits don't compile cleanly; GitHub Actions runs show persistent build errors requiring manual patching
- **Performance Catastrophe**: When builds succeeded, page load times reached ~60 seconds—unsuitable for any real-world use case
- **Misleading Attribution**: Core components (JavaScript engine) relied on Servo and QuickJS, contradicting claims of original development from scratch
- **Cost-to-Output Ratio**: Estimated 10-20 trillion tokens consumed over one week (several million dollars) for largely unusable code
- **Architecture Problems**: Maintainers described the codebase as "a tangle of spaghetti...uniquely bad design that could never support real-world web engine requirements"

## Technical Details

The fundamental issue isn't token count—AI generated substantial volume—but rather the absence of:

| Requirement | Status |
|---|---|
| Clean compilation | Failed |
| Reproducible builds | Not demonstrated |
| CI/CD validation | Failing |
| Performance benchmarks | Abysmal (60s+ page loads) |
| Architectural coherence | Poor (spaghetti design) |
| Dependency honesty | Misrepresented |

The experiment's scale (10-20 trillion tokens) masks its core failure: generating code isn't equivalent to engineering systems that compile, run reliably, and perform acceptably under real constraints.

## Implications

**For practitioners evaluating AI coding tools:**

1. **Verify before trusting claims** - Check public repositories, build status, and performance benchmarks rather than relying on marketing narratives
2. **Code volume ≠ shipping quality** - Millions of lines mean nothing without compilation success, test coverage, reproducible builds, and acceptable performance
3. **Watch for misattribution** - "Built from scratch" claims often hide dependency reuse; verify architectural decisions and component origins
4. **Cost awareness** - Massive token expenditure producing non-functional output signals fundamental gaps in AI's ability to navigate complex system constraints (memory, performance, architectural coherence)
5. **CI/CD is non-negotiable** - Real systems need passing automated builds, not aspirational code that "kind of works" with manual intervention

The hype-reality gap here reveals that AI excels at generating plausible-looking code syntax but struggles with the systemic constraints that separate prototype code from production systems: dependency management, performance under load, architectural consistency, and reproducible builds.

## Related

- GitHub repository mentioned in article - verify build status and test results before drawing conclusions
- Cursor's official claims about AI agent capabilities - cross-reference with independent verification
- Previous AI-generated code quality assessments - similar pattern of compilation/runtime failures
