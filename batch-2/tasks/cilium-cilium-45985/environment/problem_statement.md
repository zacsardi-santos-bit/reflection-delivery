## Description

The Azure IPAM component currently tracks subnet information (network range and gateway) per individual IP address rather than at the network interface level. This creates a structural inconsistency with how the AWS and Alibaba Cloud IPAM components work, where subnet data is tracked once per interface. Because all IP configurations on a single Azure NIC must share the same subnet, storing it per-address is redundant and causes maintenance friction.

There is also a correctness bug: when an interface has only its primary IP configuration and the primary is being excluded from IP allocation, the subnet's CIDR and gateway information is silently lost. This means some interfaces end up with no CIDR or gateway recorded even though the data is available.

## Expected Behavior

- Subnet information (subnet resource ID, CIDR range) should be recorded once per network interface rather than once per IP address.
- Subnet information should be derived from the interface's IP configuration even when the only available IP configuration is the primary and primary IPs are excluded from allocation.
- The gateway should be populated whenever subnet CIDR information is available, regardless of whether the primary IP is being used for allocation.
- For backward compatibility during rolling upgrades, the old per-address subnet field and the flat per-interface CIDR field should continue to be populated as deprecated mirrors for one release.
- A lookup helper should be available that reads the new preferred subnet CIDR field but falls back to the deprecated flat field for data written by older operators, preferring the newer field when both are present and they disagree.

## Why This Matters

Aligning the Azure IPAM data model with the AWS and Alibaba Cloud IPAM models simplifies cross-cloud code paths and eliminates the bug where subnet metadata is lost for primary-only interfaces. The backward-compatible migration path ensures rolling upgrades between old and new operator versions do not break existing functionality.
