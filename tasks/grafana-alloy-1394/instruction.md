Implement a standalone peer discovery module for the Alloy cluster service to handle address resolution for both static join addresses and dynamic cloud-provider-based discovery. Ensure the module is independently testable by injecting DNS lookup and dynamic discovery backend functions.

*   Implement the function `NewPeerDiscoveryFn(opts Options) (func() ([]string, error), error)` in `internal/service/cluster/discovery/peer_discovery.go`.
    *   Validate that the `Logger` field in `Options` is not nil. Return an error containing 'logger is required, got nil' if it is.
    *   Validate that the `Tracer` field in `Options` is not nil. Return an error containing 'tracer is required, got nil' if it is.
    *   Ensure that only one of `JoinPeers` or `DiscoverPeers` is set. Return an error containing 'at most one of join peers and discover peers may be set' if both are set.
    *   Handle errors from the `goDiscoverFactory` function by returning them at creation time.

*   For static join addresses (`JoinPeers`):
    *   Return the address as-is if it is in the 'host:port' format.
    *   Append `DefaultPort` to bare IP addresses without a port.
    *   Return a DNS lookup error for invalid IP addresses.
    *   Use DNS SRV records to resolve hostnames without a port, appending `DefaultPort` to each resolved target.
    *   Aggregate successfully resolved targets from multiple hostnames and skip failed lookups silently.
    *   Return an error containing 'failed to find any valid join addresses' if no valid addresses are resolved.

*   For dynamic discovery (`DiscoverPeers`):
    *   Use the `goDiscoverFactory` to resolve addresses.
    *   Return each address as-is if it already includes a port, or append `DefaultPort` if it does not.
    *   Return an error containing 'unknown provider <name>' for unknown providers.
    *   Propagate errors from the `go-discover` lookup.

*   Ensure that the `Options` struct in `internal/service/cluster/discovery` contains:
    *   `JoinPeers []string` for static addresses.
    *   `DiscoverPeers string` for dynamic discovery configuration.
    *   `DefaultPort int` for default port assignment.
    *   `Logger log.Logger` and `Tracer trace.TracerProvider` as required fields.
    *   `lookupSRVFn func(service, proto, name string) (string, []*net.SRV, error)` for DNS SRV lookup injection.
    *   `goDiscoverFactory goDiscoverFactory` for dynamic discovery backend injection.

*   Define the `goDiscoverFactory` type alias in `internal/service/cluster/discovery` with the signature `func(opts ...godiscover.Option) (*godiscover.Discover, error)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.