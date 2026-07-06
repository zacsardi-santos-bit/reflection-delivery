Implement a robust error handling mechanism for the hot module replacement (HMR) system to gracefully manage syntax errors in source files. Ensure that errors are clearly reported in the browser console, and that the server remains operational to allow for recovery and successful hot updates upon error correction.

*   Update the file `examples/react-fast-refresh/src/index.tsx`:
    *   Ensure the line `const a = 123;` is present to facilitate the end-to-end test sequence involving syntax errors and recovery.

*   Modify the HMR error overlay in `packages/runtime-plugin-hmr/src/overlay.ts`:
    *   Ensure that any syntax error reported to the browser console is prefixed with "[Farm HMR] ".
    *   Format the error message as `[Farm HMR] Parse \`<module_path>\` failed.`, specifically outputting `[Farm HMR] Parse \`src/index.tsx\` failed.` for `src/index.tsx`.

*   Adjust the HMR engine error handling in `packages/core/src/server/hmr-engine.ts`:
    *   Prevent the server from crashing or exiting by ensuring it does not call `logger.error` with `exit: true` during a syntax error.
    *   Keep the server running to allow developers to fix errors and trigger recovery without restarting.

*   Enhance the Rust function `resolve_last_failed_module_paths` in `crates/compiler/src/update/handle_update_modules.rs`:
    *   Deduplicate module paths by ensuring that if a module path is already in the current update queue, it is not added again from the last-failed list.
    *   Avoid duplicate module IDs to prevent panics during HMR recovery after fixing syntax errors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.