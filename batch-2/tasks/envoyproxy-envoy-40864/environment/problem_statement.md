## Simplify the HTTP Filter Configuration Trait Interface

## Description

The trait that SDK users must implement when writing HTTP filter configurations currently requires two separate generic type parameters: one for a filter configuration context type and one for the per-request filter handle type. However, the only method on this trait — which creates a new per-request filter instance — only ever uses the per-request filter handle. The filter configuration context type parameter is effectively unused and serves no purpose in the trait.

This unnecessary dual-parameter design forces every implementor to carry and propagate an extra generic type parameter throughout their code, even though it adds no value.

## Expected Behavior

- The filter configuration trait should require only a single generic type parameter: the per-request filter handle type
- The method for creating new filter instances should accept the per-request filter handle type directly as its argument
- All existing functionality should be preserved; only the unnecessary type parameter is removed
- The function type alias used to register filter configuration constructors should reflect the same simplified signature

## Why This Matters

Removing the redundant type parameter reduces boilerplate for SDK users, makes the trait easier to understand and implement correctly, and eliminates the need to propagate an unused type constraint through all filter configuration implementations.
