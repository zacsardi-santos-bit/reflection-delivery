Implement the integration of an OpenTelemetry Collector into Gardener's seed cluster logging pipeline. Ensure that node log agents route logs through the collector when the feature is enabled, and directly to the log store when disabled. Update the authentication proxy setup and provide utility functions for Kubernetes volume configurations.

*   Implement the `GenerateGenericKubeconfigVolume` function in `pkg/utils/gardener/shoot.go`:
    *   Accept parameters: `genericKubeconfigName`, `accessSecretName`, `volumeName`.
    *   Return a `corev1.Volume` with a Projected source, `DefaultMode=420`, and two `SecretProjections`:
        *   Kubeconfig secret: `key='kubeconfig'`, `path='kubeconfig'`, `optional=false`.
        *   Access secret: `key='token'`, `path='token'`, `optional=false`.

*   Implement the `GenerateGenericKubeconfigVolumeMount` function in `pkg/utils/gardener/shoot.go`:
    *   Accept parameters: `volumeName`, `mountPath`.
    *   Return a `corev1.VolumeMount` with `Name=volumeName`, `MountPath=mountPath`, `ReadOnly=true`.

*   Update the OpenTelemetry Collector package:
    *   Export an `Interface` type in `pkg/component/observability/opentelemetry/collector/collector.go`:
        *   Embed `component.DeployWaiter` and add `WithAuthenticationProxy(enabled bool)`.
    *   Implement the `New` constructor:
        *   Accept parameters: `client`, `namespace`, `Values`, `secretsManager`.
        *   Return a new collector `Interface`.

*   Define the `Values` struct in `pkg/component/observability/opentelemetry/collector/collector.go`:
    *   Include fields: `Image`, `KubeRBACProxyImage`, `LokiEndpoint`, `Replicas`.

*   Ensure the collector component:
    *   Deploys with or without an authentication proxy based on `WithAuthenticationProxy`.
    *   When enabled, includes a kube-rbac-proxy sidecar with specific configurations and security context.
    *   Uses a projected kubeconfig volume and mounts it correctly.

*   Update the `vali` component's `Deploy` method:
    *   Check the OpenTelemetryCollector feature gate.
    *   Adjust Ingress and RBAC rules based on the feature gate status.

*   Register and expose the OpenTelemetryCollector feature gate in the gardenlet feature gates package.

*   Ensure the botanist logging reconciliation calls `WithAuthenticationProxy` appropriately on deployers.

*   Provide a gomock-compatible `MockInterface` in `pkg/component/observability/opentelemetry/collector/mock/mocks.go`.

*   Create a `constants` package in `pkg/component/observability/opentelemetry/collector/constants/constants.go`:
    *   Export constants: `ServiceName`, `PushEndpoint`, `KubeRBACProxyPort`, `PushPort`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.