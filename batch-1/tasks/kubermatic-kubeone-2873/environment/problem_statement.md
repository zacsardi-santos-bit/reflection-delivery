## Description

The CI pipeline configuration for this project has two issues that need to be cleaned up to keep the generated configuration accurate and up-to-date.

First, OpenStack-specific test jobs currently include an explicit test timeout environment variable in their definitions. This timeout override is redundant because the testing framework already manages timeouts through other mechanisms. The extra entry creates noise in the CI configuration and should be removed.

Second, upgrade scenario tests that reference a separate stable version of the project are pointing to the main development branch as their reference. These scenarios should instead use the current stable release branch. The explicit branch override was left in place from an earlier setup but now points to the wrong branch — removing it lets the generator fall back to the correct stable release branch by default.

## Expected Behavior

- OpenStack presubmit jobs in the generated CI configuration should not include a timeout environment variable
- Stable upgrade job entries in the generated CI configuration should reference the stable release branch for the extra repository reference, not the development branch
- Upgrade scenario entries that reference a separate stable version of the tool should not include an explicit branch override in the scenario configuration file

## Why This Matters

The project uses a generator tool to produce CI pipeline definitions from a scenario configuration file. When the Go-level job definitions and the scenario configuration file are not aligned with the generated output, the consistency check fails. Fixing these two issues ensures the generator produces accurate CI configuration without redundant settings and with the correct branch references.
