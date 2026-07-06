## Description

The project maintains a stability test suite that validates configuration encoding for each supported version of Talos. For every recognized version, the test generates machine configurations and compares them against golden reference files, ensuring encoding behavior doesn't change unexpectedly between code changes.

Version 1.8 is being prepared for release, but its configuration encoding is not yet covered by the stability test. This means regressions in how version 1.8 configurations are serialized could silently slip in without being caught.

## Expected Behavior

- The stability test should include version 1.8 alongside the existing tracked versions (1.3–1.7)
- Golden reference configuration files should exist for version 1.8, covering both base configurations and configurations with common overrides applied
- Both controlplane and worker node configuration types should be represented

## Why This Matters

As the team prepares the Talos 1.8 release, having this stability coverage in place ensures that the encoding of configurations for this version is locked in. Any future code change that inadvertently alters the 1.8 configuration output will be caught early, before it reaches users.
