## Description

CRI-O currently supports loading seccomp profiles only from local files on disk or from built-in defaults. There is no way to store a seccomp profile as part of a container image in a registry and have the runtime automatically fetch and apply it when the container starts.

## Expected Behavior

- Operators should be able to annotate a pod or container with a reference to an OCI image in a registry. When that annotation is present, CRI-O should automatically pull the referenced image as a security artifact and extract the seccomp profile embedded within it.
- Three levels of annotation granularity should be supported:
  - An annotation on the container image itself (image-level), applied when no explicit seccomp policy is set at a higher priority.
  - A pod-wide annotation that applies the referenced seccomp profile to all containers in the pod.
  - A container-specific annotation that applies the referenced seccomp profile only to the named container within the pod.
- If no matching annotation is present, existing behavior must be preserved unchanged.
- If a matching annotation is found but the artifact cannot be pulled, or if the artifact does not contain the expected profile file, the operation must fail with an error rather than silently falling back.
- When a higher-priority seccomp policy is explicitly set (such as a locally specified profile or the runtime default), the OCI artifact annotation should be ignored.

## Why This Matters

Shipping seccomp profiles as OCI artifacts alongside container images makes it much easier to distribute and version security policies together with the workloads they protect, without requiring out-of-band file distribution to every node in the cluster.
