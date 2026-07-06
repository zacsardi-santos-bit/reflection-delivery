Update the project's test infrastructure to ensure compatibility across platforms and resolve dependency issues. Implement the following changes to allow the test suite to run successfully.

*   Add the missing process-tree kill utility package to the project's developer dependencies.
    *   Modify `package.json` to include `tree-kill` (version ^1.2.2) under `devDependencies`.

*   Update the process spawning logic for the local test HTTP server to ensure cross-platform compatibility.
    *   In `test/jest-global-setup.ts`, modify the `spawn()` call to include `{ shell: process.platform === 'win32' }` as its third argument. This ensures the server can be started as a batch script on Windows.

*   Revise the test teardown logic to kill the entire process tree, preventing orphaned processes.
    *   In `test/jest-global-teardown.ts`, import the `kill` function from `tree-kill`.
    *   Replace `httpServerProcess.kill()` with `kill(httpServerProcess.pid)` to terminate the full process tree.

*   Ensure that the constants package tests pass by verifying the correct export of the following groups of constants:
    *   ENV, RENDERER_TYPE, BLEND_MODES, DRAW_MODES, FORMATS, TARGETS, TYPES, SCALE_MODES, WRAP_MODES, MIPMAP_MODES, ALPHA_MODES, GC_MODES, and PRECISION.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.