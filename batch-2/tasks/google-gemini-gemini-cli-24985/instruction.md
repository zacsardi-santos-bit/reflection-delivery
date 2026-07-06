Refactor the Linux sandbox argument builder to accept a consolidated structure of pre-resolved paths instead of resolving symlinks internally. Ensure that sensitive credential files in globally-included directories are protected, and allow virtual read commands for files within these directories.

*   Define a new exported type `ResolvedSandboxPaths` in `packages/core/src/services/sandboxManager.ts` with the following fields:
    *   `workspace`: an object with `original` and `resolved` string fields.
    *   `forbidden`: a string array of pre-resolved paths to deny/mask.
    *   `globalIncludes`: a string array of directories to include globally.
    *   `policyAllowed`: a string array of paths permitted by policy.
    *   `policyRead`: a string array of paths permitted read-only by policy.
    *   `policyWrite`: a string array of paths explicitly permitted for writing.

*   Update `BwrapArgsOptions` in `bwrapArgsBuilder.ts`:
    *   Replace `workspace`, `allowedPaths`, `forbiddenPaths`, `additionalPermissions`, and `includeDirectories` with a single `resolvedPaths` field of type `ResolvedSandboxPaths`.
    *   Retain `workspaceWrite`, `networkAccess`, `maskFilePath`, and `isWriteCommand`.

*   Modify `buildBwrapArgs` in `bwrapArgsBuilder.ts`:
    *   Use `resolvedPaths.workspace.original` for bind-mounting the workspace directory.
    *   Use `resolvedPaths.workspace.resolved` to locate governance files, ensuring files at the resolved path are included if different from the original.
    *   Include a `--bind-try` entry for each path in `resolvedPaths.policyWrite` and `resolvedPaths.policyAllowed`.
    *   Deny each file path in `resolvedPaths.forbidden` by binding it to `/dev/null`.
    *   Mask each directory path in `resolvedPaths.forbidden` using `--tmpfs`.
    *   Apply forbidden path restrictions after allowed path bindings.
    *   Scan each directory in `resolvedPaths.globalIncludes` for secret files and protect them with a `--bind` entry.

*   Update `LinuxSandboxManager` in `packages/core/src/sandbox/linux/LinuxSandboxManager.ts`:
    *   Accept an `includeDirectories` option in the constructor alongside `workspace`.
    *   In `prepareCommand`, allow the command when `command` is `'__read'` and the target file is within `includeDirectories`. Ensure the resulting `args` array includes `'/bin/cat'` as the second-to-last element and the target file path as the last element.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.