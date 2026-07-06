Implement acknowledgment tracking for configuration updates in the xDS resource management layer. Ensure that completion callbacks can be attached to resource operations and are triggered once all specified nodes have acknowledged the updates. Update server configurations to include acknowledgment observers and implement a utility for parsing node identifiers.

*   Implement the `IstioNodeToIP` function in `pkg/envoy/xds/node.go`:
    *   Accept a pointer to an `Envoy Node`.
    *   Extract and return the IP address from the node's ID field, formatted as "type~ip~nodeId~domain".
    *   Return an error if the node is nil, the ID is empty, does not have exactly 4 parts, or the IP is invalid.

*   Implement `NewAckingResourceMutatorWrapper` in `pkg/envoy/xds/ack.go`:
    *   Accept a `ResourceMutator` and a `NodeToIDFunc`.
    *   Return an `*AckingResourceMutatorWrapper` for tracking acknowledgments and notifying via callbacks.

*   Implement methods in `AckingResourceMutatorWrapper` in `pkg/envoy/xds/ack.go`:
    *   `Upsert`: Insert/update resources with `force=true`, register completions for specified nodes, and trigger only when all nodes acknowledge.
    *   `Delete`: Delete resources with `force=true`, register completions for specified nodes, and trigger when all nodes acknowledge the version.
    *   `HandleResourceVersionAck`: Use `NodeToIDFunc` to extract node ID, complete pending acknowledgments if conditions are met, and remove canceled/time-out completions.

*   Ensure completion conditions:
    *   `Upsert` completions trigger only for specified nodes, resource names, and versions.
    *   `Delete` completions trigger for any resource name at the correct version.
    *   More recent version acknowledgments satisfy older pending completions.

*   Update server configuration in `pkg/envoy/xds/server.go`:
    *   Implement `NewServer` to accept a map of resource type URLs to `*ResourceTypeConfiguration`.
    *   Register resource watchers and ACK observers for each type URL.

*   Define `ResourceTypeConfiguration` struct in `pkg/envoy/xds/server.go`:
    *   Include `Source` of type `ObservableResourceSource` and `AckObserver` of type `ResourceVersionAckObserver`.

*   Modify `Cache` methods in `pkg/envoy/xds/cache.go`:
    *   `Upsert`: Add a `force` parameter to control update behavior.
    *   `Delete`: Add a `force` parameter to control delete behavior.
    *   `tx`: Add a `force` parameter to propagate force behavior in transactions.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.