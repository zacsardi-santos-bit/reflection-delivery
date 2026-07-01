## Description

As part of the Gateway API release process, the bundle version label embedded in CRD annotations and referenced by the conformance test suite needs to be advanced to reflect the current release milestone. The codebase currently carries a development-snapshot label, but the project has now reached a release candidate milestone and the version identifier must be updated accordingly.

## Expected Behavior

- The bundle version label used across the project should reflect the release candidate milestone rather than the prior development snapshot designation.
- Conformance tooling that reads CRD annotations to determine the installed Gateway API version should correctly identify and return the release candidate version when CRDs are consistently annotated with it.
- Version-checking logic that validates installed CRD bundle versions against the expected current version should pass when CRDs carry the release candidate label.

## Why This Matters

Conformance suites verify the installed API bundle by inspecting the version annotation on Gateway API CRDs and comparing it against the expected current version. When the codebase still carries the old development label but tests are written against the release candidate label, these checks fail and conformance results are incorrect. Keeping the version label up to date is a required step in the release process.
