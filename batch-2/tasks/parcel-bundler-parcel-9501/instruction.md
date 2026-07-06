Update the bundler's macro system to correctly handle and report errors related to macro module loading failures. Ensure accurate error messages, correct error locations, and proper behavior in watch mode.

*   Modify the error diagnostic message for macro loading failures:
    *   Begin the message with 'Error loading macro: ' followed by the specific error message.
*   Adjust the error code highlight location for resolution failures:
    *   Point to the import statement rather than the macro call site.
    *   Ensure the highlighted range spans the entire import declaration.
*   Emit only one error diagnostic when a macro file fails to load and the same macro is called multiple times in a single bundle:
    *   Ensure the total number of diagnostics is exactly 1.
*   In watch mode, handle macro file load errors and subsequent corrections:
    *   On initial load error, trigger a 'buildFailure' event.
    *   After correcting the macro file, trigger a 'buildSuccess' event.
    *   Ensure the output bundle contains the result of executing the macro, with the output variable assigned a numeric value.
*   Distinguish between load errors and execution errors:
    *   Use 'Error loading macro: ' for load errors (e.g., module not found, syntax errors).
    *   Use 'Error evaluating macro: ' for execution errors (e.g., errors during macro invocation).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.