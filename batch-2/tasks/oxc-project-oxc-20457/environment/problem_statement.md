## Description

The unused-variables lint rule produces a false positive for a common iterative programming pattern: when a variable is repeatedly updated by calling a method on its own current value inside a loop. In this pattern, the previous value of the variable is read to produce the new value on each iteration, so the variable is genuinely being used. However, the rule currently flags such variables as "assigned but never used," which is incorrect.

## Expected Behavior

- A variable that is reassigned inside a loop body by passing its own current value through a method call should be treated as used and should NOT generate a lint warning.
- A variable that is reassigned via the same self-referential method call pattern but outside any loop (where the result is never subsequently read) should continue to be flagged as unused.

## Why This Matters

This false positive causes valid code to be incorrectly rejected. Patterns where an object accumulates changes through iterative method calls — a common technique — are perfectly valid uses of a variable. Developers relying on this lint rule to find genuinely unused variables will instead encounter noise that undermines their trust in the rule.
