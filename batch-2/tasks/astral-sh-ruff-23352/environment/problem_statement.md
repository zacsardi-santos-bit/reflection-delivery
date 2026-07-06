## Description

When a user configures the linter to require a specific import from the standard collections module AND simultaneously enables a rule that mandates that same import always be aliased, the two rules directly contradict each other. One rule demands the import be present (unaliased), while the other flags any unaliased usage as a violation. Currently, the linter does not detect this contradiction — it either silently produces confusing behavior or leads to an auto-fix loop where applying one suggested fix immediately triggers the other rule.

## Expected Behavior

- When both rules are active at the same time with conflicting settings, the linter should detect the contradiction early and report a clear, actionable error before any linting takes place. The error should identify the two conflicting rules by name and code, explain the nature of the conflict, and offer concrete resolution steps (either alias the required import or disable the conflicting rule).
- When the required import is already configured with the alias that the other rule demands, no conflict should be reported and linting should proceed normally.
- When the rule that requires aliasing is not enabled, the required-import rule should work normally without triggering any conflict detection.

## Why This Matters

Without this detection, users enabling both configurations face a confusing situation where auto-fixes from one rule undo the requirements of another. Surfacing this contradiction as an explicit error with actionable guidance makes the tool significantly more user-friendly and prevents wasted debugging time.
