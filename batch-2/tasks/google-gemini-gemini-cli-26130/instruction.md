Implement utilities to detect the execution mode of a CLI tool and adjust how runtime flags are passed during relaunch. Ensure correct handling of flags in both standard Node.js and standalone binary modes.

*   Implement `isStandardSea()` in `packages/cli/src/utils/processUtils.ts`:
    *   Return `false` if `process.argv[0] === process.argv[1]` (relaunch SEA scenario), even if `IS_BINARY` is `'true'`.
    *   Return `true` if `IS_BINARY` is `'true'` and `process.argv[0] !== process.argv[1]`, or if `process.isSea()` returns `true` and `process.argv[0] !== process.argv[1]`.
    *   Return `false` in a plain Node.js environment.

*   Implement `getScriptArgs()` in `packages/cli/src/utils/processUtils.ts`:
    *   Return `process.argv.slice(1)` when `isStandardSea()` is `true`.
    *   Return `process.argv.slice(2)` when `isStandardSea()` is `false`.

*   Implement `isSeaEnvironment()` in `packages/cli/src/utils/processUtils.ts`:
    *   Return `true` if `IS_BINARY` is `'true'`, `process.isSea()` returns `true`, or `process.argv[0] === process.argv[1]`.
    *   Return `false` otherwise.

*   Implement `getSpawnConfig(nodeArgs: string[], scriptArgs: string[])` in `packages/cli/src/utils/processUtils.ts`:
    *   In standard Node.js mode:
        *   Set `spawnArgs` to `[...process.execArgv, ...nodeArgs, script, ...scriptArgs]` where `script` is `process.argv[1]`.
        *   Set `env` to include `GEMINI_CLI_NO_RELAUNCH` as `'true'` and ensure `NODE_OPTIONS` is absent or falsy.
    *   In SEA binary mode:
        *   Set `spawnArgs` to `[process.execPath, ...scriptArgs]`.
        *   Set `env` to include `NODE_OPTIONS` with existing value appended with `nodeArgs` (space-separated) and `GEMINI_CLI_NO_RELAUNCH` as `'true'`.
        *   Ensure `process.execArgv` is not duplicated in `NODE_OPTIONS`.
    *   Throw an error with message `'Unsupported node argument for SEA relaunch: <arg>. Complex escaping is not supported.'` if any `nodeArg` contains spaces or backslashes in SEA mode.

*   Export `ProcessWithSea` type in `packages/cli/src/utils/processUtils.ts`:
    *   Extend the Node.js process object with an optional `isSea()` method returning a boolean.

*   Ensure `relaunchAppInChildProcess` uses SEA-aware spawn configuration:
    *   Pass node arguments as command-line arguments in standard Node.js mode.
    *   Pass node arguments via `NODE_OPTIONS` in SEA binary mode, preserving existing `NODE_OPTIONS` and appending new arguments.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.