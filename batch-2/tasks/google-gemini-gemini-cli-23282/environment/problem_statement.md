## Description

The sandbox system currently supports an "allowlist" model — developers can explicitly grant access to specific paths beyond the workspace. However, there is no way to explicitly deny access to specific paths, even when those paths fall within the workspace that is otherwise broadly accessible. This is a security gap: sensitive files or directories inside the workspace cannot be protected from sandboxed commands.

We need to add a "forbidden paths" capability to the sandbox policy, letting callers specify paths that should always be blocked for both reading and writing, regardless of other policy settings.

## Expected Behavior

- The sandbox policy should accept a list of forbidden paths in addition to allowed paths.
- Access to forbidden paths must be blocked for both read and write operations.
- If a path appears in both the allowed list and the forbidden list, the forbidden restriction must win.
- Non-existent forbidden paths should be handled gracefully — the sandbox setup should not fail, and (on supported platforms) the path's creation should also be prevented.
- When a forbidden path is a symbolic link, both the symlink path and its resolved real target must be blocked.
- Forbidden directories must block recursive access to all nested files and directories.
- This capability should work consistently on all supported platforms (Linux, macOS, and Windows).

## Why This Matters

Without explicit deny support, the sandbox cannot be used to protect sensitive files within the workspace, such as credential files, private configuration, or secrets embedded in the project directory. Callers need fine-grained control to both grant and revoke access when composing sandbox policies.
