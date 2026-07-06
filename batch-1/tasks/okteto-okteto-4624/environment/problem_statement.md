## Description

We need a CLI command that lets developers validate their Okteto manifest files before deploying. Right now there is no way to check a manifest for correctness ahead of time — errors only surface once a deployment is attempted. A dedicated validate command would give developers instant feedback on whether their configuration file is well-formed and schema-compliant.

## Expected Behavior

- Running the validate command with a path to a valid manifest should succeed with no errors.
- Running it with an empty file, a file containing YAML syntax errors, a file with invalid section values, or a file referencing unknown top-level configuration keys should return a meaningful error and exit with a failure status.
- Running it with a path to a file that does not exist should also return an error.

## Additional Context

The integration test suite needs corresponding test fixture files — a set of known-good manifests and a set of deliberately broken manifests — so that the validate command can be exercised end-to-end against real examples.

Additionally, an existing test case for the manifest schema has a small typo in its label that should be corrected so the test suite accurately reflects the sample it is testing.

## Why This Matters

Catching configuration mistakes early — before a deployment is triggered — saves developer time and reduces confusion. A validate command that clearly distinguishes valid from invalid manifests makes the development loop tighter and more reliable.
