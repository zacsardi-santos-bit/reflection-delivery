## Description

The current release tooling in the build package does not distinguish between packages that are part of the main shell release and packages that have their own independent release cadence ("auxiliary" packages). This makes it impossible to bump or publish only the auxiliary packages without also touching the main shell release.

Additionally, the version-bumping utility currently requires the release version to be passed explicitly as an argument. It would be safer and more consistent with CI conventions to read the target version from a well-known environment variable and fail fast with a clear error message when that variable is not set.

Finally, when publishing a full release, git version tags are currently not created and pushed automatically. The publish step needs to create an annotated git tag for the released version and push it to the remote so that downstream tools can track which commit each release corresponds to.

## Expected Behavior

- A new function should be added that bumps only the main mongosh release packages. It should read the target version from an environment variable and throw a descriptive error if that variable is absent.
- A separate helper function should be added to update the version constant embedded in the shell API source file, replacing the old version string with the new one.
- The publish function should accept an options object instead of a bare boolean flag, with a field to indicate whether only auxiliary packages are being published.
- When doing a full (non-auxiliary-only) publish, the tooling should automatically create and push an annotated git tag for the mongosh version after lerna finishes.
- When doing an auxiliary-only publish, git tags must not be created or pushed.

## Why This Matters

These changes lay the groundwork for separate CI workflows that can independently bump and publish auxiliary packages, keeping them up to date without coupling every release to the main mongosh release cadence.
