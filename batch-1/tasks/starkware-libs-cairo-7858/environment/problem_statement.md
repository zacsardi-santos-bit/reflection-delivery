## Description

The compiler's semantic type inference for code blocks does not correctly handle all cases where a block always diverges and never produces a value. Specifically, there are several scenarios where a block should be assigned the "never" type but is instead being given the unit type. This is incorrect and can lead to downstream type inference problems.

## Expected Behavior

- A block whose last meaningful statement is a loop continuation (e.g., inside a loop body) should have type "never".
- A block whose last meaningful statement is a call to a function that itself never returns should have type "never".
- A block containing a diverging statement followed by item declarations (such as constant definitions) should have type "never" — the trailing items should not affect the block's type.
- A block containing only item declarations or that is empty should continue to have the unit type.

## Current Behavior

Blocks containing only a loop continuation, or ending with a call to a never-returning function, are being typed as unit instead of "never". Additionally, when a diverging statement is followed by item declarations, the presence of those items causes the type to be incorrectly inferred as unit.

## Why This Matters

Correct "never" type propagation is important for accurate type checking. When control flow is guaranteed to diverge, the compiler should reflect that in the block's type so that subsequent type inference and analysis work correctly.
