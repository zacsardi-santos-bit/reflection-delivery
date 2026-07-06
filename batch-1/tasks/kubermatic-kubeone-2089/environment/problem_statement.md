## Description

When KubeOne provisions Kubernetes cluster nodes for a cluster that uses Cilium as its network plugin, the generated setup scripts do not include the kernel network configuration that Cilium requires to function correctly. Specifically, Cilium needs reverse path filtering to be disabled on all network interfaces, otherwise it may drop legitimate network packets (which appear "mangled" from its data plane's perspective). This setting is not applied during node setup, which means users running Cilium-based clusters may encounter networking issues after provisioning.

## Expected Behavior

- When a KubeOne cluster is configured to use Cilium as the CNI, the node setup scripts generated for all supported Linux distributions (Debian-based, CentOS/RHEL-based, Amazon Linux, and Flatcar) should automatically include the required kernel sysctl overrides for Cilium.
- The Cilium-specific sysctl configuration (disabling reverse path filtering on all interfaces) should be applied in addition to the standard Kubernetes kernel settings, not instead of them.
- Clusters using other CNI plugins should not be affected — the Cilium-specific sysctl block should only appear when Cilium is explicitly selected.

## Why This Matters

Cilium users who provision cluster nodes using KubeOne will get correctly configured nodes out of the box, without needing to manually apply kernel tuning after provisioning. This ensures reliable Cilium networking from the start and reduces the potential for hard-to-diagnose packet-drop issues.
