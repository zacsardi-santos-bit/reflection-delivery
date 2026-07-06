## Description

The template engine currently supports only bracket-based delimiter syntax for blocks and comments. Jinja2 and similar template engines also support a line-oriented syntax where an entire line beginning with a configured prefix character is treated as a block statement, and a separate prefix character can mark the rest of any line as a comment that is completely removed from the output. This feature is missing from the engine, making it impossible to write templates in the cleaner, line-oriented style.

## Expected Behavior

- Developers should be able to configure a "line statement prefix" so that lines starting with that character (optionally preceded by whitespace) are treated as block control statements — equivalent to using standard block delimiter pairs.
- Developers should be able to configure a "line comment prefix" so that any occurrence of that string on a line removes everything from that point to the end of the line (including the trailing newline).
- Both options should be configurable through the existing syntax configuration builder.
- Line statements should support multi-line expressions when brackets or parentheses are not yet closed.
- When the line statement prefix appears mid-line (after other non-whitespace content), it must NOT be mistakenly treated as a line statement.
- Line comments should also work as trailing annotations at the end of any line, not only at the start.

## Why This Matters

This feature allows templates to look cleaner and more script-like, especially when there are many control-flow constructs. It is a well-known feature in Jinja2 that users migrating to this engine may miss or expect.
