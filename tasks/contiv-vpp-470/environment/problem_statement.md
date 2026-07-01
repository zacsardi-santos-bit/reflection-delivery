## Description

The Kubernetes state reflector components currently lack the ability to perform a proper initial reconciliation with the actual state of Kubernetes at startup. When a reflector starts, it should compare what's already stored in its local data store against the live state from Kubernetes—adding entries that are missing, updating entries that have changed, and removing entries that are no longer present in Kubernetes. Without this, stale data can accumulate in the data store across restarts and new objects may be missed.

Additionally, when event handlers (for add, delete, or update operations) receive an object of the wrong type, the reflector should gracefully record the error rather than silently proceeding. Currently, these argument type mismatches may not be properly counted in reflector statistics.

## Expected Behavior

- When a reflector initializes with the synced flag unset, it performs an initial synchronization pass that reconciles the data store with live Kubernetes state.
- After initial sync, the synced-status check method returns true.
- Stats for adds, updates, and deletes are incremented during the sync pass.
- Operations with the wrong argument type increment the argument error counter and do not proceed.
- All reflector types (for Endpoints, Namespaces, and Services) must embed a shared base reflector struct that provides these sync, stats, and lifecycle methods.
- The mock lister used in tests should share the same underlying data store as the mock writer, so that the lister reflects current data store state accurately.
- New service-related plugin packages (configurator and processor) must exist as compilable Go packages.

## Why This Matters

Without an initial sync, the reflector is unaware of pre-existing objects and stale entries when it starts or restarts. This can cause the networking control plane to be out of sync with Kubernetes, leading to incorrect routing decisions or stale network configuration. Proper reconciliation ensures the system accurately reflects Kubernetes state at all times.
