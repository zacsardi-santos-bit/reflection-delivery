## Description

The project's random number generation library has released a new major version that introduces breaking API changes. The function used to obtain a thread-local random number generator has been renamed, and the module path for the alphanumeric character distribution has been reorganized. As a result, the codebase no longer compiles against the updated library, causing every test in the ntex crate to fail.

## Expected Behavior

- The dependency version should be updated to the new major release of the random number generation library.
- All internal usages of the old API — specifically the renamed generator function and the old distribution module path — should be replaced with their new equivalents.
- All existing tests that generate random data for compression and HTTP payload testing should continue to pass after the update.

## Why This Matters

Staying on an outdated major version of a core utility library blocks adoption of other ecosystem updates and leaves the project unable to build with a current dependency tree. Updating to the new version and fixing all call sites restores a clean build and keeps the project compatible with its dependencies.
