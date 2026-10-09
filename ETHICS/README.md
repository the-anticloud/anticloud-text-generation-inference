# Ethics — TEXT_GENERATION_INFERENCE

**Project:** TEXT_GENERATION_INFERENCE  
**Category:** FRONTIER_HARNESSES  
**Upstream:** see BENCH.json  
**Pinned commit:** `b4adbf2f6e2e721280bd0ea5f91d70f7d033f5ed`  
**Assurance:** 16/16 checks passing  
**Ledger head:** `4b9c159f7c6c956ea3641fd3caa7ec34abc6a73ae9d074b899fabda67008c457`  
**Date:** October 2026

## Position

TEXT_GENERATION_INFERENCE is packaged for offline deployment with a verifiable audit trail. The
ethical questions this raises are answered by making the system's behaviour
checkable rather than by policy statements.

## The four commitments

1. **No hidden egress.** The deployment has no external API dependency; this is
   testable by running it with the network disconnected.
2. **Attributable output.** Every artifact is recorded in a hash chain, so what
   the system produced can be reconstructed.
3. **Operator control.** The institution owns the hardware and the keys.
4. **Refusal to overclaim.** Where a certification is not held, the project says
   so rather than implying it.

## Dual use

This project is packaged for civilian and public-sector deployment. Where an
upstream has dual-use characteristics, the licence gate and the reference-only
marking in `BENCH.json` record that.
