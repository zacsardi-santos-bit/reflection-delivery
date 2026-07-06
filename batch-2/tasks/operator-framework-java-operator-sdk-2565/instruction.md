Implement a method to normalize resource quantity comparisons in Kubernetes operator workloads to prevent unnecessary updates. Ensure that numerically identical resource quantities with different string formats are treated as matches, while genuine differences are still detected.

*   Implement the `ResourceRequirementsSanitizer` class:
    *   Provide a static method `sanitizeResourceRequirements(Map<String, Object> actualMap, PodTemplateSpec actualTemplate, PodTemplateSpec desiredTemplate)` in the package `io.javaoperatorsdk.operator.processing.dependent.kubernetes`.
    *   Return immediately without modifying `actualMap` if either `actualTemplate` or `desiredTemplate` is null.
    *   Return immediately without modifying `actualMap` if either template's pod spec is null.
    *   Process both `containers` and `initContainers` lists from the template spec.
    *   Do not modify `actualMap` if the number of containers or initContainers in actual and desired templates differs.
    *   Do not modify `actualMap` if corresponding containers at the same index have different names.
    *   Do not modify `actualMap` if either actual or desired container's `ResourceRequirements` is null.
    *   Do not modify `actualMap` if the number of resource keys in actual and desired `requests` or `limits` maps differs.
    *   Do not modify `actualMap` if a resource key is present in the actual map but absent in the desired map.
    *   Do not modify `actualMap` if actual and desired resource quantities have the same string representation.
    *   Do not modify `actualMap` if actual and desired resource quantities have numerically different values.
    *   Update `actualMap` to use the desired quantity's string representation if actual and desired resource quantities represent the same numerical value but with different string formats.

*   Update the `SSABasedGenericKubernetesResourceMatcher` class:
    *   Ensure `matches()` returns `true` for StatefulSets, ReplicaSets, and DaemonSets where actual and desired container resource quantities are numerically equal but differ only in string representation.
    *   Ensure `matches()` returns `false` for StatefulSets, ReplicaSets, and DaemonSets where actual and desired container resource quantities are numerically different.
    *   For StatefulSets with VolumeClaimTemplates:
        *   Ignore actual `volumeMode` if the desired spec does not include it.
        *   Return `false` if the desired `volumeMode` differs from the actual.
        *   Ignore actual `status` if the desired spec does not include it.
        *   Return `false` if the desired `status` differs from the actual.
        *   Return `false` if VolumeClaimTemplates are added or updated.
    *   Extend the `sanitizeState` method to call `ResourceRequirementsSanitizer.sanitizeResourceRequirements` for StatefulSet, ReplicaSet, and DaemonSet resource types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.