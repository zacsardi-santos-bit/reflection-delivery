## Description

Ray's C++ codebase has a `Status` type for representing success or failure, but no standard way for functions to return *either* a valid value *or* an error status in a single, type-safe container. This forces callers to use output parameters, check error codes separately, or adopt ad-hoc patterns that are inconsistent across the codebase.

We need a generic result wrapper that can hold either a successfully computed value or an error status, modeled after the well-known "result type" pattern seen in other C++ systems.

## Expected Behavior

- Any function that might fail can return this result type instead of using output parameters
- Callers can check whether the result holds a value or an error, retrieve the value safely, and fall back to defaults when an error occurred
- The result type supports copy and move semantics, and can be compared for equality
- Two result containers holding equal values are considered equal; two error containers with the same error code are considered equal
- The result type supports chaining: apply a function to the value if present (skipping it on error), or recover from an error with an alternative computation
- The result type can be converted between compatible types in a class hierarchy (e.g., a result holding a derived type can be assigned to a result holding a base type)
- A set of test utility macros should be available to assert that a result or status is successful

## Why This Matters

Without this container, error-handling code is verbose and inconsistent. With it, functions throughout the Ray C++ core can express their return contract cleanly, and callsites can chain operations, handle errors, and fall back to defaults in a uniform way.
