## Description

The match-template macro in this codebase lets developers write a single template match arm that gets expanded into many arms by substituting a list of type identifiers. This is useful for avoiding repetitive boilerplate in code that branches on types.

However, the macro currently only accepts exactly one match arm — the template arm. In practice, real match expressions almost always need a wildcard catch-all arm (for example, a branch that panics with "unreachable") to satisfy exhaustiveness requirements or to handle unexpected values. Without support for a wildcard arm, developers cannot use the macro in these situations.

## Expected Behavior

- The macro should accept an optional catch-all wildcard arm after the template arm in the input.
- When a wildcard arm is present, it should be preserved unchanged in the expanded output, appended after all the substituted arms.
- When no wildcard arm is present, behavior should remain exactly as before.

## Why This Matters

This limitation forces developers to either forgo using the macro or manually reconstruct the match expression, defeating the purpose of the template macro. Supporting a wildcard arm makes the macro usable in the real code patterns that this project relies on.
