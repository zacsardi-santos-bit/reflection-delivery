I'm working on the SPIRE server's certificate authority manager. Right now, the CA journal — which tracks the history of certificate authority keys and their rotation status — is only persisted to disk. This means there's no centralized record in the datastore, and old journal data that is no longer relevant is never cleaned up automatically.

I'd like to add datastore-backed storage for CA journals alongside the existing disk storage. When the server starts up and finds a journal on disk but not yet in the datastore, it should load from disk and start writing to both places going forward. The datastore should support creating and updating journals, fetching a journal by its associated authority identifier, pruning journals whose certificate authorities have all expired past a safety threshold, and listing all journals.

I'd also like the CA rotator to periodically prune old CA journals from the datastore (in addition to its existing bundle pruning), using a separate interval and safety threshold. The two pruning operations — bundle pruning and CA journal pruning — should each have their own dedicated constants.

Additionally, all the rotation and activation operations on the CA manager (rotating and activating both X.509 CAs and JWT keys) should accept a context so that cancellation and deadlines propagate correctly through those calls.

The telemetry wrapper around the datastore should emit metrics for each of the new CA journal operations, and errors from failing to save a journal to the datastore should surface a clear error message while still allowing the on-disk save to proceed.
