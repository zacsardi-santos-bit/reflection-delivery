Implement a mechanism to enforce the presence of specific MCP servers in user configurations by defining them as required servers. Ensure these servers override any local configurations and maintain their defined settings.

*   Export the `applyRequiredServers` function from `packages/core/src/code_assist/admin/mcpUtils.ts`.
    *   Accept two parameters: a record of current `MCPServerConfig` entries and an optional record of `RequiredMcpServerConfig` entries.
    *   Return the original `mcpServers` and an empty `requiredServerNames` array if the required servers argument is undefined or empty.
    *   Inject required servers into the `mcpServers` record and return a `requiredServerNames` array with the names of injected servers.
    *   Default the trust value to true for required servers if not specified.
    *   Replace local server entries with required server configurations when names conflict.
    *   Preserve auth-related fields: `authProviderType`, `oauth`, `targetAudience`, and `headers`.
    *   Preserve tool filtering fields: `includeTools` and `excludeTools`.
    *   Ensure compatibility with allowlist-filtered servers.

*   Extend the `sanitizeAdminSettings` function in `packages/core/src/code_assist/admin/admin_controls.ts`.
    *   Parse `requiredMcpServers` from the `mcpConfigJson` string.
    *   Expose parsed data as `result.mcpSetting.requiredMcpConfig`.
    *   Map `mcpServers` and `requiredMcpServers` independently in the result.
    *   Leave `result.mcpSetting.mcpConfig.mcpServers` undefined if only `requiredMcpServers` are present.
    *   Sort `includeTools` and `excludeTools` arrays alphabetically within `requiredMcpServers`.

*   Extend the `setRemoteAdminSettings` method in `packages/cli/src/config/settings.ts`.
    *   Map the `requiredMcpConfig` field from remote admin MCP settings to `merged.admin.mcp.requiredConfig`.

*   Update the admin MCP configuration structure in `packages/cli/src/commands/mcp/list.ts` to include a `requiredConfig` field alongside the existing `config` field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.