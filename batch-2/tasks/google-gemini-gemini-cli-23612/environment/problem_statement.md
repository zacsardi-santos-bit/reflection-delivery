## Description

The file system service that proxies read/write operations to a connected agent session currently forwards all file path requests to the remote connection without checking whether the file actually lives within the intended project workspace. This means an agent session could potentially access files outside the working directory, including sensitive personal configuration folders used by the tool itself.

## Expected Behavior

- File operations should only be forwarded to the remote connection when the target file path falls within a designated root directory.
- If the requested file is outside the root directory, the operation should fall back to the local file system service, even when the remote capability is available.
- Files that reside in the user's personal tool configuration folder should always be handled locally (fallback), even when the root directory is set to a parent that would otherwise encompass them.
- When the remote connection reports that a requested file does not exist, the error should be translated into a standard filesystem "file not found" error (with an appropriate error code) so that callers can handle it consistently.

## Why This Matters

Without path-based access control, any file operation proxied to a connected agent session could inadvertently expose or modify files outside the project scope, including configuration data that the tool manages privately. Enforcing a root boundary and properly normalizing "not found" errors makes the service safer and more predictable for both users and downstream code.
