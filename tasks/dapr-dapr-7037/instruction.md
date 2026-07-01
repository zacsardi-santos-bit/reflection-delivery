Remove legacy backwards-compatibility mechanisms from the Dapr security system. Eliminate unnecessary certificate pre-signing, identity override privileges, and outdated identity formats. Ensure all changes comply with the updated SPIFFE-based mTLS model.

*   Remove certificate pre-signing:
    *   The sidecar container must not include `DAPR_CERT_CHAIN`, `DAPR_CERT_KEY`, or `SENTRY_LOCAL_IDENTITY` environment variables.
    *   The `SidecarConfig` struct must not have `CertChain` or `CertKey` fields.
    *   The injector service must not sign or pre-fetch certificates for pods.
    *   Remove the `signDaprdCertificate` function type and field from the injector struct.
    *   Update `Injector.Run` method to exclude the certificate-signing function parameter.

*   Update identity validation:
    *   The `Validate` function in `pkg/sentry/server/validator/internal/common.go` must return `(spiffeid.TrustDomain, error)`.
    *   The `Validate` method in `pkg/sentry/server/validator/kubernetes/kubernetes.go` must return `(spiffeid.TrustDomain, error)`.
    *   Remove special privileges for the injector to request certificates for arbitrary identities.
    *   Reject certificate signing requests using the legacy colon-separated identity format with a gRPC `PermissionDenied` error.
    *   Reject app IDs longer than 64 characters with an error message 'app ID must be 64 characters or less' and a gRPC `PermissionDenied` status code.

*   Modify certificate signing:
    *   The `SignIdentity` method on the `Signer` interface in `pkg/sentry/server/ca/ca.go` must accept `(context.Context, *SignRequest)` without an override bool parameter.
    *   Remove DNS Subject Alternative Names from signed workload and issuer certificates.
    *   When signing certificates for the dapr-operator, include only the service DNS name in the DNS list.

*   Update interfaces and tests:
    *   Add `WithControlPlaneTrustDomain(trustDomain string)` function in `tests/integration/framework/process/daprd/options.go`.
    *   Update `kubeAPI` function in `tests/integration/suite/sentry/validator/kubernetes/common.go` to accept a `kubeAPIOptions` struct.
    *   Create a `legacyid` integration test suite in `tests/integration/suite/sentry/validator/kubernetes/legacyid.go`.
    *   Update the `longname` integration test to verify rejection of legacy long-name IDs and app IDs over 64 characters.
    *   Modify `validateCertificateResponse` in the insecure and JWKS test suites to assert that `cert.DNSNames` is empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.