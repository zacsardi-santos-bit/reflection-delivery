## Description

Currently, there is no way to rename an existing workspace in Kusion. If a user wants to change a workspace's name, they must delete the workspace and recreate it from scratch, which is error-prone and risks losing configuration data. We need a proper rename operation that preserves workspace configuration while changing its name.

## Expected Behavior

- All supported workspace storage backends (local filesystem, Google Cloud Storage, S3, and Alibaba Cloud OSS) should support renaming a workspace.
- A workspace rename should update the workspace's metadata (so it appears under the new name in listings) and move or copy the workspace's configuration file to match the new name.
- If either the old or new name is empty, the rename operation should fail with an error.
- The workspace update command should support a rename flag so users can rename a workspace from the CLI without having to provide a configuration file.
- Validation should prevent renaming a workspace to a reserved name (specifically, the "default" workspace name should not be usable as a rename target).
- The API request model for updating a workspace should be cleaned up: the field used to associate a backend with a workspace during an update should be removed, since the backend association is fixed at creation time.

## Why This Matters

Users often need to reorganize their workspaces as projects evolve, and forcing them to delete and recreate workspaces just to rename them is unnecessarily destructive and tedious. A first-class rename operation makes workspace management much more practical and safe.
