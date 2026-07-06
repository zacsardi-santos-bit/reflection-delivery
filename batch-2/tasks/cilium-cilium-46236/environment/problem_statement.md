## Description

When a pod requests a network device with static IP addresses for both address families specified in an annotation, the network driver does not take into account whether the cluster is running in single-stack or dual-stack mode. On a cluster where only one IP address family is enabled, the driver still applies the annotation address for the disabled family. This leads to misconfigured network devices on single-stack clusters.

## Expected Behavior

- If the cluster has only IPv4 enabled and the annotation specifies both an IPv4 and an IPv6 address, only the IPv4 address should be used for device configuration.
- If the cluster has only IPv6 enabled and the annotation specifies both an IPv4 and an IPv6 address, only the IPv6 address should be used for device configuration.
- If the cluster is dual-stack (both families enabled), both annotated addresses should be applied as before.
- If the annotation only specifies one address family's address (regardless of stack mode), the other family's address should fall back to the default from the resource claim configuration.

## Why This Matters

Single-stack deployments should not have addresses from the disabled IP family injected into their network device configuration. The driver must respect the cluster's active IP family settings when applying static IP annotations, ensuring consistent and correct network behavior across single-stack and dual-stack environments.
