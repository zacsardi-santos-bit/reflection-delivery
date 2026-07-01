## Description

The regression test suite covers a variety of installation scenarios, but end-to-end tests for embedded cluster installations (both airgapped and online environments) have not yet been ported to the current end-to-end test framework. These scenarios need full coverage equivalent to what already exists for existing cluster installations.

In addition, the shared helper that validates dashboard metric graphs is currently written assuming all test environments require manual configuration of a metrics endpoint before graphs will appear. This assumption holds for existing clusters, but embedded clusters come with their metrics infrastructure pre-configured — attempting to manually set up the endpoint in an embedded cluster environment causes unnecessary failures.

## Expected Behavior

- New end-to-end test suites exist for embedded cluster airgapped install and embedded cluster online install scenarios, following the same structure and validation steps as the existing cluster tests.
- The dashboard graph validation helper accepts a flag indicating whether the test is running against an existing cluster or an embedded cluster.
- When running against an existing cluster, the helper configures the metrics endpoint before checking that the graphs appear.
- When running against an embedded cluster, the helper skips the metrics endpoint configuration and checks directly that the graphs appear.
- All existing cluster test suites continue to work correctly with the updated helper.

## Why This Matters

Without these changes, the test suite has gaps in embedded cluster coverage, and any attempt to run the dashboard graph validation against an embedded cluster fails because the function incorrectly tries to configure a metrics endpoint that is already set up. These changes ensure accurate and complete regression coverage across all supported installation types.
