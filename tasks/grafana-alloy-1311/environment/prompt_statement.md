I'm working on the cluster service in Grafana Alloy and need to implement a dedicated peer discovery component. Right now there's no clean, testable abstraction for how peers are found when forming a cluster, and I need to build one that supports several different strategies.

The component should accept a configuration that includes a list of static peer addresses, an optional cloud-provider discovery string, and a default port to use when addresses don't include one. It should validate the configuration when it's created — returning an informative error if a required dependency (like a logger or tracer) is missing, or if two mutually exclusive options are both set.

When peer discovery is actually triggered, it should handle these cases:
- Static entries already in host:port format should be returned as-is.
- Plain IP addresses without a port should have the default port appended.
- Bare hostnames (not IPs) should be resolved via DNS SRV records; all resulting addresses should use the default port, not the port from the SRV record. If all SRV lookups come back empty, an error should be returned.
- If some SRV lookups fail but others succeed, the successful results should be returned without error.
- Cloud provider-based discovery should also be supported, using a factory function to create the underlying provider client. Addresses returned by the provider should keep their port if one is included, or fall back to the default port.

Errors from cloud provider lookups or unknown provider names should be surfaced directly to the caller.
