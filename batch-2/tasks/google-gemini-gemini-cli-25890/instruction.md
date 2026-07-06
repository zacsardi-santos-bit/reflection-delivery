I'm working on fixing the home directory warning in the CLI tool.

*   The getUserStartupWarnings function must use the operating system's native home directory detection (via node:os) rather than any application-level home directory wrapper when determining whether the working directory is the user's home directory.

*   When the working directory is exactly the OS home directory (resolved with realpath), getUserStartupWarnings must return a warning with id 'home-directory', a message containing 'Warning you are running Gemini CLI in your home directory', and priority WarningPriority.Low.

*   When the home directory is a symbolic link and the working directory is that symlinked home path, getUserStartupWarnings must resolve symlinks (using fs.realpath) on both the workspace root and the home directory before comparing them, so the warning is correctly triggered.

*   When the home directory is a symbolic link and the working directory is a subdirectory of that symlinked home path, getUserStartupWarnings must NOT return the 'home-directory' warning.

*   When the GEMINI_CLI_HOME environment variable is set to a path that differs from the value returned by os.homedir(), and the working directory is that GEMINI_CLI_HOME path, getUserStartupWarnings must NOT return the 'home-directory' warning — home detection must use os.homedir() directly, not the GEMINI_CLI_HOME environment variable.

*   The path comparison for the home directory check must use path.resolve on both realpath-resolved paths to normalize them before comparing.


*   Interface details: Type: Function
Name: getUserStartupWarnings
Location: packages/cli/src/utils/userStartupWarnings.ts
Signature: getUserStartupWarnings(settings: object, workspaceRoot: string) -> Promise<StartupWarning[]>
Description: Returns an array of startup warnings for the user. For the home directory check, it compares the resolved real path of workspaceRoot against the resolved real path of the OS home directory (from node:os homedir). If they are equal (after path.resolve normalization), and no overriding conditions apply, it returns a warning with id 'home-directory', a message containing 'Warning you are running Gemini CLI in your home directory', and priority WarningPriority.Low.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.