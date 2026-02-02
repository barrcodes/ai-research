# I tracked GPU prices across 25 cloud providers and the price differences are insane

| | |
|---|---|
| **Source** | GPU Per Hour / getdeploying.com |
| **URL** | [gpuperhour.com](https://gpuperhour.com) |
| **Researched** | 2026-01-26 |

## Overview

Cloud GPU pricing exhibits a 2000x+ spread across providers, ranging from $0.03/hour for legacy GPUs to $68.80/hour for cutting-edge accelerators. Beyond headline rates, hidden costs—data egress ($0.08-$0.12/GB), storage, networking—inflate hyperscaler bills by 20-40%, creating opportunities for 40-70% cost savings through strategic provider selection and transparent pricing models.

## Key Points

- **Pricing Spread**: GPU hourly rates span $0.03-$68.80, with identical hardware (H100) varying 5x across providers ($1.45-$7.00+/hour)
- **Hyperscaler Tax**: AWS/GCP/Azure charge $4.00-$8.00/hour for H100; specialized providers (GMI Cloud, Novita) offer $1.45-$2.10/hour for same hardware
- **Hidden Costs Often Exceed Headline Rates**: Egress fees ($0.08-$0.12/GB), networking, and storage add 20-40% to monthly bills on hyperscaler platforms
- **Spot Instance Arbitrage**: Unused capacity sold at 60-90% discounts, though with interruption risk; effective for fault-tolerant workloads
- **Provider Specialization Matters**: Salad/Database Mart dominate budget tier; Novita/GMI Cloud lead mid-range; CoreWeave/Oracle premium cutting-edge

## Technical Details

### H100 Pricing Matrix (Representative Models)

| Provider | Price/Hour | Type | Notes |
|----------|-----------|------|-------|
| Novita | $1.45 | On-demand | Cheapest consistent H100 |
| GMI Cloud | $2.10 | On-demand | Transparent pricing, no egress fees |
| RunPod | $2.70 | On-demand | Community-driven, flexible |
| AWS/GCP/Azure | $4.00-$8.00 | On-demand | Hyperscaler premium tier |
| CoreWeave | $2.40 (PCIe) | Enterprise | SXM variants higher |

### Budget GPU Tier
- **Nvidia L4**: $0.39/hour (RunPod) - emerging training workhorse
- **Nvidia L40S**: $0.55/hour (Novita) - inference-optimized sweet spot
- **Nvidia A100 80GB**: $0.68-$4.00/hour (Vast.ai to AWS) - 6x spread for identical hardware

### Data Egress Economics
Hyperscalers charge $0.08-$0.12/GB egress. Moving 1TB model/outputs costs $80-$120—significant for batch inference and multi-provider ML pipelines. Specialized providers increasingly waive or negotiate egress.

## Implications

**For Cost-Conscious ML Teams**: Provider selection is a first-order optimization lever. Switching from hyperscaler H100 ($6/hr) to Novita ($1.45/hr) saves ~$4/hr per GPU—$35k+/month for a 300-GPU cluster. Audit all egress charges; they're frequently 20% of compute costs.

**For Architecture Decisions**:
1. **Workload-Provider Matching**: Use hyperscalers only where their network integration justifies premium (tight SageMaker/Vertex pipelines). Default to specialized providers for standalone training/inference.
2. **Spot/Interruption Tolerance**: Architect fault-tolerant distributed training to exploit 60-90% spot discounts; checkpoint frequently.
3. **Regional Arbitrage**: H100 pricing varies 3x geographically; multi-region orchestration adds complexity but unlocks significant savings at scale.
4. **Hidden Cost Audit**: Model-agnostic solution: measure actual cloud egress for your workload type, multiply by provider's rate—often reveals 30-40% total cost increases vs. headline rates.

**Operational**: Implement provider abstraction layer (e.g., Ray clusters or Kubernetes across multiple clouds) to dynamically route work to cheapest available GPU meeting performance SLAs. Spot-instance orchestrators (Nomad, Kubernetes autoscalers) make this tractable.

**When Hyperscalers Win**: Tight infrastructure-as-code integration, managed SageMaker/Vertex workflows, existing egress-heavy data pipelines with AWS/GCP, or sub-hourly burstable demand (where overhead of multi-provider switching exceeds savings).

## Related

- [GPU Per Hour](https://gpuperhour.com/) - Real-time pricing dashboard across 25+ providers
- [Silicon Data H100RT Index](https://spectrum.ieee.org/gpu-prices) - Daily spot rental price aggregate from 30+ sources
- [GMI Cloud 2025 GPU Pricing Guide](https://www.gmicloud.ai/blog/a-guide-to-2025-gpu-cloud-pricing-comparison) - Hyperscaler comparison with hidden cost analysis
- [Get Deploying GPU Comparison](https://getdeploying.com/gpus) - Comprehensive pricing matrix across budget, mid-range, enterprise tiers
- [Epoch AI GPU Price-Performance Trends](https://epoch.ai/blog/trends-in-gpu-price-performance) - Long-term price-performance curves (doubles every 2.5 years)
