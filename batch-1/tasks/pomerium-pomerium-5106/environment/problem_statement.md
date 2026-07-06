## Description

When Pomerium is deployed in containerized environments (such as Docker or Kubernetes), the proxy process runs inside cgroup-constrained namespaces. Currently, there is no mechanism for Pomerium to detect how close its memory usage is to the container's memory limit, and no way to pass that information to the underlying proxy data plane so it can take protective action before being OOM-killed.

We need a resource monitor that:
1. Reads memory usage and limits from the host's cgroup filesystem (supporting cgroup v1, cgroup v2, and hybrid configurations)
2. Computes a memory saturation ratio and writes it to a well-known file on disk
3. Configures the proxy data plane to use that file as a resource pressure signal, triggering graduated overload actions as memory saturation increases
4. Respects a runtime flag that can disable the entire resource manager if needed

## Expected Behavior

- The monitor should auto-detect whether the system uses cgroup v1, v2, or a hybrid setup by inspecting the current process's mount namespace
- Memory saturation should be computed as a float between 0.0 and 1.0 (clamped), representing current usage divided by limit
- When no memory limit is set (unlimited), the saturation should be 0.0
- The monitor should poll more frequently as saturation increases, using a scaled tick interval
- When the resource manager is disabled via runtime configuration, the saturation reported to the proxy data plane should be reset to 0.0
- The proxy data plane should be configured with a set of graduated overload actions (heap shrinking, timeout reduction, stream reset, connection rejection) triggered at specific saturation thresholds
- If the memory limit file disappears at runtime (for example due to a cgroup change), the monitor should stop with an appropriate error

## Why This Matters

Without this capability, the proxy process may be killed abruptly by the kernel with no chance to shed load gracefully. With this feature, memory pressure is detected early and the proxy can take progressive steps to reduce memory consumption and protect service availability.
