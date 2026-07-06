## Description

The gRPC build system macros need improvements to support building and testing on Windows with the MSVC compiler. Currently, several test targets and build targets rely on POSIX-specific polling mechanisms that do not exist on Windows. When these targets are attempted on a Windows MSVC build, they fail or produce errors because the polling-based variants are unconditionally generated.

Additionally, the build helper macros do not support accepting platform-filtering tags, which means that platform-incompatible targets throughout the codebase cannot be properly annotated and excluded from unsupported platforms at build time.

## Expected Behavior

- A way to detect whether the current build is using MSVC should be available to all build files
- The helper macros for defining C++ binaries and libraries should accept and forward tags to the underlying build rules, allowing platform-specific tags to be passed through
- When building with MSVC, test targets that rely on POSIX polling mechanisms should be automatically skipped instead of generating incompatible test variants

## Why This Matters

Without these changes, attempting to run the full gRPC test suite on Windows MSVC either fails during build or produces test targets that cannot run on that platform. By introducing MSVC detection and tag propagation in the build system macros, individual build targets across the codebase can be properly excluded from unsupported platforms, and the build system will not try to generate POSIX-specific test configurations on Windows.
