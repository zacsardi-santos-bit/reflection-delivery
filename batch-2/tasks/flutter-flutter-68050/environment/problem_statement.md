## Description

Flutter's iOS and macOS build tooling invokes Apple's developer command-line tools using hardcoded paths. On Apple Silicon Macs (ARM64), this can cause those tools to run under the Rosetta 2 x86 translation layer instead of natively. Running under translation can cause crashes and other unexpected failures when building Flutter apps for iOS.

## Expected Behavior

- When running on an ARM64 Mac, all Apple developer tool invocations should be explicitly prefixed to force native ARM64 execution rather than going through Rosetta translation.
- When running on an x86 Mac, the current behavior should remain unchanged.
- The build system should detect the host architecture at runtime and choose the appropriate invocation style accordingly.
- The detection of whether specific Apple sub-tools (like xcdevice) are available should be simplified — the current approach of doing a secondary process lookup is redundant and can be removed.
- The class that manages iOS Simulator control must accept the Apple developer toolchain manager so it can use the correct, architecture-aware command prefix for all simulator operations.

## Why This Matters

Apple Silicon Macs are increasingly common among Flutter developers. When the build tools run in Rosetta translation unexpectedly, the developer gets hard-to-diagnose crashes or build failures. Forcing native execution on ARM64 hardware ensures reliable builds and avoids translation-related issues across the full range of iOS build operations: compiling, linking, framework creation, simulator management, and device discovery.
