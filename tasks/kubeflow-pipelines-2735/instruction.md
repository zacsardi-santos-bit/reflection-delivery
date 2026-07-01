Implement access control for pipeline runs by enforcing authorization checks when users attempt to archive, unarchive, delete, terminate, or retry existing runs. Create utility functions to extract namespace information from stored model records and ensure that users are authorized to access the namespace before processing these requests.

*   Update the `GetNamespaceFromAPIResourceReferences` function:
    *   Accept a slice of API-layer resource references.
    *   Return the ID of the first reference with type `NAMESPACE`.
    *   Return an empty string if no `NAMESPACE`-typed reference is present.
    *   Rename the function to clarify it operates on API objects.

*   Implement the `GetNamespaceFromModelResourceReferences` function:
    *   Location: `backend/src/apiserver/model/resource_reference.go`
    *   Accept a slice of model-layer `ResourceReference` objects.
    *   Return the `ReferenceUUID` of the first entry with `ReferenceType` equal to `common.Namespace`.
    *   Return an empty string if no such entry exists.

*   Update the `CanAccessNamespaceInResourceReferences` function:
    *   Location: `backend/src/apiserver/server/util.go`
    *   Accept a resource manager, a context, and a slice of API-layer resource references.
    *   Return `nil` if the user is authorized to access the namespace or if not running in multi-user mode.
    *   Return a non-nil error if the user is unauthorized or the namespace cannot be determined.
    *   Rename the function to clarify it operates on API objects.

*   Implement the `canAccessRun` method on `RunServer`:
    *   Location: `backend/src/apiserver/server/run_server.go`
    *   Accept a context and a run ID string.
    *   Look up the run by ID and extract the namespace from its model-layer resource references.
    *   Return a non-nil error if the user is not authorized to access the namespace.
    *   Return `nil` if the user is authorized or if not running in multi-user mode.
    *   Return a non-nil error if the run does not exist or has no namespace in its resource references.

*   Ensure all authorization checks are skipped in single-user deployments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.