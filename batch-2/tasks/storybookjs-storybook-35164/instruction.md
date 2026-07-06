Implement a new function to fetch both the tool list and workflow instructions from a Storybook instance during the initialization handshake. Update the help text output to include these instructions and improve section headings.

*   Export a new function `listMcpToolsWithServerMetadata` from `code/core/src/cli/ai/mcp/client.ts`.
    *   Accept the same parameters as `listMcpTools`: a `StorybookInstanceRecord` and an optional fetch implementation.
    *   Return a Promise resolving to an object with `tools` (array of tool descriptors) and `serverMetadata` (object that may contain an `instructions` string field).

*   In `listMcpToolsWithServerMetadata`:
    *   Extract the `instructions` field from the `result` object in the initialize handshake response.
    *   Trim leading and trailing whitespace from `instructions`.
    *   Handle both JSON content-type and SSE (text/event-stream) initialize responses.
    *   Return `serverMetadata.instructions` only if it is a non-empty, non-whitespace string.
    *   Return `serverMetadata` as an empty object `{}` if `instructions` is undefined, empty, whitespace-only, or a non-string type.
    *   Ensure successful completion even if the initialize response is malformed, returning tools from the subsequent tools/list request and `serverMetadata` as `{}`.
    *   Explicitly cancel the response body stream if the initialize handshake response has a non-successful HTTP status.

*   Update the `buildStorybookCommandsHelp` function in `code/core/src/cli/ai/mcp/run-tool.ts`:
    *   Use `listMcpToolsWithServerMetadata` to fetch both tools and server metadata.
    *   Change the section header to `'Storybook help from the Storybook running at <url>:'`.
    *   Include a `# Storybook commands` section heading before the tool list.
    *   When `serverMetadata.instructions` is a non-empty string, include a `# Storybook workflow instructions` section before the `# Storybook commands` section.
    *   Format the output as: header line, blank line, `# Storybook workflow instructions`, blank line, instructions text, blank line, `# Storybook commands`, blank line, indented tool list, blank line, help footer.

*   Ensure `listMcpTools` continues to return only the tool descriptors array without changes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.