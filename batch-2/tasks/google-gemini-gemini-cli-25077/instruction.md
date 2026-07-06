Implement a manifest-based approach for the Windows sandbox manager to communicate filesystem access policies to the sandbox helper binary. Replace the current method of applying permissions with external system tool calls by writing two manifest files: one for allowed paths and one for forbidden paths. Pass these files to the helper binary using dedicated command-line flags.

*   Update the `prepareCommand` method in the `WindowsSandboxManager` class.
    *   Include a `--forbidden-manifest` flag at `args[2]` and a `--allowed-manifest` flag at `args[4]` in the returned `args` array.
    *   Ensure `args[3]` points to the forbidden manifest file and `args[5]` points to the allowed manifest file.
    *   Shift the command and its arguments to start at `args[6]` and `args[7]` respectively.

*   Create the allowed manifest file:
    *   List filesystem paths, one per line, including the sandbox working directory, explicitly allowed paths, paths from persistent policy, and additional write paths.
    *   Exclude drive root paths (e.g., 'C:\\', 'D:\\') and git worktree or git directory paths.
    *   Ensure paths appearing in both allowed and forbidden lists are excluded from the allowed manifest.

*   Create the forbidden manifest file:
    *   List all configured forbidden paths, including those not existing on disk.
    *   Include paths that appear in both allowed and forbidden lists.

*   Implement error handling:
    *   Throw an error if UNC paths (e.g., '\\\\server\\share\\path') are present in write or allowed paths.

*   Implement a cleanup function:
    *   Delete both the forbidden and allowed manifest files.
    *   Remove the shared parent directory containing the manifest files.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.