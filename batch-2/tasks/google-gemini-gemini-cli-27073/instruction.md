Implement security and correctness improvements for the a2a-server package by modifying how settings are loaded and policy configurations are constructed. Ensure workspace settings are only applied if the workspace is trusted, and initialize the policy engine with default rules in non-YOLO mode.

*   Update `loadSettings` function in `packages/a2a-server/src/config/settings.ts`:
    *   Accept an optional second boolean parameter `trustOverride`.
    *   If `trustOverride` is true, load workspace settings unconditionally.
    *   If `trustOverride` is false or undefined, call `checkPathTrust` from `@google/gemini-cli-core` to verify workspace trust.
    *   If `checkPathTrust` returns `{ isTrusted: false }`, do not merge workspace settings; return only user-level settings.
    *   If `checkPathTrust` returns `{ isTrusted: true }`, merge workspace settings but protect `adminPolicyPaths` and `policyPaths` from being overridden by workspace settings.

*   Update `loadConfig` function in `packages/a2a-server/src/config/config.ts`:
    *   Call `createPolicyEngineConfig` with a `policySettings` object, `ApprovalMode.DEFAULT`, `undefined` for `defaultPoliciesDir`, and `true` for `interactive`.
    *   Construct `policySettings.tools` by merging V1 and V2 tool settings:
        *   Use V1 `allowedTools` if present, otherwise use V2 `tools.allowed`.
        *   Use V2 `tools.core` and `tools.exclude`.
    *   Include `mcpServers`, `policyPaths`, and `adminPolicyPaths` from the settings object in `policySettings`.

*   Ensure `createPolicyEngineConfig` in `@google/gemini-cli-core`:
    *   Accepts parameters `(settings, mode, defaultPoliciesDir, interactive)`.
    *   Returns an object with `rules` and `checkers` arrays.
    *   In default (non-YOLO) mode, ensure `rules` array contains at least one rule with `toolName` set to `'read_file'` and `decision` set to `PolicyDecision.ALLOW`.

*   Export `createPolicyEngineConfig` and `checkPathTrust` from `@google/gemini-cli-core`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.