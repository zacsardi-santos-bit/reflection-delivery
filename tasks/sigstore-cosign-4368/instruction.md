Implement support for self-managed signing keys in the signing configuration workflow by creating a new adapter in the internal key package. This adapter should wrap any existing signer/verifier implementation, allowing it to be used as a keypair in the signing pipeline. Ensure that the adapter supports ECDSA, RSA, and ED25519 key types and handles errors appropriately.

*   Implement the `NewSignerVerifierKeypair` function in `internal/key/svkeypair.go`:
    *   Accept a `signature.SignerVerifier` and an optional pointer to a slice of load options.
    *   Return a non-nil `*SignerVerifierKeypair` with no error for ECDSA, RSA, or ED25519 keys.
    *   Return an error with the message pattern 'getting public key: <original error message>' if `PublicKey()` returns an error.
    *   Return an error containing 'unsupported public key type' if the key type is not supported.

*   Implement the `SignerVerifierKeypair` struct in `internal/key/svkeypair.go`:
    *   Method `GetHashAlgorithm()`:
        *   Return `protocommon.HashAlgorithm_SHA2_256` for ECDSA P-256 keys.
    *   Method `GetHint()`:
        *   Return a byte slice with the base64-encoded SHA-256 hash of the DER (PKIX) encoded public key.
    *   Method `GetKeyAlgorithm()`:
        *   Return 'ECDSA' for ECDSA keys, 'RSA' for RSA keys, and 'ED25519' for ED25519 keys.
    *   Method `GetPublicKeyPem()`:
        *   Return a valid PEM-encoded public key string that can be converted back to the original public key.
        *   Return an error if the key cannot be marshalled.
    *   Method `SignData(ctx, data)`:
        *   Return three values: `signature []byte`, `digest []byte`, `error`.
        *   Compute the digest as the SHA-256 hash of `data`.
        *   Generate the signature using the underlying `SignerVerifier`'s `SignMessage` method.
        *   Return the exact error from `SignMessage` if it fails, without modification.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.