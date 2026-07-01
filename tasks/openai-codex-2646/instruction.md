Implement a standalone command-line binary for the existing Rust library to apply patches directly from the shell or scripts. Ensure it can accept patch data as a command-line argument or from standard input, and provide feedback on the operation's success.

*   Define a binary target in the Cargo package:
    *   Add a `[[bin]]` section in `codex-rs/apply-patch/Cargo.toml` with `name = "apply_patch"` and `path = "src/main.rs"`.

*   Add necessary dependencies:
    *   Include `assert_cmd = "2"` as a dev-dependency in `codex-rs/apply-patch/Cargo.toml` to facilitate CLI integration testing.

*   Implement the `apply_patch` binary functionality:
    *   When invoked with a single positional argument containing a patch string:
        *   Apply the patch to the current working directory.
        *   Exit with code 0 on success.
        *   Print to stdout: 'Success. Updated the following files:\nA {filename}\n' for each added file.
        *   Print to stdout: 'Success. Updated the following files:\nM {filename}\n' for each modified file.
    *   When invoked with no arguments:
        *   Read the complete patch payload from standard input.
        *   Apply the patch as described above, with identical output and exit code behavior.

*   Handle specific patch directives:
    *   For 'Add File' patches (using '*** Add File:' directive):
        *   Create the specified file with content from lines prefixed with '+'.
    *   For 'Update File' patches (using '*** Update File:' directive with '@@' hunks):
        *   Replace lines prefixed with '-' with corresponding lines prefixed with '+' in the existing file.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.