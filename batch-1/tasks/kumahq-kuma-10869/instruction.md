Implement support for fine-grained reachable backend references in the service mesh proxy configuration. Update the dataplane configuration validator, refactor the reachability graph, and create new subpackages for rule-building logic to handle backend-level checks.

*   Update the dataplane validator:
    *   Include a third resource type as a valid value for outbound backend reference kinds.
    *   Ensure error messages for invalid kinds read: 'invalid value. Available values are: MeshExternalService,MeshMultiZoneService,MeshService'.
    *   Validate reachable backends list in transparent proxying configuration.
    *   Report errors under 'networking.transparentProxing.reachableBackends.refs[N]'.
    *   Produce specific violation messages for:
        *   Invalid kind: 'invalid value. Available values are: MeshExternalService,MeshMultiZoneService,MeshService'.
        *   Both name and labels specified: 'labels cannot be defined when name is specified'.
        *   Neither name nor labels specified: 'name or labels are required'.
        *   Namespace without name: 'name is required, when namespace is defined'.
        *   Invalid name format: 'invalid characters. A lowercase RFC 1123 subdomain must consist of lower case alphanumeric characters, \'-\' or \'.\', and must start and end with an alphanumeric character'.
    *   Ensure valid configurations pass without errors.

*   Refactor the reachability graph:
    *   Create a new package at 'pkg/plugins/policies/meshtrafficpermission/graph/backends' with:
        *   `BuildRules` function to accept MeshService and MeshTrafficPermission resources, returning a map from `BackendKey` to rules.
        *   `BackendKey` struct with `Kind` and `Name` fields.
    *   Ensure `BuildRules` does not modify input MeshTrafficPermission objects, using deep copies when necessary.
    *   Move existing service-based rule-building logic to a new package at 'pkg/plugins/policies/meshtrafficpermission/graph/services'.
        *   Expose `BuildRules` and `BuildServices` functions.
    *   Update the graph package to expose `NewGraph` constructor function:
        *   Accepts service-level and backend-level rules maps.
        *   Replaces the old `BuildGraph` function.
    *   Implement `CanReachBackend` method in the `Graph` type:
        *   Takes source tags and a backend reference, returning true if the source can reach the backend.
        *   Treat `AllowWithShadowDeny` as `Allow`.
        *   Return false for backends with no matching rule, except for `MeshExternalService` which returns true unconditionally.
        *   Handle traffic permissions correctly: deny all returns false, no permissions return false, allow all returns true.

*   Ensure the backends `BuildRules` function applies top-level target ref subset matching, filtering unsupported tags.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.