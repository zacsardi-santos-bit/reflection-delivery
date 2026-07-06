Add Prometheus as a supported integration in the k8sgpt project. Ensure it can be retrieved by name and appears in the list of available integrations. Follow the existing pattern for integrations and satisfy the required interface.

*   Implement a Prometheus integration:
    *   Create a new integration implementation for Prometheus in a sub-package, such as `pkg/integration/prometheus/`.
    *   Ensure the Prometheus integration satisfies the `IIntegration` interface defined in the `pkg/integration` package.
        *   Implement all required methods: `Deploy`, `UnDeploy`, `AddAnalyzer`, `GetAnalyzerName`, `GetNamespace`, `OwnsAnalyzer`, `IsActivate`.

*   Register the Prometheus integration:
    *   Add the Prometheus integration to the integration registry in `pkg/integration/integration.go`.
    *   Use the key "prometheus" to register the integration in the `integrations` map.

*   Ensure functionality:
    *   Verify that retrieving an integration by the name "prometheus" returns a valid, non-nil integration object with no error.
    *   Confirm that "prometheus" appears in the list of available integrations when listing all registered integrations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.