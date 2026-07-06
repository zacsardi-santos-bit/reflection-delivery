Implement a system that allows session-authenticated users to create scheduled tasks through the API by generating and attaching the necessary authorization tokens. Ensure the system can determine the permissions required by a task's query script and create tokens with the appropriate access.

*   Update the PreAuthorizer interface in `query/preauthorizer.go`:
    *   Declare a method `RequiredPermissions(ctx context.Context, spec *flux.Spec) ([]platform.Permission, error)`.
    *   Implement this method in the concrete `preAuthorizer` struct returned by `NewPreAuthorizer`.
    *   Ensure `RequiredPermissions` inspects the Flux spec for destination buckets, resolves their IDs and organization IDs, and returns a `platform.Permission` granting write access using `platform.NewPermissionAtID`.

*   Modify `http/task_service.go`:
    *   Add a `BucketService` field of type `platform.BucketService` to the `TaskBackend` struct.
    *   Ensure `NewTaskHandler` copies `TaskBackend.BucketService` to `TaskHandler.BucketService`.

*   Implement task creation handling:
    *   For POST requests to `/api/v2/tasks` with a session-based authorizer, automatically create an authorization token.
    *   Derive the token's permissions from the task's Flux script using `RequiredPermissions`.
    *   Assign the generated token string to the task's `Token` field.
    *   Persist the authorization token for later retrieval by the authorization service.
    *   Ensure the task creation request returns HTTP 201 Created with a valid task record.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.