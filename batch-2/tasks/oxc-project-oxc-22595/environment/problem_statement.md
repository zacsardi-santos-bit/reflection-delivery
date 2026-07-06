## Description

The minifier's dead code elimination is less aggressive than it should be for certain patterns involving well-known pure constructors. Specifically, when the optimization loop folds string literal concatenations or inlines single-use variables, the resulting constructor calls may now qualify for a "pure" (no side effects) annotation — but they don't receive it, because the pure-flag check only runs once during an early normalization phase before the loop begins.

## Expected Behavior

- When a constructor call receives a folded string argument (e.g., two string literals concatenated together become a single literal), the constructor should be re-evaluated for purity and annotated accordingly if it qualifies.
- When a single-use variable holding a pure constructor call is inlined into another constructor's arguments, both the outer and inner constructors should be evaluated and annotated as pure if they qualify.
- The optimization should be fully idempotent — running it multiple times on already-optimized code should not change the output.

## Why This Matters

Without this re-evaluation, downstream optimizations (such as dead code elimination) cannot see these expressions as safe to remove. Code that could be eliminated or simplified remains in the output, leading to larger bundle sizes and missed optimization opportunities.
