Implement a mechanism in the Vertical Pod Autoscaler (VPA) to consider pod-level resource constraints when generating recommendations. Ensure that the aggregate resources across all containers in a pod comply with any defined pod-level limit ranges in the namespace.

*   Extend the `LimitRangeCalculator` interface:
    *   Add the method `GetPodLimitRangeItem(namespace string) (*core.LimitRangeItem, error)` to return the pod-level `LimitRangeItem` for a given namespace.
    *   Ensure all implementations, including the no-op variant, implement `GetPodLimitRangeItem`, returning `nil` and no error for the no-op case.

*   Implement the `applyPodLimitRange` function:
    *   Location: `vertical-pod-autoscaler/pkg/utils/vpa/capping.go`
    *   Signature: `applyPodLimitRange(resources []vpa_types.RecommendedContainerResources, pod *apiv1.Pod, limitRange apiv1.LimitRangeItem, resourceName apiv1.ResourceName) []vpa_types.RecommendedContainerResources`
    *   Functionality:
        *   Accept a slice of per-container resource recommendations, a pod, a pod-level `LimitRangeItem`, and a resource name.
        *   If the sum of proportional limits across all containers exceeds the pod-level max limit, scale each container's recommendation down proportionally to fit the max.
        *   If the sum falls below the pod-level min limit, scale each container's recommendation up proportionally to meet the min.
        *   If the sum already satisfies the pod-level constraints, return the recommendations unchanged.

*   Ensure the `applyPodLimitRange` function is accessible to tests within the same package.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.