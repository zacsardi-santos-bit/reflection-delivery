Implement a new beacon signatures module to support both chained and unchained beacon verification schemes for a Rust blockchain node. Ensure the module clearly separates the two schemes and provides necessary abstractions for verification.

*   Create a new public signatures submodule at `src/beacon/signatures/mod.rs` and declare it in `src/beacon/mod.rs`.
*   Re-export `PublicKeyOnG1` and `SignatureOnG2` as type aliases from the `bls-signatures` crate.
    *   `PublicKeyOnG1` should be a re-export of `bls_signatures::PublicKey`.
    *   `SignatureOnG2` should be a re-export of `bls_signatures::Signature`, supporting deserialization with `from_bytes` and serialization with `as_bytes`.
*   Implement `PublicKeyOnG2` struct for unchained verification.
    *   Implement `PublicKeyOnG2::from_bytes(raw: &[u8]) -> Result<Self, bls_signatures::Error>` for deserialization.
    *   Implement `PublicKeyOnG2::verify(&self, message: impl AsRef<[u8]>, signature: &SignatureOnG1) -> bool` for single signature verification.
    *   Implement `PublicKeyOnG2::verify_batch(&self, messages: &[&[u8]], signatures: &[&SignatureOnG1]) -> bool` for batch verification.
*   Implement `SignatureOnG1` struct for unchained signatures.
    *   Implement `SignatureOnG1::from_bytes(raw: &[u8]) -> Result<Self, bls_signatures::Error>` for deserialization.
*   Implement `verify_messages_chained` function for chained verification.
    *   Signature: `verify_messages_chained(public_key: &PublicKeyOnG1, messages: &[&[u8]], signatures: &[SignatureOnG2]) -> bool`.
    *   Ensure it returns true if all pairs are valid, false otherwise.
*   Use appropriate ciphersuites for signature verification:
    *   Unchained: `BLS_SIG_BLS12381G1_XMD:SHA-256_SSWU_RO_NUL_`.
    *   Chained: `BLS_SIG_BLS12381G2_XMD:SHA-256_SSWU_RO_NUL_`.
*   Add methods to `BeaconEntry` in `src/beacon/beacon_entries.rs`.
    *   `BeaconEntry::message_unchained(round: u64) -> impl AsRef<[u8]>`: Compute SHA-256 of the round number as an 8-byte big-endian integer.
    *   `BeaconEntry::message_chained(round: u64, prev_signature: impl AsRef<[u8]>) -> impl AsRef<[u8]>`: Compute SHA-256 of the previous signature concatenated with the round number as an 8-byte big-endian integer.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.