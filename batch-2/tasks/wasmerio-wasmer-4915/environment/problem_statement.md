## Description

The Wasmer project is due for a minor version release. The workspace needs all package version numbers updated from the current release to the next one. Several packages also have their own independent versioning (not derived from the shared workspace version) that must be bumped separately.

At the moment, the workspace is in an inconsistent state: some manifest files have been partially updated while others still reference the old version numbers. This inconsistency causes the build to fail when strict dependency locking is enforced, because the recorded lock file no longer matches what the manifest files declare.

## Expected Behavior

- All packages in the workspace should declare the new version numbers consistently.
- All cross-package dependency references that pin to exact version numbers should be updated to match.
- A small number of packages that maintain their own versioning (separate from the main workspace version) should also be bumped to their respective next versions.
- The lock file should be updated to reflect all of these changes.
- After the update, the project must build cleanly under strict lock file enforcement, and the existing type system unit test suite must pass in full.

## Why This Matters

When version numbers are inconsistent across the workspace, any developer or CI system that relies on the committed lock file will encounter build failures. Making all version references consistent unblocks continued development and prepares the workspace for a proper release.
