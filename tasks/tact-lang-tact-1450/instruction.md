Refactor the Tact compiler CLI to support automated end-to-end testing and enhance the programmatic API for isolated testing. Implement new functions and update existing ones to accommodate in-memory configurations and virtual file systems, ensuring robust CLI behavior and error handling.

*   Update the `run` function in `src/cli/tact/index.ts`:
    *   Accept a single argument object with `logger`, `config`, `project`, and `stdlib`.
    *   Return a Promise resolving to `{ ok: boolean; error: { message: string }[] }`.

*   Implement the `createSingleFileConfig` function in `src/cli/tact/index.ts`:
    *   Accept a `fileName: string`.
    *   Return a `Config` object with a single project entry.

*   Refactor the `main` function in `src/cli/tact/index.ts`:
    *   Serve as the CLI entry point for the tact command.
    *   Ensure `bin/tact.js` delegates to this function using `require('../dist/cli/tact/index.js').main()`.

*   Ensure CLI behavior:
    *   `tact --version` exits with code 0 and prints a semver version string followed by `git commit: ` and the current git commit hash.
    *   Suppress JavaScript stack traces in stdout when syntax or type errors occur.
    *   `tact foo.tact` exits with code 0 on valid contracts, with or without flags like `--check`, `--func`, or `--with-decompilation`.
    *   `tact --config config.json` with `mode: 'full'` produces a `.pkg` file, and with `mode: 'fullWithDecompilation'`, produces a decompiled binary file.
    *   CLI flags take precedence over config file settings.
    *   Mutually exclusive flags `--func`, `--check`, and `--with-decompilation` result in exit code 30 when used together.
    *   Unknown flags and compilation failures result in exit code 30.
    *   `tact -e "<expression>"` evaluates expressions and prints results.

*   Implement the `main` function in `src/cli/unboc/index.ts`:
    *   Serve as the CLI entry point for the unboc command.
    *   Ensure `bin/unboc.js` delegates to this function.

*   Develop test utilities:
    *   `runCommand` in `src/cli/test-util.build.ts` executes shell commands and returns results.
    *   `makeCodegen` in `src/cli/test-util.build.ts` creates test codegen helpers, adhering to Tact's output naming conventions.
    *   `allInFolder` in `src/test/utils/all-in-folder.build.ts` discovers and compiles Tact contract files, exiting with code 1 on failure.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.