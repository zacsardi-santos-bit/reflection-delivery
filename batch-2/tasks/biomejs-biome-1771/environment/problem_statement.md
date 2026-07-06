## Description

The linter currently has a narrow rule that only flags one specific method call on the console object. Many teams want to disallow all console usage in production code — not just one particular method — because any console call can leak debug output or sensitive information to end users.

We need a new, more comprehensive lint rule in the nursery group that flags any method call made on the global console object, regardless of which method is used. This includes common methods like logging, warnings, errors, and tables, as well as any other methods that may exist or be added in the future.

## Expected Behavior

- Any method call on the global console object should be flagged with a warning.
- Calls that access console through the global object indirectly should also be flagged.
- When code defines its own local variable named "console" (shadowing the global), the rule should recognize this as valid and not produce a diagnostic.
- The rule should offer an automatic unsafe fix that removes the offending statement entirely.
- The rule should not be enabled by default (not recommended), so teams can opt in.

## Why This Matters

The existing narrower rule was insufficient for teams that want a zero-console policy. A comprehensive rule covering all console access — with local-scope awareness — removes friction for these teams and avoids the false positives that come from shadowing the global console with a custom logger or mock.
