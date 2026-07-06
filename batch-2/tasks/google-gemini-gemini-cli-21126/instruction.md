Implement the function `recordToolCallInteractions` to accurately process telemetry data for accepted file edits. Ensure the function correctly calculates line counts, includes programming language information, and filters out non-edit tool interactions.

*   Update `recordToolCallInteractions` in `packages/core/src/code_assist/telemetry.ts` to:
    *   Calculate `acceptedLines` as the sum of `model_added_lines` and `model_removed_lines` for accepted edit tool calls ('replace' or 'write_file').
    *   Detect the programming language from the file extension in `file_path` and include it in the interaction record:
        *   Map '.ts' to 'TypeScript'.
        *   Map '.py' to 'Python'.
    *   Record an interaction only for tool calls with the name 'replace' or 'write_file'. Do not record any interaction for other tool names.

*   Ensure the function signature remains:
    *   `recordToolCallInteractions(config: Config, toolCalls: CompletedToolCall[]): Promise<void>`

*   Do not record any interaction event for non-edit tools, removing any previous UNKNOWN interactions for these cases.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.