Implement traffic mirroring support in HTTPProxy routes to enable duplicating incoming requests to a secondary backend service. Ensure the configuration correctly identifies and applies the mirror designation to the proxy's routing layer.

*   Update the Service type in the HTTPProxy API:
    *   Add a boolean field `Mirror` to the Service struct located at `apis/projectcontour/v1alpha1/`.
    *   Use the signature: `Mirror bool`.
    *   This field designates a service as a traffic mirror target when set to true.

*   Modify the DAG Route configuration:
    *   Introduce a `MirrorPolicy` struct in `internal/dag/` to represent traffic mirror policies.
        *   Include a `Cluster *Cluster` field to reference the mirror service upstream.
    *   Add a `MirrorPolicy` field to the Route struct in `internal/dag/`.
        *   Use the signature: `MirrorPolicy *MirrorPolicy`.
        *   Set this field when exactly one service in an HTTPProxy route has `Mirror` set to true.

*   Implement validation logic:
    *   Ensure that if more than one service in a route has `Mirror` set to true, the route is treated as invalid.
    *   Do not produce any listener or virtual host for invalid routes.

*   Translate the DAG Route to Envoy route configuration:
    *   For routes with a non-nil `MirrorPolicy`, set the Envoy route action's `RequestMirrorPolicy` with the cluster name of the mirror service.
    *   Format the cluster name as `namespace/service/port/hash` (e.g., 'default/backendtwo/80/da39a3ee5e').

*   Ensure that for valid HTTPProxy configurations with one mirrored service:
    *   The Envoy route configuration places the mirrored traffic destination in `RequestMirrorPolicy`.
    *   The primary route cluster remains unchanged.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.