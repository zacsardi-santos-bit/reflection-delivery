Implement two new helper functions, `ApplyEndpoints` and `ApplyEndpointSlice`, in the `resource-apply` package to manage Kubernetes endpoint resources. These functions should follow existing patterns for hash-based change detection, ownership verification, and event emission.

*   Implement `ApplyEndpoints` in `pkg/resourceapply/core.go` with the following behavior:
    *   Create a new Endpoints object if none exists, returning the created object, `changed=true`, and emitting a 'Normal EndpointsCreated' event.
    *   Return the existing object unchanged with `changed=false` if the hash matches the required state.
    *   Update the existing Endpoints if subsets, labels, or managed fields differ, returning the updated object, `changed=true`, and emitting a 'Normal EndpointsUpdated' event.
    *   Return an error and `changed=false` if the required Endpoints lacks `OwnerReferences`.
    *   Return an error and emit a 'Warning UpdateEndpointsFailed' event if the existing Endpoints is not controlled by the current controller.
    *   Handle cases where the object is in the lister cache but not found in the API server, returning an error and emitting a 'Warning UpdateEndpointsFailed' event.
    *   Use the existing object's `ResourceVersion` if the required object does not specify one.
    *   Preserve externally added labels and annotations during updates.
    *   Ensure idempotency: repeated calls with the same state should result in `changed=false` and no events.

*   Implement `ApplyEndpointSlice` in `pkg/resourceapply/discovery.go` with the following behavior:
    *   Create a new EndpointSlice object if none exists, returning the created object, `changed=true`, and emitting a 'Normal EndpointSliceCreated' event.
    *   Return the existing object unchanged with `changed=false` if the hash matches the required state.
    *   Update the existing EndpointSlice if endpoints, ports, labels, or managed fields differ, returning the updated object, `changed=true`, and emitting a 'Normal EndpointSliceUpdated' event.
    *   Return an error and `changed=false` if the required EndpointSlice lacks `OwnerReferences`.
    *   Return an error and emit a 'Warning UpdateEndpointSliceFailed' event if the existing EndpointSlice is not controlled by the current controller.
    *   Handle cases where the object is in the lister cache but not found in the API server, returning an error and emitting a 'Warning UpdateEndpointSliceFailed' event.
    *   Use the existing object's `ResourceVersion` if the required object does not specify one.
    *   Preserve externally added labels and annotations during updates.
    *   Ensure idempotency: repeated calls with the same state should result in `changed=false` and no events.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.