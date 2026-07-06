## Description

The lint rule that flags functions with missing explicit return statements has a gap: it does not detect the pattern where a function's conditional else branch ends with a context manager block that lacks an explicit return.

For example, a function that returns a value in its if branch, but whose else branch only uses a context manager block (without any explicit return inside it), should be flagged — but currently is not. The function implicitly returns nothing through that branch, which is the exact behavior the rule is meant to catch.

## Expected Behavior

- A function whose else branch ends with a context manager block that does not explicitly return should trigger the implicit-return lint rule.
- The diagnostic should point to the last statement inside the context manager block.
- A suggested fix (marked as unsafe) should offer to insert an explicit return after the last statement in the context manager block, at the correct indentation level.

## Why This Matters

This is a correctness gap in the rule's coverage. Developers relying on this rule to enforce explicit returns may miss cases involving context managers in conditional branches, leading to silent implicit returns in code they believe has been fully checked.
