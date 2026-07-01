Implement a new lint rule to detect and warn about unnecessary escape sequences in JavaScript and TypeScript string literals. Ensure the rule provides a safe fix for removing these superfluous backslashes. Update the existing rule for regular expressions to maintain consistent warning severity.

*   Add a new lint rule named `noUselessEscapeInString` in the `nursery` category.
    *   Detect unnecessary escape sequences in single-quoted, double-quoted, and untagged template literals.
    *   Exclude tagged template literals and JSX attribute string values from this check.
    *   Do not flag legitimate escape sequences such as `\0`, `\n`, `\t`, `\r`, `\uXXXX`, `\xXX`, and the enclosing quote character.
    *   Handle multibyte unicode characters correctly, flagging unnecessary escapes.
*   Emit a warning-level diagnostic with:
    *   Message: "The character doesn't need to be escaped."
    *   Note: "Only quotes that enclose the string and special characters need to be escaped."
    *   Severity displayed as `!` (warning).
    *   Safe fix labeled "Unescape the character." to remove the unnecessary backslash.
*   Ensure the diagnostic text range covers only the escaped character, accounting for multibyte characters.
*   Update the existing `noUselessEscapeInRegex` rule:
    *   Change the severity from `Severity::Error` to `Severity::Warning` in the `declare_lint_rule!` macro.
    *   Ensure the diagnostic displays `!` instead of `×`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.