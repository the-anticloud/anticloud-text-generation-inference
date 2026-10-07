# Build and Test

**Project:** `TEXT_GENERATION_INFERENCE`
**Upstream:** https://github.com/huggingface/text-generation-inference
**License:** Apache 2.0

## Quick Start

```bash
git clone https://github.com/huggingface/text-generation-inference
cd text-generation-inference
pip install -r requirements-anticloud.txt
python anticloud_main.py --offline --pax-local
```

## Anticloud Improvements Applied

1. PAX L5 Narrow L2 General 27B local eval harness with no API key requirement
2. AIOSS tamper-evident benchmark result chain — reproducibility proof
3. AES-256 encryption for proprietary evaluation datasets
4. Single-binary eval runner with all benchmarks bundled locally
5. Zero-cloud: all scoring, logging, and reporting runs locally
6. GPU/CPU equalizer: eval runs on GPU or CPU with identical scoring
7. Open eval format: HELM/BIG-Bench compatible output schema
8. Offline leaderboard generator: produces publication-ready tables without API

## Benchmark Targets

| Metric | Target |
| --- | --- |
| Latency | Primary inference task: <5s on CPU, <1s on GPU |
| Throughput | Batch processing: >100 items/hour on single CPU server |
| Memory | <8GB RAM for standard deployment |
| Accuracy | Task-specific accuracy within 5% of cloud-API baseline |

## Build Status

Not yet measured. Run verified build and record actual figures above.
