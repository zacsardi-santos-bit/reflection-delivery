Implement a secure environment variable redaction system and a Linux-specific sandbox backend. Ensure that environment variable redaction is always enforced, and develop a new Linux sandbox backend for OS-level isolation using Bubblewrap.

*   Implement `getSecureSanitizationConfig` in `packages/core/src/services/environmentSanitization.ts`:
    *   Always set `enableEnvironmentVariableRedaction` to true.
    *   Accept optional `requestedConfig` and `baseConfig` parameters.
    *   Merge `allowedEnvironmentVariables` and `blockedEnvironmentVariables` from both configs.
    *   Filter out variables from the merged allowed list that match `NEVER_ALLOWED_ENVIRONMENT_VARIABLES` or `NEVER_ALLOWED_NAME_PATTERNS`.
    *   Deduplicate both `allowedEnvironmentVariables` and `blockedEnvironmentVariables` lists.

*   Update `NoopSandboxManager`:
    *   Use `getSecureSanitizationConfig` in `prepareCommand`.
    *   Ensure redaction cannot be disabled via request config.
    *   Redact variables matching `NEVER_ALLOWED_NAME_PATTERNS` even if explicitly allowed.
    *   Allow safe variables listed in `sanitizationConfig.allowedEnvironmentVariables`.

*   Develop `LinuxSandboxManager` in `packages/core/src/sandbox/linux/LinuxSandboxManager.ts`:
    *   Implement the `SandboxManager` interface.
    *   Constructor must accept an `options` object with a required `workspace` string and optional `allowedPaths` string array.
    *   `prepareCommand` must return a `SandboxedCommand` with `program` set to 'bwrap' and `args` in the specified order.
    *   Append `--bind`, `path`, `path` for each `allowedPaths` entry not equal to the `workspace`.

*   Update `createSandboxManager` in `packages/core/src/services/sandboxManager.ts`:
    *   Accept a `workspace` parameter and update the signature to `createSandboxManager(sandboxingEnabled: boolean, workspace: string): SandboxManager`.
    *   Return `NoopSandboxManager` when `sandboxingEnabled` is false.
    *   Return `LinuxSandboxManager` when `sandboxingEnabled` is true and the platform is Linux.
    *   Return `LocalSandboxManager` when `sandboxingEnabled` is true and the platform is not Linux.

*   Ensure `LocalSandboxManager` is exported from `packages/core/src/services/sandboxManager.ts`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.