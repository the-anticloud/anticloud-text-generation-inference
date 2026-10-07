# Command Line Interface — TEXT_GENERATION_INFERENCE

**Upstream:** https://github.com/huggingface/text-generation-inference

## Anticloud CLI

```bash
# Install
pip install anticloud-text-generation-inference

# Run offline with PAX inference
anticloud-text-generation-inference --offline --pax-local

# Run with AIOSS logging
anticloud-text-generation-inference --aioss-log ./ledger.jsonl

# Single binary (after build)
./text_generation_inference --config config.yaml
```

## Options

| Flag | Description |
| --- | --- |
| `--offline` | Disable all network calls |
| `--pax-local` | Use local PAX inference at 127.0.0.1:11434 |
| `--aioss-log PATH` | Write AIOSS audit chain to PATH |
| `--encrypt` | Enable AES-256 at rest for output files |
| `--gpu` | Force GPU inference |
| `--cpu` | Force CPU inference |
| `--config PATH` | Load configuration from YAML file |
