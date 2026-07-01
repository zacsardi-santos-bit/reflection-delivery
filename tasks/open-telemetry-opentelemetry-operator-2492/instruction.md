Fix the application of security settings for the Target Allocator component in the OpenTelemetry Operator to ensure they are applied at the container level, not the pod level. Update the relevant code to reflect this change and ensure container-specific security properties are respected.

*   Update the `OpenTelemetryTargetAllocator` struct:
    *   Ensure the `SecurityContext` field is of type `*v1.SecurityContext` in `apis/v1alpha1/opentelemetrycollector_types.go`.
    *   This field should support container-specific properties such as `RunAsNonRoot`, `RunAsUser`, `RunAsGroup`, and `Privileged`.

*   Modify the `Container` function in `internal/manifests/targetallocator/container.go`:
    *   Propagate the `SecurityContext` value from `otelcol.Spec.TargetAllocator.SecurityContext` to the `SecurityContext` field of the returned `corev1.Container`.

*   Adjust the `Deployment` function in `internal/manifests/targetallocator/deployment.go`:
    *   Do not set the pod-level `SecurityContext` (`Spec.Template.Spec.SecurityContext`) from the `TargetAllocator`'s `SecurityContext` field.
    *   Ensure the pod-level `SecurityContext` is not derived from `TargetAllocator.SecurityContext`.

*   Update the `DeepCopyInto` method in `apis/v1alpha1/zz_generated.deepcopy.go`:
    *   Allocate a `new(v1.SecurityContext)` when deep-copying a non-nil `SecurityContext` field on `OpenTelemetryTargetAllocator`.
    *   Ensure not to allocate a `new(v1.PodSecurityContext)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.