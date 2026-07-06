Implement a lazy-loading mechanism for forbidden paths in the sandbox security system, ensuring that paths are resolved only when needed. Update path conflict resolution to prioritize forbidden paths over allowed paths and handle case-insensitive filesystems correctly.

*   Modify the `sanitizePaths` function:
    *   Return an empty array for undefined, null, or empty array inputs.
    *   Deduplicate paths case-insensitively on 'win32' and 'darwin', and case-sensitively on 'linux'.
    *   Export from `packages/core/src/services/sandboxManager.ts`.

*   Implement the `getPathIdentity` function:
    *   Normalize paths by stripping trailing slashes and normalizing separators.
    *   Lowercase paths on 'win32' and 'darwin', preserve case on 'linux'.
    *   Export from `packages/core/src/services/sandboxManager.ts`.

*   Implement the `resolveSandboxPaths` function:
    *   Accept a sandbox options object with `workspace` and optional `forbiddenPaths` as an async function.
    *   Return a Promise of `{ allowed: string[]; forbidden: string[] }`.
    *   Filter the workspace path from the allowed paths list.
    *   Remove paths from the allowed list if they appear in the forbidden list.
    *   Perform case-insensitive conflict detection on 'darwin' and 'win32'.
    *   Export from `packages/core/src/services/sandboxManager.ts`.

*   Export the `SandboxRequest` type from `packages/core/src/services/sandboxManager.ts`.

*   Update `forbiddenPaths` option in sandbox manager constructors:
    *   Accept an async function `() => Promise<string[]>` in `LinuxSandboxManager`, `MacOsSandboxManager`, and `WindowsSandboxManager`.
    *   Ensure `MacOsSandboxManager` passes an empty array to `buildSeatbeltProfile` when no forbidden paths are configured.

*   Update `createSandboxManager` function:
    *   Accept `forbiddenPaths` as an async function `() => Promise<string[]>` in its options parameter.

*   Ensure `buildSeatbeltProfile` requires `allowedPaths` and `forbiddenPaths` as non-optional parameters.

*   Add `getSandboxForbiddenPaths` method to the `Config` class:
    *   Return a Promise of string array.
    *   Call `fileService.getIgnoredPaths()` only when first invoked, not during initialization.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.