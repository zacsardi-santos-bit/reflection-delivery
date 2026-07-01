## Description

The patch-apply functionality exists as a library but there is no standalone command-line binary that users or automation scripts can invoke directly. This makes the tool hard to use outside of the main application — for instance, when users or CI systems want to apply patches from the shell.

## Expected Behavior

- A standalone executable should be available that accepts a patch as either a command-line argument or via standard input (when no argument is given).
- When invoked with an argument, the tool should read the patch from that argument, apply it to the filesystem relative to the current working directory, and print a summary of changed files to standard output (indicating whether each file was added or modified).
- When invoked with no arguments, the tool should read the patch from standard input and behave identically.
- The tool should exit successfully (exit code 0) after a successful patch application.

## Why This Matters

Without a standalone binary, users must go through a larger hosting process to apply patches, which is cumbersome for scripting, testing, and standalone usage. Providing a dedicated binary — usable both via argument and piped input — opens the tool up for use in shell scripts, CI pipelines, and direct developer workflows.
