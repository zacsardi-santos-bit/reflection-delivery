Correct the misuse of error-formatting verbs in logging calls across the codebase to ensure error messages are human-readable. Implement a codebase-wide check to enforce the correct usage of format verbs in logging calls, failing the build if incorrect usage is detected.

*   Update all logging calls in the following directories to use the %v format verb for error values:
    *   `pkg/`
    *   `cmd/`
    *   `test/`
    *   Ensure that no logging call uses the %w verb, which is reserved for error wrapping.
*   Specifically, correct the following in `pkg/gce-pd-csi-driver/node.go`:
    *   Replace %w with %v in warning-level logging calls related to EvalSymlinks failure.
    *   Replace %w with %v in warning-level logging calls related to device-disable failure during volume unstage.
*   Implement an automated check in the build process to detect and fail the build if any logging call uses %w instead of %v for error values.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.