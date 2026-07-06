I'm working on a workspace management feature in Kusion and I need to add support for renaming workspaces. Right now there's no way to change a workspace's name without deleting it and recreating it from scratch, which is really inconvenient when workspace configuration already exists.

I need renaming to work across all the supported storage backends — local filesystem, Google Cloud Storage, S3, and Alibaba Cloud OSS. The rename operation should update the workspace's metadata (so the new name shows up in listings and the old name disappears) and also move or rename the underlying configuration file. If either the old or new name is empty, the operation should fail with an error.

On the CLI side, the workspace update command should get a new flag that lets users specify a new name for a workspace, so they can rename it without providing a configuration file. Validation should reject the reserved "default" workspace name as the target of a rename.

On the API side, the request model for updating a workspace should be cleaned up by removing the field for changing the backend association — that should be fixed at creation time, not modified through updates. The workspace manager's delete and update operations also need to be updated to properly interact with both the workspace repository and the backend repository.
