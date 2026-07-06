Implement a feature to determine CPU assignments for Scylla containers using the kubelet pod resources API in the node tuning controller. Replace the current method of querying the container runtime and cgroup filesystem paths with this API to ensure consistent and reliable CPU assignment data across different Kubernetes environments.

*   Update the `getIRQCPUs` function:
    *   Change the signature to `getIRQCPUs(ctx context.Context, kubeletPodResourcesClient kubelet.PodResourcesClient, scyllaPods []*corev1.Pod, hostFullCpuset cpuset.CPUSet) (cpuset.CPUSet, error)`.
    *   Accept a `PodResourcesClient` instead of a CRI client and remove the cgroup mountpoint parameter.
    *   Ensure the function skips pods that are not of Guaranteed QoS class.
    *   For each Guaranteed QoS Scylla pod, retrieve CPU assignments by:
        *   Calling `List` on the `PodResourcesClient`.
        *   Matching pods by name and namespace.
        *   Finding the container named 'scylla' and extracting its `CpuIds`.
    *   Return an error wrapping 'can't find Scylla container cpuset' if the API returns a non-empty result but the Scylla container's CPU set is empty for a Guaranteed pod.
    *   Calculate the IRQ CPU set as the host full CPU set minus CPUs pinned to Guaranteed QoS Scylla containers.

*   Define the `PodResourcesClient` interface in a new package `pkg/kubelet`:
    *   Include the method `List(ctx context.Context) ([]*v1.PodResources, error)`.
    *   Include the method `Close() error`.

*   Implement a concrete `PodResourcesClient` that connects to the kubelet pod resources gRPC endpoint and fulfills the `List` and `Close` methods.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.