---
title: "Hey Lama" - Local AI Voice Assistant for Mac
source: GitHub (iBibek/hey-lama-local-ai-voice-assistant)
url: https://github.com/iBibek/hey-lama-local-ai-voice-assistant
researched_at: 2026-01-26T00:00:00Z
---

# "Hey Lama" - Local AI Voice Assistant for Mac

## Overview

A fully offline, local-first voice assistant for macOS that eliminates cloud dependencies entirely. The system implements a modular pipeline combining wake-word detection (Vosk), containerized speech-to-text (Parakeet-TDT), local LLM inference (Qwen, llama.cpp, or Ollama), and text-to-speech synthesis (KittenTTS) to enable always-listening capabilities with direct macOS system control via AppleScript.

## Key Points

- **Offline-first architecture**: No external APIs; all processing runs locally—wake-word detection, ASR, LLM inference, and TTS remain within system boundaries
- **Containerized ASR pipeline**: Speech recognition runs in Docker, exposing an OpenAI-compatible API for interoperability and resource isolation
- **Modular function system**: Command extensions defined through prompt engineering + Python method pairs in `function_tools.py`, avoiding hardcoded command routing
- **macOS native integration**: Leverages m-cli and AppleScript for system control (brightness, reminders, media playback, calendar)
- **Configurable LLM**: Supports Qwen, llama.cpp, and Ollama backends; user-selectable model sizing

## Technical Details

**Core Pipeline Flow**:
- Wake-word detection (Vosk) → ASR via Docker (Parakeet-TDT-0.6b) → LLM processing → TTS synthesis (KittenTTS) → Audio output

**Critical Dependencies**:
- Python 3.11 (type hints and features)
- PyAudio + PortAudio for bidirectional audio I/O
- webrtcvad (voice activity detection)
- pygame (audio playback)
- Docker (containerized ASR service at `http://127.0.0.1:5092`)

**Audio I/O Architecture**:
- Recording via sounddevice with continuous buffering
- Playback through pygame to handle concurrent system audio without interruption conflicts
- Voice activity detection prevents silent audio processing

## Implications

**Strengths**: Eliminates cloud privacy concerns and network latency. The modular function design scales cleanly—new capabilities require only prompt modification and corresponding Python function, reducing maintenance surface area.

**Trade-offs**: Acoustic echo cancellation is absent, making mid-playback interruption unreliable. Model response latency depends on local hardware (0.6B models are development-grade). Docker overhead adds operational complexity versus pure Python execution.

**When to adopt**: Ideal for privacy-conscious teams, offline-first workflows, or Mac-native automation. Tradeoff against cloud solutions: lower latency and privacy but higher hardware demands and limited multimodal reasoning.

**Architecture decision**: Containerizing ASR decouples speech processing from Python runtime, enabling independent scaling and model updates. This mirrors production microservice patterns but adds deployment complexity not justified for single-user applications.

## Related

- [Vosk offline speech recognition](https://alphacephei.com/vosk/) - Wake-word and offline ASR toolkit
- [llama.cpp quantization](https://github.com/ggerganov/llama.cpp) - Local LLM inference engine
- [Parakeet ASR models](https://nvidia.com/en-us/ai-data-center/products/nemo/parakeet/) - NVIDIA's open speech-to-text models
- [KittenTTS](https://github.com/Kipok/KittenTTS) - Lightweight text-to-speech synthesis
