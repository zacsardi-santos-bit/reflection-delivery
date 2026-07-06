## Description

Natalie is missing support for array deconstruction when using the one-line rightward assignment pattern matching syntax. When a developer tries to match an array (or any object that supports the array decomposition protocol) against multiple named variables in a single assignment expression, the operation fails or is simply not implemented.

## Expected Behavior

- When an array is used with the one-line pattern matching syntax to assign to multiple comma-separated variables, each element should be bound to the corresponding variable in order.
- When a non-array object that implements the array decomposition protocol is used in the same way, its array decomposition method should be called and the returned array should be used to bind the variables.
- When the object on the left-hand side does not support deconstruction, a descriptive error should be raised identifying both the failing value and the fact that it does not support array decomposition.
- When the number of elements in the deconstructed result does not match the number of target variables, a descriptive error should be raised indicating the actual vs. expected length.

## Why This Matters

This is a fundamental part of Ruby's pattern matching feature, and without it developers cannot use the clean, expressive single-line destructuring form that is idiomatic in modern Ruby. The errors raised on failure should be informative enough to diagnose the problem quickly.
