## Description

The tool that locates the bundled search binary does not correctly handle all deployment layouts. In particular, when the application is packaged as a self-contained executable with a "purely flattened" layout — where the binary sits directly alongside the application file with no intermediate subdirectory — the resolver fails to find it. The resolver needs to check this flat layout first, before falling back to the other paths it already knows about.

There is also a related path that reflects how the build output is organized when the source tree has a particular directory depth; this Dev/Dist layout variant is not currently tried.

Additionally, the path-trust checker that decides whether a system binary is safe to use has two gaps in its logic:

1. It does not recognize paths that are inherently trusted in internal build-system and hermetic execution environments (paths under specific internal infrastructure directories).
2. When a hermetic test runner is active — signaled by well-known environment variables — working-directory paths should be considered safe, but the current logic still rejects them.

## Expected Behavior

- The binary resolver should try the purely flattened layout first, then the vendor-subdirectory layout, then two Dev/Dist layout variants, and finally the system path.
- If any unexpected error occurs during resolution, the function should fail gracefully with no result rather than crashing.
- Paths located under the internal build-system infrastructure prefix should be trusted.
- When recognized hermetic-test-runner environment variables are present, the CWD proximity check should be bypassed so that the correct binary can be found.

## Why This Matters

Users running the tool inside internal build environments or hermetic CI environments cannot use the bundled search binary, causing the tool to either silently degrade or fail. Supporting these deployment layouts ensures the tool works reliably across all supported execution contexts.
