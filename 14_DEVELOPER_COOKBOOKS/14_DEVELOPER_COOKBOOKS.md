# Developer Cookbooks — TEXT_GENERATION_INFERENCE

**Project:** `TEXT_GENERATION_INFERENCE`
**Category:** FRONTIER_HARNESSES
**Domain:** frontier AI harnesses and inference
**Date:** 2026-10-07

---

## Common Tasks

### Adding a New Feature
1. Create a feature branch
2. Write tests first (TDD)
3. Implement the feature
4. Run `python tools/run_bench.py`
5. Submit a pull request

### Debugging
```bash
python -m pdb src/text_generation_inference/main.py
```

### Profiling
```bash
python -m cProfile -o profile.out src/text_generation_inference/main.py
```

### Security Scanning
```bash
python tools/run_bench.py --only owasp
```

## Code Patterns

### Error Handling
All errors are logged to the AIOSS chain with full context.

### Configuration
Configuration is loaded from environment variables with sensible defaults.

### Testing
Tests use pytest with coverage reporting. Target: >80% coverage.

## Verification

All 16 checks PASS. Evidence: `ISOLATED_LAB_RESULTS/03_Result_Register.md`.
