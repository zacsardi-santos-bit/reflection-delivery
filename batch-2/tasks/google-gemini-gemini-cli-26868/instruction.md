Implement a fallback mechanism for the text-search tool to use a system-installed binary when the bundled binary is unavailable. Ensure that the system-installed binary is only used if it resides in a trusted system directory. Additionally, handle command safety by identifying and flagging dangerous flags.

*   Update `resolveRipgrepPath` in `packages/core/src/tools/ripGrep.ts`:
    *   Export `resolveRipgrepPath(): Promise<string | null>`.
    *   Replace `canUseRipgrep` and `getRipgrepPath` exports.
    *   Attempt to locate a bundled binary first (paths containing 'vendor/ripgrep').
    *   Use `fileExists` to check for the bundled binary and return its path if found.
    *   If no bundled binary is found, search the system PATH using `resolveExecutable('rg')`.
    *   Resolve symlinks with `resolveToRealPath()`.
    *   Validate the real path using `isTrustedSystemPath()`.
    *   Return the resolved path or null if no trusted binary is found.

*   Implement `isTrustedSystemPath` in `packages/core/src/utils/paths.ts`:
    *   Export `isTrustedSystemPath(filePath: string): boolean`.
    *   Return false for paths inside the current working directory.
    *   On Windows, return true for paths under `SystemRoot`, `ProgramFiles`, and `ProgramFiles(x86)` environment variables.
    *   On macOS/Linux, return true for paths under `/usr/bin`, `/bin`, `/usr/local/bin`, `/opt/homebrew/bin`, `/opt/homebrew/Cellar`, `/usr/local/Cellar`, `/usr/sbin`, and `/sbin`.

*   Update `resolveExecutable` in `packages/core/src/utils/shell-utils.ts`:
    *   Export `resolveExecutable(name: string): Promise<string | undefined>`.
    *   Return the absolute path of an executable found on the system PATH or undefined if not found.

*   Update command safety functions in `packages/core/src/sandbox/utils/commandSafety.ts`:
    *   `isKnownSafeCommand(args: string[]): boolean`:
        *   Return false for ripgrep invocations if the executable is not an absolute path.
        *   Return false if `resolveToRealPath()` throws or `isTrustedSystemPath()` is false.
        *   Return false if args include `--search-zip`, `-z`, or start with `--pre=`.
        *   Return true only when the path is trusted and no unsafe args are present.
    *   `isDangerousCommand(args: string[]): boolean`:
        *   Return true for ripgrep invocations if args include `--search-zip` or start with `--pre=`.
    *   `isStrictlyApproved(command: string, args: string[], tools?: string[]): Promise<boolean>`:
        *   Return true if the command is in the tools array or if `isTrustedSystemPath()` returns true for the real path.
        *   Return false otherwise.

*   Update the `Config` class in `packages/core/src/config/config.ts`:
    *   Add `getRipgrepPath(): Promise<string | null>` method.
    *   Delegate to `resolveRipgrepPath()` to determine the ripgrep binary path.

*   Ensure `RipGrepTool` uses `config.getRipgrepPath()` to determine the binary path:
    *   Return an error result with 'Cannot find bundled ripgrep binary' if `getRipgrepPath()` returns null.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.