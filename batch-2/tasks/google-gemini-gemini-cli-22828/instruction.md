Implement sanitization utilities and update subagent activity handling to address security and usability issues in the CLI tool.

*   Implement the `sanitizeErrorMessage` function in `packages/core/src/utils/agent-sanitization-utils.ts`:
    *   Redact complete PEM blocks by replacing them with '[REDACTED_PEM]'.
    *   Ensure resilience against ReDoS attacks by processing incomplete PEM-like patterns in under 50 milliseconds.
    *   Redact sensitive key-value pairs by replacing sensitive values with '[REDACTED]'.
    *   Redact space-separated sensitive keyword constructs with tokens of 8 or more characters by replacing them with '[REDACTED]'.

*   Implement the `sanitizeToolArgs` function in `packages/core/src/utils/agent-sanitization-utils.ts`:
    *   Recursively traverse objects, replacing values of sensitive field names with '[REDACTED]'.
    *   Handle arrays by replacing sensitive key-value pattern matches with '[REDACTED]'.

*   Implement the `sanitizeThoughtContent` function in `packages/core/src/utils/agent-sanitization-utils.ts`:
    *   Redact token-like sensitive patterns by replacing them with '[REDACTED]'.

*   Update subagent activity handling:
    *   For `BrowserAgentInvocation`, ensure each new THOUGHT_CHUNK event overwrites the previous thought entry in `SubagentProgress.recentActivity`.
    *   For `LocalSubagentInvocation`, ensure each new THOUGHT_CHUNK event overwrites the previous thought entry in `SubagentProgress.recentActivity`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.