## Description

The cluster service needs a reliable, well-tested way to discover the addresses of peers when forming or joining a cluster. Currently, the peer discovery logic is not organized into a dedicated, testable component, making it hard to verify correct behavior across the variety of address formats and discovery strategies that operators use in practice.

## Expected Behavior

- The peer discovery component should validate its configuration upfront and return clear, descriptive errors when required fields are missing or mutually exclusive options are both set.
- Static addresses provided in host:port format should be returned as-is without any further lookup.
- IP addresses provided without a port should have a configured default port appended automatically.
- Bare hostnames (not IPs, not host:port) should be resolved via DNS SRV records, and all resolved addresses should use the configured default port regardless of what the SRV record advertises.
- If SRV resolution finds no valid addresses across all provided hostnames, the discovery should return an informative error.
- If SRV resolution fails for some hostnames but succeeds for others, the successful results should still be returned.
- Cloud provider-based discovery (e.g. AWS, GCE) should be supported via an pluggable factory; discovered addresses that already include a port should keep that port, while those without should use the default port.
- Errors from the underlying cloud provider discovery should be surfaced to the caller.

## Why This Matters

Alloy is increasingly deployed in clustered environments across different infrastructure providers. Having a structured, tested peer discovery mechanism ensures that operators get clear feedback on misconfiguration and that address resolution behaves predictably regardless of whether peers are specified statically, via DNS, or via cloud-provider APIs.
