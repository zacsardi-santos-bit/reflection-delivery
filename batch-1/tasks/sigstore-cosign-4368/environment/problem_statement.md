## Description

When using the signing configuration workflow (the go-based signing pipeline), only ephemeral keys were previously supported. If a user tried to pass their own existing key — for example, one stored in a hardware security module, a key management service, or as a file on disk — the tool would immediately fail with an error refusing to proceed. This prevented teams that manage their own long-lived keys from benefiting from the signing configuration workflow.

## Expected Behavior

- Users who already have a signing key (hardware token, KMS, or on-disk) should be able to sign blobs and create attestations using the signing configuration workflow, without being forced to use ephemeral keys.
- A new adapter type should be introduced in the internal key package that wraps any existing signer/verifier implementation so it can be used as a keypair in the signing pipeline.
- The adapter must correctly expose the key algorithm, a public key hint (derived from a hash of the public key), the hash algorithm, the PEM-encoded public key, and a signing method.
- The adapter must support ECDSA, RSA, and ED25519 key types; unsupported types should produce a descriptive error.
- Errors retrieving the public key must be propagated with context indicating the operation that failed.

## Why This Matters

Teams using key management services or hardware tokens for signing could not participate in the signing configuration workflow at all. Supporting self-managed keys here allows organizations with stricter key governance policies to adopt the newer signing format without abandoning their existing key infrastructure.
