Implement a mechanism to sanitize shell-special characters in nushell when passing arguments to external commands. Ensure that characters like semicolons, ampersands, and subcommand substitution patterns are treated as literal text and not misinterpreted by the underlying shell.

*   Ensure that when a string containing a semicolon is passed to an external command, it is received as a literal semicolon.
    *   Example: Passing 'a;b' should result in the external command receiving 'a;b'.
*   Ensure that when a string containing an ampersand is passed to an external command, it is received as a literal ampersand.
    *   Example: Passing 'a&b' should result in the external command receiving 'a&b'.
*   Ensure that when a string containing subcommand substitution syntax is passed to an external command, it is received as literal text.
    *   Example: Passing '$(ls)' should result in the external command receiving '$(ls)'.
*   Apply shell argument sanitization even when the argument is the result of evaluating another nushell expression.
    *   Example: If a nushell expression produces 'a;&$(hello)', it should be passed literally to the external command.
*   Implement these sanitization behaviors specifically for non-Windows platforms.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.