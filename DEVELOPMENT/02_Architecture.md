# Technical Architecture — TEXT_GENERATION_INFERENCE

**Upstream:** [https://github.com/huggingface/text-generation-inference](https://github.com/huggingface/text-generation-inference)
**License:** Apache 2.0
**Category:** FRONTIER_HARNESSES
**Anticloud Integration:** PAX L5 Narrow L2 General 27B + AIOSS + Offline-First

## Upstream Description

TGI LLM serving

## Anticloud Architectural Changes

1. PAX L5 Narrow L2 General 27B local eval harness with no API key requirement
2. AIOSS tamper-evident benchmark result chain — reproducibility proof
3. AES-256 encryption for proprietary evaluation datasets
4. Single-binary eval runner with all benchmarks bundled locally
5. Zero-cloud: all scoring, logging, and reporting runs locally
6. GPU/CPU equalizer: eval runs on GPU or CPU with identical scoring
7. Open eval format: HELM/BIG-Bench compatible output schema
8. Offline leaderboard generator: produces publication-ready tables without API

## Integration Points

- **PAX Inference Socket:** Local HTTP endpoint at `127.0.0.1:11434/v1/chat` — same OpenAI-compatible API, zero cloud
- **AIOSS Hook:** Every write operation calls `aioss_append(event, payload)` before commit
- **Encryption Layer:** All file I/O routed through `anticloud_crypto.encrypt_at_rest()`
- **Single Binary Build:** `pyinstaller anticloud_text_generation_inference.spec` or `go build -o text_generation_inference`

## Deployment Modes

| Mode | Hardware | Notes |
| --- | --- | --- |
| Edge CPU | Raspberry Pi 4 / Intel NUC | Full feature set, PAX on CPU |
| Desktop GPU | RTX 3060 / A10 | PAX GPU inference, <1s latency |
| Server | 2× A100 | Full batch throughput |
| Air-gapped | Any x86/ARM | Zero network dependency |