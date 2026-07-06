## Description

Several type-checking issues affect the expression language in Nussknacker scenarios. When users write conditional (ternary) expressions where the two branches have different but related types, the type checker sometimes reports an error or produces an unusable "empty" type instead of computing a reasonable common type. Similarly, comparing two record literals or a record with a map variable is sometimes rejected as a type error even though such a comparison is valid at runtime.

## Expected Behavior

- A conditional expression where the two branches have incompatible types should produce an unresolved generic type rather than being rejected as invalid. When both branches are record types, the result should be a merged record containing all fields from both branches. When one branch is a record and the other is a generic map, the result should be typed accordingly.
- Comparing records with different field sets (or a record against a generic map) in an equality check should be valid and should evaluate correctly at runtime.
- The API for finding the common supertype of two types should expose two clear, named strategies rather than requiring callers to manually construct strategy objects with multiple configuration flags. A "default" strategy that falls back gracefully, and an "intersection" strategy for strict structural matching, should be the standard ways to perform supertype lookups.
- The expression type-checking component should no longer require explicit configuration of a supertype-finding strategy at initialization time.
- The strict type checking configuration option should be removed, as the behavior it controlled is now handled automatically by the appropriate named strategy.

## Why This Matters

Users were seeing spurious type mismatch validation errors when comparing records that had fields with mixed types (e.g., simple classes alongside nested records). They were also unable to use ternary operators with branches of different structural types, receiving an error or an unusable empty type that would fail any downstream type check. These fixes make the expression language more permissive in the right places while keeping strict checks where they matter (e.g., arithmetic operations).
