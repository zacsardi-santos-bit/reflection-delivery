## Description

The current autotune API bundles memory resources (bindings) directly into the operation set definition. This creates an awkward coupling: every time you want to benchmark a group of candidate kernels, you must mix the reusable "what to benchmark" logic with specific runtime memory allocations that belong to a single call-site.

This design has several downsides:
- Operation sets cannot be defined once and reused across different inputs.
- Individual operations must store and carry their own copies of the input bindings, adding boilerplate and redundancy.
- Extending the system with new operation sets requires writing a full struct with trait implementation rather than composing simpler building blocks.

## Expected Behavior

- Tunable operation sets should describe *which* operations to benchmark and *how* to key and clone inputs, but should not store the actual inputs themselves.
- Actual input data (memory bindings) should be supplied separately at execution time.
- Individual tunable operations should no longer need to hold their own copies of the input data.
- It should be possible to define a tunable operation using a plain closure or function, without implementing a full trait by hand.
- Custom checksum logic for cache invalidation should be configurable per-set via a builder method rather than through struct fields or trait overrides.
- All existing caching semantics must be preserved: same key produces a cache hit, different key or checksum produces a cache miss.

## Why This Matters

Decoupling the operation definition from the input data makes the autotune system easier to use, enables reuse, and reduces the amount of code needed to add new autotunable operations. It also makes the API more composable and idiomatic.
