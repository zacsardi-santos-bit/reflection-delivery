Implement safety controls for sensitive browser operations in the browser automation sub-agent. Add configuration options for user confirmation and file upload blocking, and ensure the permission system correctly matches tool names. Update the `createBrowserAgentDefinition` and `createMcpDeclarativeTools` functions to support these features.

Requirements:

*   Update `createBrowserAgentDefinition` in `packages/core/src/agents/browser/browserAgentFactory.ts`:
    *   Register policy rules with decision `ASK_USER` and priority `999` for `mcp_browser_agent_fill`, `mcp_browser_agent_upload_file`, and `mcp_browser_agent_evaluate_script` when `agents.browser.confirmSensitiveActions` is `true`.
    *   Always register a policy rule for `mcp_browser_agent_fill`.
    *   Do not register a policy rule for `mcp_browser_agent_upload_file` when `agents.browser.confirmSensitiveActions` is `false` or unset.
    *   Register `ALLOW` rules with priority `PRIORITY_SUBAGENT_TOOL` for read-only tools (`take_snapshot`, `take_screenshot`, `list_pages`) using the format `mcp_browser_agent_<toolname>`.

*   Update `createMcpDeclarativeTools` in `packages/core/src/agents/browser/mcpToolWrapper.ts`:
    *   Accept a fourth parameter `blockFileUploads` (boolean, default `false`).
    *   Return a result with `error` and `llmContent` containing "File uploads are blocked" when `blockFileUploads` is `true` and `upload_file` is invoked, without calling `browserManager.callTool`.
    *   Execute `upload_file` normally when `blockFileUploads` is `false`.

*   Update `check` method in `packages/core/src/policy/policy-engine.ts`:
    *   Resolve unqualified short tool names to fully qualified equivalents when `serverName` is provided.
    *   Ensure `check({ name: 'tool' }, 'my-server')` matches rules registered for `mcp_my-server_tool` and returns the matching rule's decision.

*   Export `PRIORITY_SUBAGENT_TOOL` from `packages/core/src/policy/types.ts` and use it for `ALLOW` rules for read-only tools.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.