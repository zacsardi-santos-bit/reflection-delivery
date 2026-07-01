## Description

The cluster service in Alloy needs a standalone, testable peer discovery module that handles address resolution for both static join addresses and dynamic cloud-provider-based discovery. Currently, the peer discovery logic is not independently testable — DNS lookups and dynamic discovery interactions are coupled to the broader cluster service, making it impossible to validate the address resolution logic without live network infrastructure.

## Expected Behavior

- There should be a dedicated peer discovery component that can be created from a set of options (logger, tracer, static join addresses, or a dynamic discovery configuration string).
- The component must validate its required dependencies at creation time, returning clear errors when a logger or tracer is missing, or when conflicting discovery options are provided.
- For static addresses, it must handle bare IP addresses (adding a default port), entries that already include an explicit port (passing through as-is), and hostnames (resolving via DNS SRV records, using the default port for each result).
- When multiple hosts are provided and some DNS lookups fail, the component should continue with the ones that succeed.
- For dynamic discovery, the component must integrate with a pluggable cloud-provider discovery backend to resolve addresses from cloud providers, adding the default port to any addresses that don't already have one.
- If no valid addresses can be resolved at all, the component must return an error indicating that no valid join addresses were found.

## Why This Matters

Without this refactoring, developers cannot write reliable unit tests for the peer discovery behavior. Any bugs in address resolution (e.g., wrong port handling, partial failure behavior) are only discoverable in integration environments, slowing down development and increasing risk of regressions.
