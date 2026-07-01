## Description

The project is currently failing to build because a key cryptography library dependency is pinned to an outdated version that is no longer compatible with other dependencies in the project. This causes compilation errors that prevent any tests from running — including the integration tests for the DNS challenge solver.

## Expected Behavior

- The project should compile successfully with updated, compatible dependency versions
- The dependency on the cryptography library and its transitive dependencies (system-level library, terminal library, text library) should be updated to newer versions that are mutually compatible
- All module dependency files and their corresponding checksum files should be updated consistently across the entire repository, including all submodules and sub-commands
- All license attribution files should reflect the updated dependency versions
- The DNS challenge solver integration tests should run and pass once the build is fixed

## Why This Matters

Integration tests for the DNS01 challenge solver (which handles dynamic DNS updates for certificate issuance) are completely blocked from running due to this compilation issue. These tests cover a wide range of important scenarios including server communication, authentication handling, and support for different nameserver address formats. Fixing the dependency versions restores the ability to validate this functionality.
