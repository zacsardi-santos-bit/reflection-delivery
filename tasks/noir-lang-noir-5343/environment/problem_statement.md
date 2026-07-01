## Description

When implementing a trait, developers can accidentally add extra constraints to individual methods that are stricter than what the original trait method requires. Currently the compiler silently accepts this, which causes confusing behavior: code that should work with a trait can fail in unexpected ways because the implementation is more restrictive than the trait contract.

For example, if a trait defines a method with no constraints, an implementation of that method should not be able to add extra type constraints — doing so violates the Liskov substitution principle and makes the implementation unusable in contexts where the extra constraint is not met.

## Expected Behavior

- When a trait implementation method introduces constraints that are not present on the corresponding trait method, the compiler should reject the code with a clear error identifying the problematic constraint type and trait.
- This detection should handle all forms of constraint types: plain generics, generic types wrapping other types, array types, and tuple types.
- Constraints placed on the implementation block itself (rather than on individual methods) should remain valid and should not trigger this error.
- When an implementation-block-level constraint is not satisfied at a call site, the compiler should report that no matching implementation was found.

## Why This Matters

Without this check, a developer might write a trait implementation that silently restricts the applicability of the trait, leading to hard-to-understand type errors elsewhere in their code. The compiler should catch this at the point where the stricter implementation is defined, not at the call site.
