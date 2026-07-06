## Description

The iOS physical-device debugging setup currently has version-specific branching logic: it inspects the installed version of the development tools and sends different commands to the underlying debugger session depending on that version. This version-specific path has become unnecessary, adds maintenance burden, and means the objects responsible for setting up debug sessions need to accept and track development tool version information.

There is also a known upstream bug in the debugger engine that causes breakpoints to intermittently fail to rearm after they fire. This results in unreliable debugging sessions on physical iOS devices. A workaround is needed at the session setup level.

## Expected Behavior

- The debugging session setup should use a single, consistent command sequence regardless of which version of the development tools is installed.
- A new configuration step must be added to the session setup that works around the upstream breakpoint rearming issue.
- The objects that set up iOS debug sessions should no longer require or depend on development tool version information.
- The detection of a successful session start should match the new output produced by the updated command sequence.

## Why This Matters

Removing the version-specific branching simplifies the codebase and makes the debugging pathway easier to maintain. Adding the workaround for the upstream bug makes physical-device debugging sessions significantly more reliable, preventing intermittent failures caused by breakpoints not rearming correctly.
