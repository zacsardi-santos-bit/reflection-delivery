Add a readiness probe to the inference graph router container to ensure Kubernetes can determine when the pod is ready to serve traffic. Apply the same readiness probe consistently in both raw Kubernetes deployments and Knative-based deployments.

*   Implement the `GetRouterReadinessProbe()` function in `pkg/constants/constants.go`.
    *   Ensure it returns a `*corev1.Probe` object.
    *   Configure the probe to use an HTTP GET action to assess router health.

*   Update the `createInferenceGraphPodSpec` function in `pkg/controller/v1alpha1/inferencegraph/raw_ig.go`.
    *   Set the `ReadinessProbe` field on the router container to the result of `constants.GetRouterReadinessProbe()`.
    *   Ensure this is applied across all InferenceGraph configurations, including basic, with resource requirements, and with propagated headers.

*   Modify the `createKnativeService` function in `pkg/controller/v1alpha1/inferencegraph/knative_reconciler.go`.
    *   Set the `ReadinessProbe` field on the router container to the result of `constants.GetRouterReadinessProbe()`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.