Implement a more readable permission dialog by separating the display title from contextual details for tool invocations. Update the tool invocation classes and the session processing logic to handle these changes.

*   Extend the `ToolInvocation` interface in `packages/core/src/tools/tools.ts`:
    *   Add two optional methods: `getDisplayTitle?(): string` and `getExplanation?(): string`.
*   Update the `BaseToolInvocation` abstract class in `packages/core/src/tools/tools.ts`:
    *   Implement default methods: `getDisplayTitle()` returns the same value as `getDescription()`.
    *   `getExplanation()` returns an empty string.
*   Modify the `ShellToolInvocation` class in `packages/core/src/tools/shell.ts`:
    *   Override `getDisplayTitle()` to return only `params.command`.
    *   Override `getExplanation()` to return contextual details formatted based on the presence of `dir_path`, `description`, and `is_background`.
        *   Format: '[in {dir_path}] ({description}) [background]' or '[current working directory {cwd}]'.
*   Modify the `DiscoveredMCPToolInvocation` class in `packages/core/src/tools/mcp-tool.ts`:
    *   Override `getDisplayTitle()` to return `params['command']` if it exists, otherwise use `displayName`.
    *   Override `getExplanation()` to return a JSON-stringified `params` if the length is 500 characters or fewer.
        *   If longer, return '[Payload omitted due to length with parameters: key1, key2, ...]' listing up to 5 keys.
*   Update the `Session` class in `packages/cli/src/acp/acpClient.ts`:
    *   Use `getDisplayTitle()` for the `toolCall.title` in `requestPermission`.
    *   Set `toolCall.content` to an empty array `[]`.
    *   If `getExplanation()` returns a non-empty string, send a `sessionUpdate` with `sessionUpdate='agent_thought_chunk'` and `content={ type: 'text', text: <explanation> }` before the permission check.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.