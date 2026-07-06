Implement the necessary fixes to address security and functionality issues in the sandboxed file I/O system. Ensure file paths are handled securely, access policies are correctly communicated, and error handling is improved.

*   On Windows:
    *   Use environment variables to safely pass file paths in sandbox commands.
        *   For '__write' commands, use PowerShell.exe with the argument '-Command' followed by '& { $Input | Out-File -FilePath $env:GEMINI_TARGET_PATH -Encoding utf8 }'.
        *   For '__read' commands, use PowerShell.exe with the argument '-Command' followed by '& { Get-Content -LiteralPath $env:GEMINI_TARGET_PATH -Raw }'.
    *   Ensure the GEMINI_TARGET_PATH environment variable equals the original file path, including special characters.

*   On Linux:
    *   Translate '__read' to '/bin/cat' and '__write' to '/bin/sh -c tee ...' in bwrap arguments.
    *   Use '--bind-try' to bind the parent directory when an allowed path does not exist.

*   On macOS:
    *   Translate '__read' to '/bin/cat' and '__write' to '/bin/sh -c tee ...' in the resulting args.
    *   Default workspaceWrite to false unless explicitly requested.

*   Implement access policies:
    *   In `readTextFile`, pass a policy object with allowedPaths to `prepareCommand`.
    *   In `writeTextFile`, pass a policy object with allowedPaths and additionalPermissions.fileSystem.write.

*   Improve error handling:
    *   Set the error code to 'ENOENT' when a sandboxed read fails due to a missing file, indicated by 'No such file or directory' or 'Could not find a part of the path'.

*   Ensure directory creation:
    *   In `EnterPlanModeInvocation.execute`, check for the existence of the plans directory and create it recursively if missing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.