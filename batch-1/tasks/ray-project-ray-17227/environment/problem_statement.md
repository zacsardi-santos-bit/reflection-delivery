## Description

When developing Ray from source, it's currently not possible to set up conda test environments that use the locally-built version of Ray rather than a published release. The runtime environment setup module is missing several utility helpers that are needed to support this local development workflow: there is no way to determine the running Python version, no way to discover Ray's own dependencies from the source tree, and no way to link a fresh conda environment back to the current Ray source.

## Expected Behavior

- Developers working with a source checkout of Ray should be able to bootstrap isolated conda environments that reference the local Ray installation rather than a published wheel.
- The runtime environment module should expose utilities to:
  - Return the current Python interpreter version as a formatted string
  - Resolve the list of Ray's own dependencies from the source checkout
  - Inject the local Ray source directory into a conda environment's package search path

## Why This Matters

Without these utilities, the test infrastructure for conda-based runtime environments cannot run locally during development — it relies on having a published Ray wheel. This slows down the development cycle when iterating on changes that affect conda environments and runtime environment setup.
