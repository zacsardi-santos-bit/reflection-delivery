Correct the diagnostic message in the lint rule that flags the usage of Node.js built-in modules to ensure proper subject-verb agreement. Update the message to be grammatically correct and consistently applied across all types of module imports.

*   Update the diagnostic message to: 'Using Node.js modules is forbidden.'
    *   Ensure the message uses the singular verb form 'is'.
*   Apply the corrected message to all detected violations:
    *   CommonJS require() calls.
    *   ES Module static imports.
    *   ES Module dynamic imports.
*   Ensure the corrected message triggers for:
    *   Bare module names (e.g., 'fs', 'path').
    *   Module names with the node: protocol prefix (e.g., 'node:fs', 'node:path').

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.