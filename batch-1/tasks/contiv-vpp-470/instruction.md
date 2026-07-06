Implement a proper initial reconciliation process for Kubernetes state reflectors to synchronize the local data store with the live Kubernetes state at startup. Refactor the reflectors to use a shared base struct for synchronization logic and error handling. Ensure the mock lister in tests shares the same data store as the mock writer.

*   Refactor Reflector Struct:
    *   Embed a `Reflector` struct in `EndpointsReflector`, `NamespaceReflector`, and `ServiceReflector` using the field name 'Reflector'.
    *   Implement `HasSynced() bool` in `Reflector` to indicate if the initial sync is complete.
    *   Implement `GetStats() *ReflectorStats` in `Reflector` to return operation statistics.
    *   Include a `dsSynced bool` field in `Reflector`, initialized to false, to control initial sync.
    *   Include a `Lister` field in `Reflector` for listing existing items in the data store.

*   Initial Synchronization:
    *   Perform an initial sync when `dsSynced` is false:
        *   List current Kubernetes objects using a list function.
        *   Add missing objects to the data store, incrementing `NumAdds`.
        *   Update objects that differ, incrementing `NumUpdates`.
        *   Delete objects not present in Kubernetes, incrementing `NumDeletes`.
    *   Ensure `HasSynced()` returns true after sync completion.

*   Error Handling:
    *   Increment `NumArgErrors` when event handlers receive arguments of the wrong type and do not proceed with the operation.

*   Testing and Mocking:
    *   Implement `newMockKeyProtoValLister(ds map[string]proto.Message) *mockKeyProtoValLister` to share the data store with the mock writer.
    *   Ensure `MockK8sCache` variable is accessible within the ksr package, with a `ListFunc` field for listing current Kubernetes objects.

*   Package Requirements:
    *   Ensure `plugins/service/configurator` and `plugins/service/processor` exist as compilable Go packages with basic test files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.