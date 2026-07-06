## Description

The test suite is completely broken because the global test teardown script imports a package that is not listed in the project's dependency manifest. Since the package is never installed, the teardown module fails to load at Jest startup, causing every test to error before it even runs — including foundational checks that verify the library's core constants are exported correctly.

Beyond the missing dependency, there are two additional Windows compatibility problems in the test infrastructure:

1. The local HTTP server is spawned without going through the system shell, which means it cannot start on Windows (where the server executable is a batch script).
2. On teardown, only the top-level server process is killed, leaving its child processes running as orphans.

## Expected Behavior

- The missing package should be declared as a developer dependency so it is automatically installed when developers set up project dependencies.
- Spawning the local test server should use the system shell on Windows and direct execution on other platforms.
- The teardown should kill the entire process tree — not just the root process — so no orphaned processes are left behind.
- With these fixes in place, the core constants tests (verifying that groups like rendering types, blend modes, texture formats, and scale modes are all properly exported) should run successfully.

## Why This Matters

Without the missing dependency, **no tests can run at all** — the suite fails at the setup/teardown loading stage before any test code executes. On Windows, even after the dependency is added, the server can't start and can't clean up properly. Fixing all three issues is required to make CI reliable across platforms.
