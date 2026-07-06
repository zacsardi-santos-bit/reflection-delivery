Update the Gemini CLI's auto-edit approval mode to automatically approve shell commands with output redirection, while still requiring manual approval for commands without redirection. Implement the necessary logic changes and export the shell tool name as a constant for identification purposes.

*   Implement logic to handle shell commands in AUTO_EDIT mode:
    *   Automatically approve shell commands containing output redirection operators (e.g., '>') when in AUTO_EDIT mode, as these are considered file-writing operations.
    *   Ensure shell commands without output redirection still require manual approval, maintaining the current behavior for non-file-writing operations.

*   Export the shell tool name as a constant:
    *   Define and export a constant named `SHELL_TOOL_NAME` from the `packages/core/src` directory.
    *   Ensure `SHELL_TOOL_NAME` is a string that holds the registered name of the shell execution tool.
    *   Use this constant in the approval mode filtering logic to differentiate shell tool calls from other tool types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.