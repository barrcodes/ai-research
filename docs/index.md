# AI Research Articles

## Current Week

### [2026-02-13](./2026-02-13/index.md)

Frontier model economics shift from capability races to latency optimization: Anthropic's $30B Series G valuation ($2.5B Claude Code revenue, 4% GitHub commits) and OpenAI's Cerebras partnership both signal custom silicon and inference efficiency now compete with raw parameter scaling. Agent reliability proves infrastructure-bound: OpenEnv reveals 90% accuracy drops to 40% under ambiguity, with 50%+ of failures occurring post-tool-selection from argument malformation—not model limitations. Inference-time compute scaling (Gemini 3 Deep Think: 90% IMO accuracy) potentially outperforms pre-training efficiency. Production agents operate at 2004 security maturity with 73%+ exposed to prompt injection; defense-in-depth now mandatory. Custom kernel generation viable for narrow domains (1.88x speedups) but end-to-end impact modest (1.06x). Governance patterns stabilizing: multi-agent Review-and-Critique becomes standard for high-stakes content. 6 sources, 11 articles researched.

### [2026-02-12](./2026-02-12/index.md)

Frontier models drive agentic architecture: Gemini Deep Think solves research problems through structured verification loops; GLM-5 (744B, 40B active) achieves open-weight parity with frontier models. Agent engineering flips the constraint: repository design and harness architecture matter more than model capability—the hashline format improves weak models 10x without retraining. Optimization breakthroughs show parallel decoding (DTS) beats longer-chain-of-thought assumptions, while sparse attention (HySparse) achieves 10x KV cache reduction via oracle guidance. Infrastructure becomes competitive moat: power grid constraints outpace semiconductor growth; Anthropic absorbs grid externalities while most competitors face cost passthrough. 11 sources, 20 articles researched.

### [2026-02-11](./2026-02-11/index.md)

Agentic middleware consolidates: Entire ($60M seed) and OpenAI Frontier both address agent fleet management rather than point solutions, while Apple Xcode integrates Claude Agent SDK natively via MCP. GPT-5.3-Codex and Claude Opus 4.6 lead frontier coding benchmarks with distinct architectures—Codex emphasizes interactive steering during execution while Opus offers graduated adaptive reasoning. GLM-5 (745B MoE on Huawei Ascend) and MiniMax M2.1 ($19/month Agent Desktop) demonstrate cost/sovereignty alternatives. Security incidents dominate: OpenClaw's 42K exposed instances with 341 malicious skills prompted AgentVault emergency response; prompt injection enables API key theft via attacker-supplied credentials. MCP token optimization patterns mature—dynamic tool discovery achieves 75-95% reduction from static initialization. Evaluation rigor challenged: agentic eval variance of 2.2-6.0 percentage points invalidates most claimed 2-3 point improvements. SAE steering fails catastrophically for structured output (86.8% → 24.4% valid JSON), validating constrained decoding for syntax control. 11 sources, 32 articles researched.

### [2026-02-09](./2026-02-09/index.md)

Frontier models solidify agentic orchestration: Claude Opus 4.6's agent teams enable parallel multi-agent workflows natively, while SAE steering limitations clarify that structured output requires constrained decoding—not semantic control. AIRS-Bench emerges as the first genuine autonomy benchmark, measuring end-to-end research lifecycle capability (4/20 tasks solved across frontier models). Security surfaces proliferate: prompt injection in messaging apps exploits automatic URL preview to exfiltrate agent data; enterprise agent deployments show 58% visibility but only 37% actual control—a critical governance-containment gap. Terminology crisis around "async agents" exposes definitional fragmentation across orchestration models. Sparse MoE models (Qwen3, Step-3.5-Flash, GLM-5, LLaDA2.0) compress toward frontier capability with 6-10x efficiency gains via active-parameter optimization. Video synthesis quality reaches 95-98% accuracy (MOS 3.5-4.2) but full-deepfake detectability remains, limiting high-assurance applications. Medical AI benchmarks decouple from real-world performance (94% exam scores → 33% diagnostic accuracy), highlighting verification bottleneck. Local LLM deployment economics improve with linear attention (Kimi-Linear-48B) achieving 6x throughput on million-token contexts. 10 sources, 44 articles identified, 28 researched.

---

## Archived Weeks

- [Week of 2026-02-02](./archive/week-2026-02-02/week-2026-02-02.md)
- [Week of 2026-01-26](./archive/week-2026-01-26/week-2026-01-26.md)
