# Students — TEXT_GENERATION_INFERENCE

**Project:** TEXT_GENERATION_INFERENCE  
**Category:** FRONTIER_HARNESSES  
**Upstream:** see BENCH.json  
**Pinned commit:** `b4adbf2f6e2e721280bd0ea5f91d70f7d033f5ed`  
**Assurance:** 16/16 checks passing  
**Ledger head:** `4b9c159f7c6c956ea3641fd3caa7ec34abc6a73ae9d074b899fabda67008c457`  
**Date:** October 2026

## What this project gives you

A complete worked example of offline-first packaging with a cryptographic audit
chain: source pinned at `b4adbf2f6e2e721280bd0ea5f91d70f7d033f5ed`, a 16-check assurance suite, an evidence register
with per-check hashes, and an AIOSS ledger chain ending at `4b9c159f7c6c956ea3641fd3caa7ec34abc6a73ae9d074b899fabda67008c457`.

## Learn by verifying

```
python tools/run_bench.py --out BENCH.json
```

Then take any row from `ISOLATED_LAB_RESULTS/03_Result_Register.md`, recompute
the SHA3-256 of its evidence file, and confirm it matches. If it does not match,
the record has been altered — that is the whole point of the chain.

## Licence

Apache 2.0 terms apply to study and teaching use.
