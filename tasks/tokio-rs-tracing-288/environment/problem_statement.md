## Description

Currently, when using the tracing logging macros to emit an event with both structured fields and a human-readable message, the fields must be wrapped in curly braces. This is unnecessarily verbose and makes the syntax feel inconsistent compared to simpler cases that don't require braces.

The same issue applies to all the level-specific convenience macros, and to cases where local variable shorthands (including those with display or debug formatting) are used alongside a message.

## Expected Behavior

- All tracing macros should accept structured fields directly before a format string message without requiring curly braces around the fields.
- Local variable shorthand syntax (with or without display/debug formatting sigils) should work inline alongside a message, without needing braces.
- Trailing commas after the final argument should continue to be accepted.
- The message format string should continue to support format arguments, named arguments, and plain string literals.
- Invocations with an explicit target and invocations that have fields but no message at all should also work without braces.

## Why This Matters

Removing the requirement for braces makes the macro syntax more ergonomic and consistent. Developers shouldn't need to know about two different syntaxes (with and without braces) depending on whether they're also providing a message. The brace-free syntax is more readable, easier to type, and aligns better with how developers naturally think about structured logging.
