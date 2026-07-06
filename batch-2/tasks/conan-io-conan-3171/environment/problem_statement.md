## Description

When a library declares some of its dependencies as "private" (meaning they are internal implementation details that consumers of the library should not care about), and those private dependencies happen to lack pre-built binaries in the local cache, the package manager incorrectly fails the installation with an error instead of simply skipping those private dependencies.

This is wrong behavior: a consumer of the library should never need to care about the library's private, internal dependencies. Whether or not those private dependencies have a pre-built binary available is completely irrelevant to the consumer.

## Expected Behavior

- When a package has private dependencies and those private dependencies have no pre-built binary, installing a consumer of that package should succeed.
- Private dependencies with no binary should be reported as skipped in the installation output.
- Skipped private dependencies should not appear in the generated build configuration files — consumers should only see the libraries they actually need.
- If the same dependency is referenced as private by one package but as public (or directly) by another, the public reference should win: the dep should be available and included in the build configuration.

## Related Issue

There is also a related problem with merging directories when the destination is a subdirectory of the source. This causes incorrect behavior when a package stored under a legacy internal directory layout is downloaded from a remote server and built locally — the merge step can loop into itself or fail to copy the expected files.

## Why This Matters

Users who publish libraries with private dependencies should not need to ensure that those private dependencies' binaries are always present in every consumer's environment. The whole point of marking a dependency as private is to keep it hidden from consumers. The current behavior defeats this purpose and causes unnecessary build failures in CI environments where only the necessary binaries are available.
