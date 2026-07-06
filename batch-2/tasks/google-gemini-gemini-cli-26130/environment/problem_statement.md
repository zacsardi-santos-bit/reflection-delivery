## Description

The CLI application can be distributed and run in two different modes: as a standard Node.js script, or as a self-contained standalone binary. When the application needs to relaunch itself with additional runtime configuration (such as increased memory limits), it currently assumes it is always running as a plain Node.js process and passes runtime flags as command-line arguments.

This approach breaks when the application is running as a standalone binary. Binaries do not accept Node.js runtime flags via the command line the same way a standard script invocation does — the flags must be passed through the environment instead.

## Expected Behavior

- The application should be able to detect whether it is running as a standalone binary, as a relaunching binary (where the binary re-invokes itself), or as a standard Node.js script.
- When relaunching itself in standalone binary mode, runtime flags should be passed through the environment rather than as command-line arguments.
- When relaunching itself in standard Node.js mode, runtime flags should continue to be passed as command-line arguments.
- Existing environment settings for runtime flags should be preserved and new flags appended rather than overwritten.
- Passing complex or unsupported flag formats in binary mode should result in a clear, descriptive error rather than silent failure.

## Why This Matters

Without these fixes, the application can silently fail or behave incorrectly when it attempts to relaunch itself with additional configuration in standalone binary mode. Developers distributing the CLI as a binary and users running such a binary will hit issues where memory or configuration flags are simply ignored, or the relaunch fails entirely.
