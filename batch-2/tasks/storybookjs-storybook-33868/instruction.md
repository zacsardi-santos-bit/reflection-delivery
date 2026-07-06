Implement a utility function to escape special characters in strings for safe embedding in generated code. This function should handle backslashes, single quotes, double quotes, backticks, and dollar signs, ensuring they are properly escaped to prevent syntax errors or code injection.

*   Create a new file at `code/core/src/core-server/utils/safeString.ts`.
*   Implement and export the `escapeForTemplate` function as a named export.
    *   Signature: `escapeForTemplate(str: string): string`
    *   Purpose: Escape special characters in a string for safe embedding in TypeScript/JavaScript code.
*   Ensure the function:
    *   Escapes backslashes by replacing each with two backslashes.
    *   Escapes backticks by prepending a backslash.
    *   Escapes dollar signs by prepending a backslash.
    *   Escapes single quotes by prepending a backslash.
    *   Escapes double quotes by prepending a backslash.
    *   Correctly escapes all special characters in a single pass without double-escaping.
    *   Returns unchanged strings for file paths without special characters.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.