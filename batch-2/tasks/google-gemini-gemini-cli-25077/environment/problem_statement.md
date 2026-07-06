## Description

The Windows sandbox manager currently applies filesystem access controls by calling an external system tool for each individual directory at sandbox setup time — once to grant access for allowed paths and once to deny access for forbidden paths. This approach has several drawbacks: it fails or is silently skipped for paths that don't exist yet, it requires the host process to have elevated permissions to apply access labels, and it tightly couples sandbox policy enforcement to the host-side setup code.

## Expected Behavior

Instead of pre-applying filesystem permissions through external tool calls, the sandbox manager should write two manifest files — one listing paths that should be allowed for write access and one listing paths that should be forbidden — and pass both files to the sandbox helper binary via dedicated command-line flags. The helper binary should then be responsible for applying the policy at runtime.

- The argument layout passed to the helper should include both a forbidden manifest flag and a new allowed manifest flag, each pointing to the respective manifest file.
- Manifest files should be plain text with one path per line.
- The allowed manifest should contain the workspace, additional allowed paths, and write paths — but must exclude drive roots and git directory paths.
- The forbidden manifest should include all forbidden paths, even those that don't exist on disk (previously these were skipped, causing policy gaps).
- When a path appears in both allowed and forbidden lists, it should be present only in the forbidden manifest.
- The cleanup function should remove both manifest files and their shared temporary directory.

## Why This Matters

The current implementation silently skips non-existent forbidden paths, leaving potential policy gaps. Moving to a manifest-based approach makes the sandbox policy more complete, declarative, and easier for the helper binary to enforce consistently.
