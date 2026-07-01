## Description

When nushell passes arguments to external commands, shell-special characters in those argument strings are not being sanitized. Characters like semicolons, ampersands, and subcommand substitution patterns can be misinterpreted by the underlying subshell instead of being treated as literal text. This is both a correctness issue and a security concern — a string that should be passed verbatim to an external program could instead trigger unintended shell operations.

## Expected Behavior

- A string argument containing a semicolon should be received by the external command as a literal semicolon, not split into separate commands.
- A string argument containing an ampersand should arrive as a literal ampersand, not trigger background execution.
- A string argument containing subcommand substitution patterns should be received as literal text, not evaluated.
- These guarantees should hold even when the argument value is produced by evaluating another nushell expression — the sanitization should happen at the point where the argument is handed off to the external program.

## Why This Matters

Without proper sanitization, user-provided or pipeline-computed strings passed to external commands could inadvertently execute shell metacharacters. This is a shell injection vulnerability. Any string passed as an argument to an external command should always arrive as the exact literal string, regardless of what special characters it contains.
