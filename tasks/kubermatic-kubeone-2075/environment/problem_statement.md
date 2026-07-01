## Description

When configuring a cluster, operators sometimes need to limit how many pods can run on each node. Currently, there is no field in the host configuration to specify a maximum pod count — nodes always use whatever default their runtime provides. We need to add support for this setting so that operators can control pod density per host.

## Expected Behavior

- Operators should be able to set a maximum pod count per host in the host configuration.
- If the maximum pod count is specified, it must be a positive number. Providing zero or a negative value should be rejected during configuration validation with a clear error.
- If the maximum pod count is not specified at all, the configuration should be considered valid and the system should use the runtime's default limit.

## Why This Matters

Without the ability to configure per-node pod limits, operators cannot tailor resource utilization for nodes with different capacities. Additionally, catching invalid values (zero, negative) during validation prevents misconfigured clusters from being deployed and failing later during the actual node setup.
