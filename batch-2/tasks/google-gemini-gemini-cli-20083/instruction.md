Implement a system to preserve and utilize MCP tool annotations throughout the policy decision process. Ensure that annotations are retained on tool objects after discovery and are available for policy checks, allowing rules to match tools based on their declared behavior. Update the policy engine to support annotation-based criteria and adjust the tool exclusion mechanism to be annotation-aware.

*   Update the `DiscoveredMCPTool` class:
    *   Add an optional `toolAnnotations` parameter (type `Record<string, unknown>`) as the last constructor argument.
    *   Store `toolAnnotations` in a private field `_toolAnnotations` and provide a public getter `toolAnnotations` to access it.
    *   Ensure `_toolAnnotations` is accessible externally for scheduler logic.
    *   Set `isReadOnly` to `true` if `toolAnnotations` includes `readOnlyHint: true`.

*   Modify the `PolicyEngine` class:
    *   Update `check` method to accept an optional `toolAnnotations` parameter (type `Record<string, unknown> | undefined`) for annotation-based rule matching.
    *   Update `getExcludedTools` method to accept an optional `metadata` parameter (type `Map<string, Record<string, unknown>>`). Skip annotation-based rules if metadata is not provided.

*   Update policy rule handling:
    *   Ensure `PolicyRule` type includes an optional `toolAnnotations` field for annotation-based matching.
    *   Ensure rules require both `toolName` pattern and `toolAnnotations` to match for exclusion.

*   Update `ToolConfirmationRequest` type to include an optional `toolAnnotations` field. Ensure `MessageBus` forwards these annotations to `policyEngine.check()`.

*   Modify `checkPolicy` function:
    *   Extract `toolAnnotations` from `DiscoveredMCPTool` and pass it to `policyEngine.check()`.
    *   Pass `undefined` for non-MCP tools.

*   Update `McpClient`:
    *   Stop calling `policyEngine.addRule()` for tools with `readOnlyHint: true`.
    *   Store full tool annotations on `DiscoveredMCPTool` instances.

*   Update `plan.toml` policy file:
    *   Add a rule for MCP tools with `toolName = "*__*"`, `toolAnnotations = { readOnlyHint = true }`, `decision = "ask_user"`, and `priority = 70`.
    *   Ensure MCP tools without annotations or with `readOnlyHint: false` receive a `DENY` decision in plan mode.
    *   Ensure non-MCP tools are denied in plan mode, even with `readOnlyHint: true`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.