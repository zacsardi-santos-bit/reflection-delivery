## Description

There are two related gaps in the integration test infrastructure that need to be addressed.

First, when integration tests spin up a remote containerized environment to test file system operations, only the main binary is copied to the container. The sandboxing binary is never deployed, so the remote test environment cannot actually enforce sandbox policies. This means any sandboxed file operation tests running in remote mode are not genuinely testing sandbox enforcement — the sandboxing component simply isn't there.

Second, the boilerplate code that allows test binaries to act as multiple helpers (dispatching to different roles based on how they are invoked) is duplicated independently in each test crate that needs it. Each crate maintains its own version of the same initialization logic, which is fragile and hard to keep in sync.

## Expected Behavior

- When a remote exec server is started for testing, the sandboxing binary should be deployed to the remote container alongside the main binary, made executable, and verified to work before tests begin.
- A shared utility library should centralize the test binary dispatch setup, allowing any test crate to configure this behavior with a concise call instead of copy-pasting the logic.
- The remote test environment should include dedicated integration tests for sandboxed file operations: reading within allowed directories should succeed, path traversal via symbolic links should be rejected, removing a symbolic link should not affect the file it points to, and copying a symbolic link should produce a new symbolic link rather than a copy of the target.
- Tests that previously ran in both local and remote modes should have their remote variants properly separated into the remote test suite.

## Why This Matters

Without the sandboxing binary deployed in the remote container, remote sandbox tests give false confidence — they either silently skip enforcement or test the wrong thing. Centralizing the dispatch setup reduces maintenance burden and ensures consistent behavior across all test crates.
