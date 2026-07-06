## Description

The Flutter build system currently passes a pre-computed engine directory path as a raw string when running CocoaPods pod installations for both iOS and macOS platforms. This design is outdated: iOS projects no longer need the engine directory injected into the pod install environment at all, while macOS projects should derive this path internally from the build mode and available build artifacts rather than requiring the caller to supply it. Additionally, the code that handles outdated Podfile patterns is too lenient — it merely prints a warning instead of stopping the build — which can allow incompatible configurations to silently proceed.

## Expected Behavior

- The method that processes CocoaPods installation should accept a build mode value instead of a pre-computed engine path string.
- The class should also accept an artifacts dependency to enable internal computation of any required framework paths.
- When running pod installation for iOS projects, no engine or framework directory should be passed in the pod install environment.
- When running pod installation for macOS projects, the framework directory should be computed internally and injected into the pod install environment.
- Podfiles that reference obsolete Flutter patterns (such as creating engine symlinks or parsing the old plugins file format) should cause the build to fail with a clear error, not just print a warning.
- The iOS Xcode build configuration should no longer include the Flutter framework directory variable.
- The macOS Xcode build configuration should still include the Flutter framework directory variable, computed from the build mode.

## Why This Matters

This change makes the build system more robust and self-contained: callers no longer need to compute and supply engine paths externally, and projects using outdated integration patterns are caught early with a clear failure rather than silently continuing with a potentially broken setup.
