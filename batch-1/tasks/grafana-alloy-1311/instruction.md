Implement a dedicated peer discovery component for the cluster service in Grafana Alloy. Ensure it supports static peer addresses, DNS SRV resolution, and cloud-provider-based discovery. Validate configurations upfront and handle errors appropriately.

*   Implement the `NewPeerDiscoveryFn` function in `internal/service/cluster/discovery/peer_discovery.go` with the signature `NewPeerDiscoveryFn(opts Options) (func() ([]string, error), error)`.
    *   Validate the `Options` struct:
        *   Return an error "logger is required, got nil" if `Logger` is nil.
        *   Return an error "tracer is required, got nil" if `Tracer` is nil.
        *   Return an error "at most one of join peers and discover peers may be set" if both `JoinPeers` and `DiscoverPeers` are set.
        *   Return any error from `goDiscoverFactory` during creation.
    *   Handle `JoinPeers`:
        *   Return static entries in 'host:port' format as-is.
        *   Append `DefaultPort` to IP addresses without a port.
        *   Resolve bare hostnames using `lookupSRVFn`:
            *   Use `DefaultPort` for all resolved addresses.
            *   Return an error "failed to find any valid join addresses" if no SRV records are found.
            *   Propagate errors from SRV lookup.
            *   Combine results from multiple hostnames, returning successful resolutions without error if some lookups fail.
    *   Handle `DiscoverPeers`:
        *   Use `goDiscoverFactory` to create a go-discover instance.
        *   Preserve ports in addresses returned by go-discover; append `DefaultPort` if missing.
        *   Propagate errors from go-discover lookups.
        *   Return an error "unknown provider <name>" for unknown provider names.

*   Define the `Options` struct in `internal/service/cluster/discovery/peer_discovery.go`:
    *   Fields:
        *   `JoinPeers []string` — list of peer addresses.
        *   `DiscoverPeers string` — format string for cloud-provider discovery.
        *   `DefaultPort int` — default port for addresses without a port.
        *   `Logger log.Logger` — required logger.
        *   `Tracer trace.TracerProvider` — required tracer.
        *   `lookupSRVFn func(service, proto, name string) (string, []*net.SRV, error)` — SRV DNS lookup function.
        *   `goDiscoverFactory goDiscoverFactory` — factory for go-discover instances.

*   Define the `goDiscoverFactory` type alias in `internal/service/cluster/discovery/peer_discovery.go`:
    *   Signature: `type goDiscoverFactory func(opts ...godiscover.Option) (*godiscover.Discover, error)`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.