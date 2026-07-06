## Description

Dask's array module should have an internal expression-based array backend that lives within the Dask package itself, rather than depending on an external package. Currently, when the experimental expression-based query planning mode is enabled for arrays, the required functionality is either missing or pulled from an external source. We need a first-class internal module that provides this capability.

## Expected Behavior

- A new internal array module within Dask should be importable and provide a random array creation submodule that accepts shape and chunking parameters.
- Arrays created through the new module must support standard arithmetic operations — addition, subtraction, multiplication, division (true and floor), and exponentiation — along with their reflected counterparts, all producing results consistent with the underlying array behavior.
- The array objects must be computable and produce correct numerical output.
- The test suite must have a dedicated option to run tests specific to the expression-based array backend in isolation, and tests that are not compatible with that backend should be skipped cleanly when the option is active.
- The main array collection class should be accessible from the top-level array namespace.

## Why This Matters

This change moves the expression-based array backend from an external dependency into the Dask codebase, making it easier to maintain, test, and iterate on. It also ensures the test suite can run correctly in both standard and expression-based array modes without spurious failures.
