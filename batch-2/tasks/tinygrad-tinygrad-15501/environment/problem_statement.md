## Description

The profiling CLI tool in the visualization utilities cannot be invoked programmatically from other Python code. The argument-parsing setup is embedded inside the script's launch block, and the main function terminates the process via an exit call rather than returning normally. This design makes it impossible to import and call the tool from within a test suite or from other modules while capturing its output.

## Expected Behavior

- The argument parser should be accessible as a standalone, importable function so that callers can construct parsed argument objects without launching a subprocess.
- The main logic function should accept the parsed arguments as a parameter and return cleanly, rather than calling process-exit. This allows callers to capture any stdout output.
- When the tool is asked to list available profiling devices, SQTT trace names should appear in the output so callers can discover them.
- When the tool is asked to display the instruction trace for a specific SQTT device, the output should have a header line identifying the clock column, and every subsequent data row should start with a numeric clock timestamp.

## Why This Matters

Without these changes, the CLI tool can only be used as a standalone script, making automated testing and programmatic integration impossible. Exposing the parser and accepting arguments explicitly enables the tool to be exercised in test environments and embedded in larger workflows that need to capture and validate its output.
