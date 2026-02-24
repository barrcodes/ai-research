# Exclusive: DeepSeek Trained on Nvidia's Best Chip Despite US Ban

| | |
|---|---|
| **Source** | Reuters |
| **URL** | [reuters.com/world/china/chinas-deepseek-trained-ai-model-nvidias-best-chip-despite-us-ban-official-says-2026-02-24](https://www.reuters.com/world/china/chinas-deepseek-trained-ai-model-nvidias-best-chip-despite-us-ban-official-says-2026-02-24/) |
| **Researched** | 2026-02-24 |

## Overview

Chinese AI startup DeepSeek trained its latest model on Nvidia's Blackwell processors—the company's most advanced AI chip—despite US export controls explicitly prohibiting such shipments to China. A Trump administration official confirmed the development, raising questions about enforcement of semiconductor export restrictions and the feasibility of maintaining technological barriers in AI infrastructure.

## Key Points

- **Hardware**: DeepSeek used Nvidia Blackwell chips, believed to be clustered at a data center in Inner Mongolia, China's autonomous region
- **Timeline**: The model was set to launch within days of the February 23 announcement, suggesting training occurred recently
- **Technical approach**: DeepSeek leveraged knowledge distillation from leading US AI models (Anthropic, Google, OpenAI, xAI) rather than building entirely from scratch
- **Obfuscation**: US officials believe DeepSeek will remove technical indicators revealing its use of American hardware before public release
- **Policy conflict**: White House AI Czar David Sacks and Nvidia CEO Jensen Huang argue unrestricted chip sales deter Chinese competitors from developing alternative architectures; China hawks counter this enables military applications and threatens US AI dominance

## Technical Details

The distillation approach represents an important architectural insight: DeepSeek achieves competitive model performance not through direct training on massive proprietary datasets, but by compressing knowledge from frontier models. This method reduces training compute requirements and chip count needed, making export control enforcement effectively impossible—a single cluster of Blackwells can produce models competitive with trillion-parameter systems trained on unrestricted infrastructure.

The Inner Mongolia data center location suggests deliberate use of geographic distribution to obscure the operation, with hardware specifications likely hidden in reported technical specifications.

## Implications

**For infrastructure architects**: Export controls on AI chips are technically difficult to enforce. Models trained via distillation achieve performance parity with vastly more expensive alternatives, shifting the competitive battleground from raw FLOPS to algorithmic efficiency and training data quality.

**For policy**: This incident exposes fundamental tensions in semiconductor restriction strategy. If advanced chips can be legally purchased for "civilian use" and then diverted, enforcement requires either:
1. Extreme restrictions on all chip exports (economically damaging)
2. Technical verification of hardware usage (logistically infeasible at scale)
3. Reliance on voluntary compliance by companies and third-party data center operators

**For model development**: DeepSeek's success with distillation demonstrates that frontier capability doesn't require the largest clusters. This has strategic implications for companies building competitive models under hardware constraints.

## Sources

- [Reuters: China's DeepSeek trained AI model on Nvidia's best chip despite US ban](https://www.reuters.com/world/china/chinas-deepseek-trained-ai-model-nvidias-best-chip-despite-us-ban-official-says-2026-02-24/)
- [TradingView News: DeepSeek trained on Nvidia's best chip despite US ban](https://www.tradingview.com/news/reuters.com,2026:newsml_L1N3ZK008:0-china-s-deepseek-trained-ai-model-on-nvidia-s-best-chip-despite-us-ban-official-says/)
- [Benzinga: US Claims China's DeepSeek Used Banned Nvidia Blackwell Chips](https://www.benzinga.com/markets/tech/26/02/50808520/us-claims-chinas-deepseek-used-banned-nvidia-blackwell-chips)
- [Investing.com: DeepSeek trained latest AI model on Nvidia Blackwell chips despite US ban](https://www.investing.com/news/stock-market-news/exclusivechinas-deepseek-trained-ai-model-on-nvidia-s-best-chip-despite-us-ban-official-says-4520307)
