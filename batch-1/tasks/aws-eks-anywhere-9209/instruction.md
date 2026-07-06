Implement cryptographic signature verification for EKS Anywhere bundle manifests to ensure their integrity. Create a package to verify bundle signatures against a known public key, and integrate this verification into cluster operations.

*   Implement the `ValidateSignature` function in `pkg/signature/manifest.go`:
    *   Accept a `Bundles` object and a base64-encoded PKIX public key string.
    *   Return `(false, error)` with 'missing signature annotation' if the bundle lacks the `SignatureAnnotation`.
    *   Return descriptive errors for malformed base64 signatures, invalid key formats, or non-PKIX public keys.
    *   Return `(false, nil)` if the signature does not match the bundle content.
    *   Return `(true, nil)` if the signature is valid and matches the bundle content.

*   Implement the `getDigest` function in `pkg/signature/manifest.go`:
    *   Accept a `Bundles` object and return a SHA-256 digest and filtered JSON bytes.
    *   On success, return a non-zero digest and non-empty filtered bytes.
    *   On error, return a zero digest and nil bytes, with error wrapping as "filtering excluded fields: <inner error>".

*   Implement the `filterExcludes` function in `pkg/signature/manifest.go`:
    *   Accept JSON bytes and remove fields listed in `AlwaysExcludedFields` and paths from `Excludes`.
    *   Return errors for malformed JSON or gojq execution errors.
    *   Ensure removal of at least `.status`, `.metadata.creationTimestamp`, `.metadata.annotations`, and `.spec.versionsBundles[].eksa`.

*   Implement the `ValidateExtendedK8sVersionSupport` function in `pkg/validations/extendedversion.go`:
    *   Accept `(context.Context, *Cluster, *Bundles, kubernetes.Client)`.
    *   Return an error if the bundle's signature is missing or invalid.
    *   Return nil if the bundle's signature is valid.

*   Define constants in `pkg/constants/constants.go`:
    *   `SignatureAnnotation`: "anywhere.eks.amazonaws.com/signature".
    *   `KMSPublicKey`: "MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAEFZU/Z6VVMU9HioT7rGkPdJg3frC2xyQZhWFIrz5HeZEfeQ2nAdnJMLrs2Qr3V9xVrJrHA54wnIHDoPGbEhojqg==".
    *   `Excludes`: base64-encoded list of jq paths to exclude.
    *   `AlwaysExcludedFields`: `[]string` with paths like ".status", ".metadata.creationTimestamp", ".metadata.annotations".

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.