## Description

The Windows desktop build pipeline has a few issues that need to be addressed:

1. The service used to write dependency tracking files requires callers to pass in platform information explicitly. This is unnecessary since the underlying file system object already knows its own path style. Removing the platform dependency simplifies construction and reduces coupling.

2. The Windows engine artifact unpacking step writes files directly to the Windows Flutter project directory rather than an ephemeral subdirectory. Generated artifacts should be isolated in an ephemeral subfolder to keep the project directory organized and make it clear which files are managed by the build system.

3. The Windows artifact unpacking step does not generate a dependency tracking file. Without this, the build system cannot detect when engine artifacts are stale and need to be re-copied. A proper dependency file recording source and destination paths should be generated each time artifacts are unpacked.

4. There is no dedicated build target for assembling Windows debug bundles. Linux already has this, but Windows is missing a target that copies the compiled Dart kernel to the assets directory and records an asset dependency file.

5. The old deprecated unpack command should be removed now that the build system targets handle artifact management.

## Expected Behavior

- The dependency file service can be constructed with only a logger and file system — no platform argument needed
- A reusable utility is available for copying desktop artifacts from a source directory to an output directory, tracking inputs and outputs in a depfile, and throwing an error if any expected artifact is missing
- Windows engine artifacts are unpacked into the ephemeral subdirectory and a corresponding dependency file is generated
- A Windows debug bundle target exists that copies the compiled kernel to the assets folder and generates an asset dependency file

## Why This Matters

Without these changes, the Windows build pipeline is missing proper dependency tracking, artifacts land in the wrong output location, and there is no way to incrementally build Windows debug bundles through the standard build system.
