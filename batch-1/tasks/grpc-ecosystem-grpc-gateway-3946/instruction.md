Implement a fix in the gRPC-Gateway setup to ensure that path parameters always take precedence over query parameters when both target the same field. Extend the echo service with new routes and message fields to demonstrate this behavior, ensuring consistent handling of nested fields and different naming conventions.

*   Create two new HTTP GET routes for the echo service:
    *   `/v1/example/echo/resource/{resourceId}`: Maps `resourceId` path parameter to `resource_id` field.
    *   `/v1/example/echo/nested/{nId.nId}`: Maps `n_id.n_id` path parameter to `n_id.n_id` nested field.
*   Ensure path parameter values override any conflicting query parameter values targeting the same field.
    *   Ignore query parameters like `?resourceId=...` when `resourceId` is set by the path.
    *   Ignore query parameters like `?nId.nId=...` when `n_id.n_id` is set by the path.
*   Allow non-conflicting query parameters to apply normally:
    *   Apply query parameters like `?n_id.val=foo` when they target different sub-fields.
*   Extend protobuf message types:
    *   `SimpleMessage` and `UnannotatedSimpleMessage` must include:
        *   `resource_id` string field (JSON name `resourceId`).
        *   `n_id` nested message field (JSON name `nId`).
    *   Create `NestedMessage` and `UnannotatedNestedMessage` types with:
        *   `n_id` string field (JSON name `nId`).
        *   `val` string field (JSON name `val`).
*   Update `runtime/request.go` in the `PopulateQueryParameters` function:
    *   Normalize camelCase query parameter keys to proto snake_case equivalents before filtering.
    *   Implement a `normalizeFieldPath` function to convert query parameter keys using message descriptors.
*   Update proto/YAML route bindings:
    *   For `echo_service.proto`, add HTTP rule bindings for new routes.
    *   For `unannotated_echo_service.yaml`, add equivalent route entries.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.