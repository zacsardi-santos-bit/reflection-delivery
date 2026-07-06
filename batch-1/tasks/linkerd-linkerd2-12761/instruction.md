Implement support for gRPC routing configurations in the outbound policy controller to ensure gRPC routes are indexed and distributed to proxies. Update the outbound policy to include gRPC routes and track the existence of backend services, notifying watchers of any changes.

*   Update the `OutboundPolicy` struct:
    *   Add a `grpc_routes` field of type `RouteSet<GrpcRouteMatch>` in `policy-controller/core/src/outbound.rs`.
    *   Ensure `grpc_routes` is analogous to the existing `http_routes` field.

*   Define new types in the core routes module:
    *   Create `GrpcRouteMatch` in `policy-controller/core/src/routes.rs`:
        *   Derive `Clone`, `Debug`, `PartialEq`, and `Eq`.
        *   Include fields: `headers` (`Vec<HeaderMatch>`) and `method` (`Option<GrpcMethodMatch>`).
    *   Create `GrpcMethodMatch` in `policy-controller/core/src/routes.rs`:
        *   Derive `Clone`, `Debug`, `PartialEq`, and `Eq`.
        *   Include fields: `method` (`Option<String>`) and `service` (`Option<String>`).

*   Implement trait for indexing gRPC routes:
    *   Implement `IndexNamespacedResource<k8s_gateway_api::GrpcRoute>` for the `Index` struct in `policy-controller/k8s/index/src/outbound/index.rs`.
    *   Ensure the `apply` method:
        *   Accepts `GrpcRoute` objects and indexes them.
        *   Converts backend references to `Backend::Service` with an `exists` flag indicating presence in the index.
        *   Sends updates to the outbound policy watch channel when the backend existence state changes.

*   Handle gRPC route application and backend service tracking:
    *   When a `GrpcRoute` is applied and references a non-existent backend service, include the route in `grpc_routes` with `Backend::Service` where `exists` is `false`.
    *   Upon registration of a backend service referenced in an existing `GrpcRoute`, update the outbound policy watch receiver to reflect `Backend::Service` with `exists` set to `true`.

*   Ensure outbound policy retrieval:
    *   The `outbound_policy_rx` must include `grpc_routes` populated from applied `GrpcRoute` resources.
    *   Ensure the parent reference matches the queried service name, namespace, and port.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.