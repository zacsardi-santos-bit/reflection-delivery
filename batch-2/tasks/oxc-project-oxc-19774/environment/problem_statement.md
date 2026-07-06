## Description

The lint rule for detecting unused declarations currently applies its auto-fix behavior in a single, fixed mode: it always emits suggestion-style fixes for both unused imports and unused variables. There is no way to configure whether fixes are applied automatically or as suggestions, and no way to disable fixes for one category while keeping them enabled for another.

## Expected Behavior

- Users should be able to independently control the fix behavior for unused imports and unused variables
- Each category should support three modes:
  - No fix at all (just report the diagnostic without any code modification offer)
  - Suggestion-style fix (current behavior — user manually accepts the change)
  - Automatic fix (tool or editor can apply the change directly without user confirmation)
- When only one sub-option is configured, the other should independently default to suggestion-style (preserving the current behavior for the unconfigured category)
- Invalid configuration values should be rejected with a clear error

## Why This Matters

Editor integrations and CI tooling often need to treat different categories of unused declarations differently. For example, automatically removing unused imports is generally safe and desirable in many workflows, while automatically deleting unused variables may be riskier. Today there is no way to express this distinction. Giving users fine-grained control over fix modes enables more powerful and safer automated code cleanup workflows.
