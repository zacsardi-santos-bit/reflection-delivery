## Description

The node tuning component needs to determine which CPUs are assigned to Scylla containers in order to configure interrupt request (IRQ) affinity correctly. Currently, it does this by querying the container runtime interface and falling back to reading cgroup filesystem paths when runtime information is unavailable. This approach is fragile: the fallback requires platform-specific directory patterns that differ between environments (e.g., EKS, GKE, Minikube), and runtime information is not always exposed in a consistent way across different container runtimes.

## Expected Behavior

- The component should retrieve CPU assignments for Scylla containers by querying the kubelet's pod resources API, which provides this information consistently regardless of the underlying container runtime or Kubernetes distribution.
- The CPU lookup should match pods by their name and namespace, then find the Scylla container within the matched pod to extract the assigned CPU IDs.
- The old cgroup filesystem fallback logic and platform-specific path patterns should be removed.
- IRQ CPU calculation (host CPUs minus Scylla-assigned CPUs) should continue to work correctly for Guaranteed QoS pods, and burstable pods should continue to be excluded from the pinned CPU calculation.

## Why This Matters

Using the kubelet pod resources API provides a single, reliable source of CPU assignment data that works uniformly across all supported Kubernetes environments, eliminating the need for fragile environment-specific workarounds.
