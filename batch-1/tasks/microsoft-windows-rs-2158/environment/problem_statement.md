## Description

The platform-specific crate for 64-bit Windows development using the GNU toolchain needs its version number updated to match the current release of the library suite.

Currently, this crate is still at the previous release version, while the rest of the ecosystem has moved to the new release. This mismatch means that developers depending on this target crate may encounter version conflicts or pull in outdated artifacts when building for this platform.

## Expected Behavior

- The version of the x86_64 GNU target crate should be updated to match the current release series.

## Why This Matters

Keeping all target crates in sync with the overall library release version ensures that dependency resolution works cleanly and that consumers of the crate get the correct, up-to-date binaries for their target platform.
