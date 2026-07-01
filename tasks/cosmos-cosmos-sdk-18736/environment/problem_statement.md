## Description

The current multi-store commitment layer only produces single-level cryptographic proofs when querying data. These proofs can verify a key's membership within its individual store tree, but they cannot extend the verification chain up to the overall multi-store commit hash. As a result, it's impossible for external parties to do a full end-to-end proof of a key from the leaf level all the way to the final committed root — without doing additional manual steps that the API currently doesn't support.

Additionally, the commit store keeps no persistent record of which store hashes were committed at each version. This means that after a restart, or when trying to generate a proof for a historical version, the required metadata is unavailable and the proof cannot be constructed.

## Expected Behavior

- When querying with proof enabled, the result should include a two-level proof chain: one level proving the key within its store's Merkle tree, and a second level proving that store's root hash within the overall multi-store commitment. Both levels must be independently verifiable.
- Commit metadata (version number and per-store hashes) must be persisted to a database at each commit so it can be retrieved by version number at any later point.
- The commit store constructor must accept a database instance for persisting this metadata.
- The commit operation must accept an explicit version number, and individual tree commits must return the committed version number as part of their result.
- Commit info must be sorted consistently (lexicographically by store key) to ensure deterministic hash computation.
- A method to retrieve the commit info for any specific historical version must be available through the commitment interface.

## Why This Matters

Proof verification is essential for light clients and IBC relayers that need to confirm data exists on-chain without trusting a full node. Without a complete proof chain from leaf to root, these clients cannot independently verify responses. Persisting commit metadata also makes the system more reliable across restarts and enables auditable historical state.
