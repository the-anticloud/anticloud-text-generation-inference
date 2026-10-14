# Educators — TEXT_GENERATION_INFERENCE

**Project:** TEXT_GENERATION_INFERENCE  
**Category:** FRONTIER_HARNESSES  
**Upstream:** see BENCH.json  
**Pinned commit:** `b4adbf2f6e2e721280bd0ea5f91d70f7d033f5ed`  
**Assurance:** 16/16 checks passing  
**Ledger head:** `4b9c159f7c6c956ea3641fd3caa7ec34abc6a73ae9d074b899fabda67008c457`  
**Date:** October 2026

## Teaching with TEXT_GENERATION_INFERENCE

The project is usable as a worked example of offline-first packaging with a
cryptographic audit chain. It ships with the assurance suite, the evidence
register and the ledger, so students can verify claims rather than take them on
faith.

## Suggested exercises

1. Run `python tools/run_bench.py --out BENCH.json` and read the 16 results.
2. Recompute the SHA3-256 of a row's evidence file and compare to the register.
3. Walk the AIOSS chain from genesis to head `4b9c159f7c6c956ea3641fd3caa7ec34abc6a73ae9d074b899fabda67008c457` and confirm every link.
4. Break one artifact and observe the chain fail to verify.

## Licence for teaching

Apache 2.0 terms apply to academic and teaching use. See `24_ANTICOMMONS_LICENSE`.
