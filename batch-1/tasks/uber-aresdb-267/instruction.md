Implement the specified changes to the data node bootstrap system to improve its architecture and ensure correct concurrent behavior. Update constructors and methods as described, and ensure proper handling of concurrent bootstrap requests.

*   Update the `NewBootstrapManager` function:
    *   Modify the signature to accept a `bootstrap.Options` value as the third parameter instead of the top-level `Options` type.
    *   Ensure the call site in `datanode.go` passes `opts.BootstrapOptions()` when constructing the manager.
    *   Internally call `NewPeerSource(topo, nil)` without passing a logger.

*   Update the `NewPeerSource` function:
    *   Remove the logger parameter from its signature. The new signature should be `NewPeerSource(topo topology.Topology, dialerOverride client.PeerConnDialer) (client.PeerSource, error)`.
    *   Use `utils.GetLogger()` internally for logging purposes.

*   Modify the `BootstrapManager` interface:
    *   Implement the `Bootstrap()` method to return `nil` on success and propagate any underlying error on failure.
    *   Ensure concurrent calls to `Bootstrap()` coalesce such that at most one additional bootstrap run is queued while a bootstrap is already in progress. For example, 10 concurrent calls should result in exactly 2 underlying bootstrap executions.
    *   Implement the `IsBootstrapped()` method to return `false` before any successful bootstrap and `true` after at least one successful bootstrap.
    *   Implement the `LastBootstrapCompletionTime()` method to return `(time.Time, bool)`. After a successful bootstrap, it should return `(utils.Now(), true)`, recording the time at the moment the bootstrap completes.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.