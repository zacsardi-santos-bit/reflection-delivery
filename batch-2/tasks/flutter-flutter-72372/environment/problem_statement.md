## Description

macOS Flutter projects currently embed the Flutter engine and app frameworks by manually linking them in the Xcode project file. With the newer Xcode build system and updated CocoaPods integration, this manual embedding approach is no longer correct — the frameworks should now be embedded via a build-time assembly script invocation instead. The old approach causes build errors or double-embedding issues with the new system.

## Expected Behavior

- An automated migration should detect and remove the manual framework link and embedding entries from macOS Xcode project configuration files.
- The build shell script entry in the Xcode project should be updated to call the assembly tool with the embed command, replacing the old behavior of simply writing the app filename.
- Projects that have already been updated to the new approach should be detected and skipped automatically without any modification.
- If a project has unrecognized leftover framework references that cannot be automatically cleaned up, the migration should report an error to the developer rather than silently leaving the project in a broken state.
- The failure case should record an analytics event so that the frequency of this scenario can be tracked.
- The existing integration test and manual test macOS projects should be updated to reflect the correct post-migration state.
- The CocoaPods workaround that was previously needed to prevent double-embedding should be removed, since the build system no longer requires it.

## Why This Matters

Without this migration, macOS Flutter projects built with the new Xcode build system may fail to compile or produce incorrectly structured app bundles. The migration ensures all existing projects are automatically brought up to date the next time they are built.
