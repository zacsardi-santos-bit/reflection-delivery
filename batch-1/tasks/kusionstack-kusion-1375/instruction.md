Implement a feature to rename workspaces in Kusion, ensuring the operation works across all supported storage backends and preserves workspace configuration. Update the CLI and API to support the rename operation while maintaining data integrity and validation.

*   Update the Storage interface in `pkg/workspace/storage.go`:
    *   Add the method `RenameWorkspace(oldName, newName string) error`.

*   Implement the `RenameWorkspace` method for each storage backend:
    *   LocalStorage (`pkg/workspace/storages/local.go`): Ensure the new name appears in `GetNames()` results, and the old name does not.
    *   GoogleStorage (`pkg/workspace/storages/google.go`)
    *   OssStorage (`pkg/workspace/storages/oss.go`)
    *   S3Storage (`pkg/workspace/storages/s3.go`)
    *   All implementations must return an error if `oldName` or `newName` is empty.

*   Modify the CLI update command:
    *   Add a `NewName` field to the `Options` struct in `pkg/cmd/workspace/update/options.go`.
    *   Ensure `Validate()` returns nil for valid `NewName` values and an error if `NewName` is "default".

*   Update the API request model:
    *   Remove the `BackendID` field from `UpdateWorkspaceRequest` in `pkg/domain/request/workspace_request.go`.
    *   Reflect this change in OpenAPI specification files (`api/openapispec/docs.go`, `swagger.json`, `swagger.yaml`).

*   Enhance the `WorkspaceManager` struct in `pkg/server/manager/workspace/workspace_manager.go`:
    *   Add a `backendRepo` field.
    *   Update `DeleteWorkspaceByID` to fetch the workspace and associated backend before deletion.
    *   Update `UpdateWorkspaceByID` to use `workspaceRepo.Update` and return the updated `*entity.Workspace`.

*   Ensure all types implementing the Storage interface, including mock implementations, implement the `RenameWorkspace` method.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.