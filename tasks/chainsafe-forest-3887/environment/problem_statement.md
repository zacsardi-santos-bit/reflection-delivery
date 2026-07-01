## Description

The Forest node's beacon verification currently only supports the traditional chained variant used by the Filecoin mainnet drand network. In this variant, each beacon entry's message is derived from both the previous entry's signature and the current round number. However, there is a newer drand network configuration — sometimes called "quicknet" — that uses an unchained scheme: each entry is independently verifiable, with the signed message derived solely from the current round number (no dependency on any previous signature).

The two schemes also differ in how public keys and signatures are arranged across elliptic curve groups. The chained (mainnet) scheme places public keys on one curve group and signatures on another, while the unchained (quicknet) scheme reverses this arrangement. The existing code has no abstraction for either scheme — it just uses whatever the underlying BLS library provides by default, which happens to match the chained layout.

## Expected Behavior

- A new beacon signatures module should be introduced that clearly expresses both verification schemes
- The unchained variant should expose a new public key type (carrying the key on the appropriate curve group) with single-entry and batch verification methods
- The chained variant should expose a free function for verifying one or more entries given a public key, a list of messages, and a list of signatures
- Both message hashing helpers — one for unchained entries (hash just the round) and one for chained entries (hash the previous signature followed by the round) — should be accessible as methods on the existing beacon entry type
- All of these should be verified against real public keys and signatures from the live drand APIs

## Why This Matters

Without support for the unchained beacon scheme, Forest cannot participate in or validate chains that use the newer quicknet drand network. Adding proper abstractions for both variants makes it clear which curve groups and signature schemes are expected by each network type, reducing the risk of silent mismatches.
