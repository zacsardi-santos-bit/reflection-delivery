## Description

The codebase currently depends on an older version of the random number generation library. A new major version of this library has been released that introduces several breaking changes: the thread-local RNG accessor function was renamed, the module path for distributions was reorganized, range-generation methods were renamed, boolean and ratio randomness methods were renamed, and the uniform distribution constructor was changed to return a fallible result rather than panicking. Additionally, the method for seeding an RNG from the OS entropy source was renamed, and one of the standard distribution types was moved and renamed.

Because of these breaking changes, the codebase no longer compiles against the new library version. All tests that depend on random number generation are currently failing as a result.

## Expected Behavior

- All source utilities, shared test helpers, and fuzz targets should be updated to use the new API names and module paths.
- The dependency manifest should be updated to specify the new library version.
- The codebase should compile cleanly and all affected tests should pass after the update.

## Why This Matters

Keeping dependencies up to date is important for security patches, bug fixes, and compatibility with the broader ecosystem. The old version of the library is no longer receiving updates, and the new version has been available long enough that dependent tooling has begun dropping support for the old API. This migration unblocks further dependency updates and ensures tests can run reliably.
