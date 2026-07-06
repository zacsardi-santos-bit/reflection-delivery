## Description

The modeling structure linter is currently implemented as a standalone script that must be manually added to the Python path before it can be imported. This makes it difficult to import cleanly in tests, and it cannot be invoked through the standard Python module system. Additionally, three useful lint checks are missing: one to catch documentation decorators used with empty strings, one to enforce proper weight initialization patterns (using dedicated init utilities rather than in-place tensor operations), and one to ensure model constructors always call the required post-initialization hook.

The test infrastructure also has no awareness of the linter package — changes to its files do not automatically trigger the linter's own tests to run in CI.

## Expected Behavior

- The linter should live in a proper Python package under the repository's utilities directory and be importable using standard import statements without any path hacks.
- The package should expose a submodule structure so that internal components (like the pipeline-parallelism rule's module map) can be patched independently in tests.
- A new lint rule should flag model classes decorated with an empty documentation decorator, requiring non-empty content.
- A new lint rule should flag in-place weight initialization operations inside weight initialization methods, requiring the use of dedicated init utility functions instead.
- A new lint rule should flag model constructors that do not call the post-initialization hook.
- The test fetcher should be updated with functions to discover the linter's tests, determine when those tests need to run based on which files changed, route them to the correct output file, and include them when running tests after changes to the linter.

## Why This Matters

Keeping the linter as a flat script rather than a package creates unnecessary friction for testing and import. The three new lint rules close gaps in modeling best-practices enforcement. Integrating the linter into CI test routing ensures that future linter changes are always tested automatically.
