Refactor the sandbox system to move per-execution security settings from the constructor to individual command requests. Implement a shared utility for validating and deduplicating path lists, ensuring paths are absolute and duplicates are removed while preserving order.

* Implement `sanitizePaths` function in `packages/core/src/services/sandboxManager.ts`:
    * Signature: `sanitizePaths(paths?: string[]): string[] | undefined`.
    * Return `undefined` if `paths` is `undefined`.
    * Return a deduplicated array preserving the first occurrence of each path.
    * Throw `Error` with message `'Sandbox path must be absolute: <path>'` for non-absolute paths.

* Define interfaces in `packages/core/src/services/sandboxManager.ts`:
    * `ExecutionPolicy` with fields: `allowedPaths?: string[]`, `networkAccess?: boolean`, `sanitizationConfig?: Partial<EnvironmentSanitizationConfig>`.
    * `GlobalSandboxOptions` with a required field: `workspace: string`.

* Modify `SandboxRequest` interface:
    * Rename `config` field to `policy` with type `ExecutionPolicy`.

* Update `WindowsSandboxManager` in `packages/core/src/services/windowsSandboxManager.ts`:
    * Constructor accepts `GlobalSandboxOptions`.
    * Use `spawnAsync('icacls', [path.resolve(workspace), '/setintegritylevel', 'Low'])` for workspace and each path in `req.policy.allowedPaths`.

* Update `LinuxSandboxManager` in `packages/core/src/sandbox/linux/LinuxSandboxManager.ts`:
    * Constructor accepts `GlobalSandboxOptions`.
    * Use `--bind-try` for non-workspace allowed paths.
    * Normalize paths to avoid workspace duplication.

* Update `MacOsSandboxManager` in `packages/core/src/sandbox/macos/MacOsSandboxManager.ts`:
    * Constructor accepts `GlobalSandboxOptions`.
    * Profile must include specific configurations and resolve symlinks.
    * Handle ENOENT by resolving the nearest existing parent directory.
    * Re-throw errors other than ENOENT during path resolution.

* Ensure environment sanitization uses `req.policy.sanitizationConfig`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.