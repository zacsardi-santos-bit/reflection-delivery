## Description

The project has versioned kustomize configurations for deploying clusters using different bootstrap scenarios (one of them being the Flatcar system-extension approach). When a new API version was introduced for the OpenStack infrastructure provider, the end-to-end test data was updated to reference the new version's deployment configuration. However, the corresponding kustomize directory for that version was never created, so any attempt to assemble the test configuration fails.

Because the test runner verifies that the kustomize configuration builds cleanly before executing the full unit-test suite, all tests — including those completely unrelated to the Flatcar scenario — are currently failing.

## Expected Behavior

- A valid kustomize configuration exists for the new API version's Flatcar system-extension scenario.
- The kustomize assembly step completes without errors.
- The full unit-test suite is able to run and all tests pass.

## Why This Matters

Without this directory in place, CI is completely broken: no unit tests can run at all. Adding the missing kustomize resources for the new API version unblocks the entire test suite and ensures that the Flatcar cluster template continues to work correctly with the latest API version.
