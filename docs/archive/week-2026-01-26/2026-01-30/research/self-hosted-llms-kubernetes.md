# How to Run Self-Hosted LLMs on Kubernetes

| | |
|---|---|
| **Source** | Oneuptime Blog |
| **URL** | [oneuptime.com/blog/post/2026-01-29-self-hosted-llms-on-kubernetes/view](https://oneuptime.com/blog/post/2026-01-29-self-hosted-llms-on-kubernetes/view) |
| **Researched** | 2026-01-30 |

## Overview

This guide presents a production-ready architecture for deploying self-hosted LLMs on Kubernetes, addressing GPU resource management, model serving framework selection, and operational scaling. The approach balances throughput optimization with resource constraints through strategic configuration of PagedAttention, continuous batching, and KEDA autoscaling.

## Key Points

- **GPU Resource Scheduling**: NVIDIA device plugin exposes GPUs as schedulable resources; time-slicing enables development cost reduction by multiplexing pods on single GPUs
- **Model Serving Framework Trade-offs**: vLLM prioritizes throughput via PagedAttention; Text Generation Inference and Ollama serve latency-sensitive workloads differently
- **Storage Pattern**: ReadWriteMany PVCs with job-based model downloading centralizes initial loading while enabling replica sharing
- **Sequence Length Tuning**: Reducing `max-model-len` from 8192 to 4096 tokens prevents OOM but sacrifices context window—critical decision point for workload requirements
- **Continuous Batching**: `--enable-chunked-prefill` flag dramatically increases request throughput versus sequential processing
- **Autoscaling Signal**: KEDA monitors `vllm_num_requests_waiting` queue depth, scaling replicas 1-5 based on actual demand
- **Service Exposure**: Internal ClusterIP for cluster access; Ingress with extended timeouts (10+ minutes) for long inference requests

## Technical Details

| Consideration | Configuration | Trade-off |
|---|---|---|
| GPU Memory | Target 90% utilization | Maximum throughput vs. stability margin |
| Context Window | 4096 vs. 8192 tokens | OOM prevention vs. context capability |
| Request Processing | Chunked prefill enabled | Higher throughput vs. latency variance |
| Autoscaling | KEDA queue-based | Dynamic resource allocation vs. cold start penalty |
| Shared Memory | 8GB emptyDir | PyTorch multiprocessing support |

**Health Check Configuration**: 60-second initial delays accommodate model loading times before serving traffic.

**Rate Limiting & Security**: 10 requests/second per IP; network policies restrict access to labeled clients; resource quotas cap GPU consumption at 8 GPUs cluster-wide.

## Implications

**For Capacity Planning**: GPU time-slicing during development significantly reduces infrastructure spend—schedule non-production workloads on shared nodes. Track `vllm_num_requests_waiting` as leading indicator for scaling decisions before queue buildup causes user-facing latency.

**For Model Selection**: Choice between vLLM (throughput), TGI (specialized inference), and Ollama (simple deployments) depends on SLA requirements. Sequence length tuning is non-trivial—measure actual workload distribution before aggressive reduction.

**For Operational Stability**: Extended Ingress timeouts are non-negotiable for long-running inference; 60-second health check delays prevent cascading failures during model loading. Resource quotas protect cluster from runaway GPU allocation.

**Integration Point**: Prometheus metrics enable tight feedback loops—monitor token/second generation rate and GPU cache hit ratios to validate PagedAttention effectiveness and identify scaling inflection points.

## Related

- [vLLM Documentation](https://docs.vllm.ai/) - Framework selection guide and performance tuning
- [NVIDIA GPU Operator](https://nvidia.github.io/gpu-operator/) - GPU resource plugin reference
- [KEDA Documentation](https://keda.sh/) - Queue-based autoscaling patterns
