## Description

The CuPy CI pipeline uses a central version matrix configuration and an automated generator script to produce container build files for each supported GPU environment (various CUDA and ROCm versions). When the supported Python interpreter and numeric library versions in the test matrix need to be updated, the generator must be run to regenerate those container build files so they remain consistent with the configuration.

Currently, the version matrix is out of date. Additionally, one family of environments (based on an older OS) requires additional system dependencies to build newer Python versions, and the generator does not yet emit those setup steps. As a result, running the generator in validation mode reports that many container build files need to be regenerated, and the validation check fails.

## Expected Behavior

- The central version matrix configuration should be updated with current Python interpreter and numeric computing library versions for every CUDA and ROCm environment.
- The generator should be extended to emit the required system-level build dependencies for older OS environments that need them when building newer Python versions.
- After both changes, all container build files should exactly match what the generator would produce.
- Running the generator in dry-run (validation) mode should succeed with no files reported as needing regeneration.

## Why This Matters

Keeping the CI test matrix up to date ensures CuPy is tested against relevant and current dependency versions, catching compatibility issues early. An out-of-sync matrix causes CI validation failures that block contributors and obscure real issues.
