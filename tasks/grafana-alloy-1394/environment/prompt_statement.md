I'm working on the cluster service in Grafana Alloy and I need to extract the peer discovery logic into its own testable module. Right now the code that resolves peer addresses — whether from a static list of join addresses or from dynamic cloud-provider discovery — is buried inside the cluster service and can't be unit tested without real network infrastructure.

I'd like a standalone peer discovery component that accepts configuration options including a logger, a tracer, optional static join addresses, and an optional dynamic discovery string. The component should validate at creation time that required fields like the logger and tracer are present, and should reject configurations that specify both static and dynamic discovery simultaneously.

When resolving addresses, the component needs to handle several cases: a bare IP address should get the default port appended; an address that already has a port should pass through unchanged; a hostname should be resolved via DNS SRV records, using the default port for each resolved target (ignoring whatever port the SRV record itself reports). If multiple hostnames are provided and some DNS lookups fail, it should continue with the successful ones. If no valid addresses can be found at all, it should return a clear error saying so.

For dynamic discovery, the component should delegate to a pluggable cloud-provider discovery backend, adding the default port to any addresses that don't already specify one. If the provider is unknown or the lookup fails, the error should be surfaced to the caller.

To make this properly testable, both the DNS lookup function and the dynamic discovery backend factory should be injectable so tests can substitute them without real network calls.
