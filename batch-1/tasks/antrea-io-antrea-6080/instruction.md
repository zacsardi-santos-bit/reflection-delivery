Implement a smarter certificate rotation strategy for the Antrea controller. Replace the fixed maximum duration with a minimum valid duration threshold for certificate rotation. Persist certificates in a Kubernetes Secret to avoid unnecessary regeneration on controller restarts and ensure consistency across multiple instances.

Requirements:

*   Update the `CAConfig` struct in `pkg/apiserver/certificate/config.go`:
    *   Replace `MaxRotateDuration` with `MinValidDuration` of type `time.Duration`.
    *   Add `TLSSecretName` field of type `string` for Kubernetes Secret storage.
*   Set `MinValidDuration` to 90 days for the multicluster controller.
*   Implement `newSelfSignedCertProvider` in `pkg/apiserver/certificate/selfsignedcert_provider.go`:
    *   Signature: `newSelfSignedCertProvider(client kubernetes.Interface, secureServing *options.SecureServingOptionsWithLoopback, caConfig *CAConfig, options ...providerOption) (*selfSignedCertProvider, error)`.
    *   Replace `generateSelfSignedCertificate` function.
    *   Configure Secret informer if `TLSSecretName` is set.
*   Modify `selfSignedCertProvider` struct in `pkg/apiserver/certificate/selfsignedcert_provider.go`:
    *   Implement `shouldRotateCertificate(certBytes []byte) bool` to check certificate validity against `MinValidDuration`.
    *   Implement `CurrentCABundleContent() []byte` to return active certificate bytes.
    *   Implement `Run(ctx context.Context, workers int)` to handle certificate rotation and Secret updates.
    *   Include an `enqueue()` method to trigger rotation checks.
    *   Ensure `secureServing` field is accessible within the package.
*   Define `providerOption` type and related functions:
    *   `withClock(clock clockutils.Clock) providerOption` for test clock injection.
    *   `withGenerateSelfSignedCertKeyFn(fn generateSelfSignedCertKeyFn) providerOption` for custom certificate generation.
*   Define `generateSelfSignedCertKeyFn` type for certificate generation.
*   Ensure `loopbackAddresses` variable is defined and accessible in `pkg/apiserver/certificate/selfsignedcert_provider.go`.
*   Export `PodNamespaceEnvKey` constant in `pkg/util/env/env.go`.
*   Use `filepath.Join` for constructing certificate file paths.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.