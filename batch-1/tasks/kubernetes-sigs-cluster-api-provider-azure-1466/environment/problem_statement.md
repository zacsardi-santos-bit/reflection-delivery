## Description

Private clusters — those where the API server is only accessible internally — currently have no way to give their control plane nodes outbound internet connectivity through a dedicated, user-configurable load balancer. The node outbound load balancer handles worker nodes, but control plane nodes in private clusters are left without a managed outbound path.

We need to support an optional outbound load balancer specifically for control plane nodes in private clusters. Users should be able to configure it (or leave it unset to disable it) by specifying it in the cluster's network specification. The system should automatically fill in sensible defaults (name, SKU, type, frontend IP addresses) when the user provides the configuration.

## Expected Behavior

- For public clusters (where the API server load balancer already handles outbound traffic), the control plane outbound load balancer must **not** be configurable — trying to set it should result in a validation error.
- For private clusters, the control plane outbound load balancer is optional. If left unset (nil), no outbound load balancer is created.
- If a private cluster specifies a control plane outbound load balancer, the system should apply defaults: deriving the load balancer name from the cluster name, setting the appropriate SKU and type, and generating frontend IP address configurations based on the requested count.
- The control plane outbound load balancer configuration must be treated as immutable after cluster creation — any attempt to modify it should be rejected.
- The number of frontend IPs must not exceed the maximum allowed (16).

## Why This Matters

Without this feature, operators running private clusters cannot provide outbound internet access for their control plane nodes through a dedicated managed load balancer. This is a necessary capability for private cluster deployments that need control plane nodes to reach external resources.
