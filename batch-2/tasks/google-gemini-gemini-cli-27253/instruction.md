Implement the `resolveRipgrepPath` and `isTrustedSystemPath` functions to enhance the path resolution and trust checking mechanisms for a CLI tool. Ensure the resolver supports additional deployment layouts and the trust checker recognizes specific internal paths and environment conditions.

*   Update `resolveRipgrepPath` in `packages/core/src/tools/ripGrep.ts`:
    *   Check for a 'purely flattened' SEA binary as the highest-priority path using `path.resolve(__dirname, 'rg-linux-x64')`.
    *   Attempt the 'Dev/Dist layout (with src/)' path as a fallback using `path.resolve(__dirname, '../../../vendor/ripgrep')`.
    *   Try bundled path candidates in priority order:
        *   Purely flattened SEA binary in `__dirname`.
        *   Vendor-subdirectory SEA path containing 'vendor/ripgrep'.
        *   Dev/Dist layout with src/ at `path.resolve(__dirname, '../../../vendor/ripgrep')`.
        *   Dev/Dist layout without src/ at `path.resolve(__dirname, '../../vendor/ripgrep')`.
        *   System PATH as the final fallback.
    *   Handle errors gracefully by catching exceptions during path resolution and returning `null`.

*   Update `isTrustedSystemPath` in `packages/core/src/utils/paths.ts`:
    *   Return `true` for paths starting with '/google/bin/'.
    *   Return `true` for paths matching '/google/src/cloud/.../bazel-out/.../bin/...' or '/google/src/cloud/.../blaze-out/.../bin/...' on Linux.
    *   Bypass the current-working-directory rejection check when any of the following environment variables is set: `TEST_SRCDIR`, `BAZEL_TEST`, `TEST_WORKSPACE`, or `RUNFILES_DIR`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.