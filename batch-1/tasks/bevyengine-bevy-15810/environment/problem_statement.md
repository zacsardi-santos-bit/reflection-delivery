## Description

The math operations module in Bevy currently does not expose certain common floating-point utility functions as standalone free functions when building without the standard library (using an alternative math backend). This makes it impossible to use the module in no_std environments that rely on that alternative backend.

Specifically, functions for computing absolute values, square roots, and sign-copying are missing from the module's public interface in no_std builds. Because these functions are only available as methods on the primitive types in the standard library — and not as free functions in the math module — code that needs them in a no_std context cannot compile.

## Expected Behavior

- An absolute value function should be available as a free function in the math operations module when building without the standard library.
- A square root function should be available as a free function in the math operations module under the same no_std build conditions.
- A sign-copying function should be available as a free function in the math operations module under the same no_std build conditions.
- All of these functions should be accessible via a glob import of the module.

## Why This Matters

Developers targeting embedded systems or other environments without a standard library need basic floating-point operations to work the same way regardless of whether the standard library is present. Without these free functions, the math module cannot be used at all in no_std contexts, blocking adoption for those targets.
