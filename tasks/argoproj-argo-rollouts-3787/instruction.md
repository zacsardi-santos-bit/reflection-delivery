Implement a system to allow analysis templates in Argo Rollouts to reference Datadog credentials from a named secret within their own namespace. Update the credential lookup process to support multiple sources and ensure proper error handling when credentials are unavailable or improperly configured.

*   Update the `NewDatadogProvider` function in `metricproviders/datadog/datadog.go`:
    *   Add a `namespace` string parameter between the Kubernetes client and metric arguments: `NewDatadogProvider(logCtx log.Entry, kubeclientset kubernetes.Interface, namespace string, metric v1alpha1.Metric) (*Provider, error)`.

*   Modify the `newProvider` function type on the `Controller` struct in `analysis/controller.go`:
    *   Include a `namespace` string parameter: `func(logCtx log.Entry, namespace string, metric v1alpha1.Metric) (metric.Provider, error)`.

*   Enhance the `DatadogMetric` type in `pkg/apis/rollouts/v1alpha1/`:
    *   Add a `SecretRef` field of type `SecretRef` with sub-fields:
        *   `Name` (string) — name of the secret.
        *   `Namespaced` (bool) — indicates if the secret should be looked up in the analysis template's namespace.

*   Implement the `findCredentials` function in `metricproviders/datadog/datadog.go`:
    *   Signature: `findCredentials(logCtx log.Entry, kubeclientset kubernetes.Interface, namespace string, metric v1alpha1.Metric) (string, string, string, error)`.
    *   Return an error if `SecretRef.Namespaced` is true and `SecretRef.Name` is empty.
    *   Return an error if `SecretRef.Namespaced` is true and the secret is not found in the specified namespace.
    *   Return the address, apiKey, and appKey from the secret data if found.

*   Define the `CredentialsFinder` interface in `metricproviders/datadog/finders.go`:
    *   Method: `FindCredentials(logCtx log.Entry) (string, string, string)`.

*   Create the `NewSecretFinder` constructor in `metricproviders/datadog/finders.go`:
    *   Signature: `NewSecretFinder(kubeclientset kubernetes.Interface, secretName string, namespace string) *secretFinder`.
    *   The `FindCredentials` method must read credentials from a Kubernetes secret using keys 'address', 'api-key', and 'app-key'.
    *   Return empty strings if the secret is not found or if `api-key` or `app-key` are missing.

*   Ensure `NewDatadogProvider` correctly creates a provider and returns metric results when:
    *   A namespaced `SecretRef` with a valid secret name is specified on a V1 Datadog metric.
    *   A namespaced `SecretRef` with a valid secret name is specified on a V2 Datadog metric.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.