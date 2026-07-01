Implement the necessary changes to ensure compatibility and correct functionality of Unix utilities under Windows Subsystem for Linux (WSL) and fix the file status utility's timestamp reporting. Address the test failures by making the following updates:

*   Add a public function `is_wsl()` in `tests/common/util.rs`:
    *   Detect if the environment is WSL by checking `/proc/sys/kernel/osrelease` on Linux.
    *   Convert the file content to lowercase ASCII and return true if it contains "microsoft" or "wsl".
    *   Return false on non-Linux platforms or if the file cannot be accessed.
    *   Use conditional compilation with `#[cfg(target_os = "linux")]`.

*   Update the `stat` utility:
    *   Ensure the `%w` format specifier outputs the file birth/creation time as a human-readable timestamp from the Unix epoch. Output "-" if unavailable.
    *   Ensure the `%W` format specifier outputs the file birth/creation time in seconds since the Unix epoch. Output "0" if unavailable.
    *   Match the output of `stat -c %w` and `stat -c %W` with the system's `stat` command, allowing differences only when the system returns "-" or "0".

*   Modify test behaviors:
    *   Skip the `chgrp --reference` test if running as root or if `is_wsl()` returns true.
    *   Ensure normal `stat` format strings in tests on Linux do not include `%w` or `%W` due to variability in birth/creation time support.
    *   Avoid `%a`, `%d`, or `%f` in filesystem `stat` format strings on Linux to prevent race condition discrepancies.
    *   Use the test framework's command runner with preserved environment variables and `LANGUAGE=C` for expected `stat` output, instead of `std::process::Command`.

*   Ensure all Rust range patterns in the codebase use the inclusive range syntax (`..=`) and compile without errors under the project's Rust toolchain version.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.