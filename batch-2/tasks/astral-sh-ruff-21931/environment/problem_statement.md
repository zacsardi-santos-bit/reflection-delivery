## Description

The lint rule that detects the combination of a yield expression and a value-bearing return statement in generator functions is producing false positive warnings for a legitimate and common pattern in testing framework hook wrappers. When a function is explicitly registered as a hook wrapper using the appropriate wrapper-enabling option on the hook registration decorator, it is both expected and required to yield control to the wrapped hook and then return the hook's result. The lint rule does not recognize this pattern and incorrectly flags these functions as bugs.

## Expected Behavior

- Generator functions registered as hook wrappers via the wrapper-enabling decorator option should not trigger the lint rule — returning a value after yielding is the intended protocol for these wrappers.
- Generator functions that use the same hook registration decorator *without* the wrapper-enabling option should still trigger the lint rule, since returning a value in that context is not part of the protocol.
- Generator functions registered as test fixtures that use both a yield expression and a value-bearing return should still trigger the lint rule.

## Why This Matters

Developers writing plugins or hook wrappers are seeing spurious lint warnings for code that is architecturally correct. This creates noise in CI pipelines and may lead developers to add suppression comments or restructure perfectly valid code unnecessarily. The rule should understand the hook wrapper protocol well enough to distinguish intentional patterns from actual bugs.
