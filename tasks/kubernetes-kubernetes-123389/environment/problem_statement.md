## Description

The nftables-based network proxy uses a simulated nftables environment for unit testing, which allows tests to verify that packet routing logic works correctly without needing a real kernel. However, this simulated environment has no way to load a textual snapshot of nftables rules — tests must build up the state programmatically through the proxier itself. This makes it impossible to write standalone packet routing tests based on static rule data.

Additionally, the simulated environment has a bug in its IPv6 support, preventing IPv6-specific packet routing tests from working correctly.

## Expected Behavior

- The simulated nftables environment should support loading rules from a textual dump (the same format produced when serializing the current rule state), so tests can initialize it with static rule data
- After loading rules from a dump, serializing the state back should produce output equivalent to the input
- IPv4 packet flow simulation should work correctly through the loaded rules, covering scenarios like: no matching service, routing to a single endpoint, routing to multiple endpoints, traffic requiring masquerade, DROP and REJECT verdicts, firewall source-range filtering, and NodePort routing
- IPv6 packet flow simulation should also work correctly through the loaded rules, covering scenarios like pod-to-cluster-IP routing, external traffic to NodePorts, and node-to-NodePort traffic

## Why This Matters

Without the ability to load static rule data, every packet routing test must run the entire proxier setup. This makes isolated testing of specific routing scenarios difficult and slow. With this capability, developers can write focused, lightweight tests for specific routing behaviors using hand-crafted rule sets, separately for IPv4 and IPv6 since the simulated environment only supports one address family at a time.
