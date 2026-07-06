## Description

The operation graph optimizer fails to eliminate conditional selection nodes in certain common patterns involving gated memory loads and type conversions.

The optimizer already handles the simple case where a conditional selection chooses between a gated load and a zero fallback value — it merges the condition into the load's gate and eliminates the conditional node. However, this optimization does not fire when:
1. The outer conditional has additional conditions beyond those already in the load's internal gate (e.g., the load is gated on condition A, and the outer conditional uses condition A AND condition B), **and**
2. The result of the conditional is subsequently cast to a different type.

In these cases, the optimizer leaves the conditional selection node in place rather than eliminating it, resulting in inefficient lowered code.

## Expected Behavior

- When the outer conditional's condition is a superset of the load's gate condition (it includes the gate condition plus additional terms), the optimizer should still eliminate the conditional and fold all conditions into the load's gate.
- This should work whether the gated load is in the "true" branch or the "false" branch of the conditional.
- When a type cast follows the conditional selection, the optimizer should eliminate the conditional and correctly propagate the type cast through to the resulting gated load.

## Why This Matters

Bounds-checked indexing patterns often produce this combination — a gated load inside a broader condition, followed by a precision conversion. Failing to optimize these patterns introduces unnecessary runtime overhead in otherwise clean conditional load sequences.
