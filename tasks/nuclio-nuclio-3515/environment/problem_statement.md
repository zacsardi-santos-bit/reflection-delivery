## Description

When running the release tool as part of an automated CI/CD pipeline, downstream workflow steps need access to the resolved version information — specifically the current version, the target version, and the helm chart target version. Currently there is no way for the release tool to persist this information to disk, which means downstream steps either have to re-run parts of the release logic or use fragile workarounds to share these values between pipeline stages.

## Expected Behavior

- The release tool should support an option to specify a file path where release version information will be saved.
- When such a path is provided, the tool writes a structured text file containing the resolved current version, target version, and helm chart target version.
- The output file should use a simple, line-oriented format with labeled fields so that shell scripts and other tooling can easily extract individual values using standard text processing utilities.
- When no path is specified, the tool should continue to operate normally without writing any file.

## Why This Matters

This allows multi-stage CI/CD pipelines to share resolved release version information across steps without redundant computation or tight coupling between steps. Instead of each step re-deriving versions independently, they can all read from a single source of truth written by the release tool.
