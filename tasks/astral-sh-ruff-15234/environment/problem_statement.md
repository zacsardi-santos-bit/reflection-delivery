## Description

The lint rule that detects unnecessary rounding of integer values is producing false positives. It currently flags cases where the precision argument to the rounding function is not known at analysis time — including variables, expressions, or values that might be negative — even though in those situations the rounding operation may not actually be a no-op.

## Expected Behavior

- Rounding calls should **not** be flagged when the precision argument is a negative value. Negative precision changes the result even for integers (e.g., rounding to the nearest ten or hundred), so the call is not unnecessary.
- Rounding calls should **not** be flagged when the precision argument is a variable or an arithmetic expression whose value cannot be statically determined, since it may or may not result in a change to the value.
- Rounding calls should **still** be flagged when no precision is given, when precision is explicitly absent, or when a non-negative integer literal precision is provided — those are the only cases where rounding an integer is genuinely provably unnecessary.

## Why This Matters

The false positives cause the linter to suggest removing rounding calls that actually do have an effect on the result. This can lead to silent correctness bugs if the suggestion is applied. The rule should be conservative and only flag calls where it can be 100% certain the rounding has no effect.
