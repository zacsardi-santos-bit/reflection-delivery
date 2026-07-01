## Description

The authorization system currently supports an optimized fast-path evaluation for tuple-to-userset (TTU) relationships that include set operations (union, intersection, exclusion). However, a similar optimization does not yet exist for plain userset relationships that include set operations. This means that when an authorization model defines userset relationships using combined operations (e.g., a group's membership determined by union or exclusion of sub-relations), the system falls back to the slower general evaluation path even when a faster route is available.

Additionally, an internal component responsible for mapping relationship tuples in the graph resolver has an inconsistently named identifier that should be cleaned up for clarity and consistency with the rest of the codebase.

## Expected Behavior

- The internal tuple mapper concept should be renamed to use a cleaner, more consistent name throughout the graph resolution code.
- A new analysis capability should be added to the type system that can determine whether a userset relationship is eligible for fast-path optimization (specifically, a "weight-2" analysis). This analysis should:
  - Return true (eligible) for userset relationships involving direct assignments, computed relations, union, intersection, and exclusion/difference set operations, as well as conditional relations.
  - Return false (not eligible) for recursive/self-referencing usersets, relations that involve public/wildcard membership, tuple-to-userset chains, or nested userset-as-member patterns.
- Authorization check operations should correctly evaluate access for users referenced through complex algebraic combinations of set operations in userset relationships, ensuring excluded users are denied and eligible users are granted access.

## Why This Matters

This optimization extends the existing fast-path improvements to cover userset relationships with set operations, improving the performance of authorization checks for models that use these patterns. It ensures correctness and consistency with the existing TTU fast-path optimization.
