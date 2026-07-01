Implement the following updates to the AWS Load Balancer Controller to address issues with OIDC secret handling, listener attribute conflict resolution, error message clarity, and observability.

*   Strip trailing Unicode control characters from OIDC client secrets:
    *   Modify the OIDC authentication configuration process to automatically strip trailing control characters from the `clientSecret` value read from Kubernetes secrets.

*   Resolve listener attribute conflicts using IngressClass parameters:
    *   Ensure that when listener attribute keys are specified by IngressClass parameters, any conflicting annotation values from ingresses in the same group are ignored.
    *   Prioritize the IngressClass parameter value over conflicting annotations, preventing errors.

*   Clarify error messages for load balancer attribute conflicts:
    *   Update error messages to specify the resource type when multiple ingresses define conflicting values for a load balancer attribute key.
    *   Format the error message as: "conflicting load balancer attributes <key>: <value1> | <value2>".

*   Introduce observability for pod readiness gate transitions:
    *   Create a new metrics package at `pkg/metrics/lbc` with the package name 'lbc'.
    *   Define a `MetricCollector` interface with the method `ObservePodReadinessGateReady(namespace string, tgbName string, duration time.Duration)`.
    *   Export a constant `MetricPodReadinessGateReady` with the value "readiness_gate_ready_seconds".
    *   Implement a `NewCollector` function that returns a real metrics collector if a non-nil `prometheus.Registerer` is provided, otherwise returns a no-op collector.
    *   Provide a `MockCollector` struct with an `Invocations` field to record method calls, and a `NewMockCollector` constructor.
    *   Update `defaultResourceManager` in `pkg/targetgroupbinding/resource_manager.go` to include a `metricsCollector` field of type `lbcmetrics.MetricCollector`.
    *   Modify `NewDefaultResourceManager` to accept a `metricsCollector` parameter.
    *   Update the `updateTargetHealthPodConditionForPod` method to accept a `tgb *elbv2api.TargetGroupBinding` parameter and call `metricsCollector.ObservePodReadinessGateReady` when a pod's target health condition transitions from non-True to True.

*   Relocate AWS SDK metrics package:
    *   Move the AWS SDK metrics package to `pkg/metrics/aws` with the package name 'aws'.
    *   Export the `Collector` type and update the `NewCollector` function to return `*Collector` without an error return value.
    *   Implement `WithSDKMetricCollector` to attach SDK metric collection middleware.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.