Implement changes to ensure that transactions failing due to oversized script arguments are classified as malformed. Update the logic to remove any special exceptions that prevent this classification, and clean up related code and constants.

*   Update the `is_malformed_tx` function in `util/types/src/core/tx_pool.rs`:
    *   Ensure it returns `true` for any `Reject::Verification` variant with `ErrorKind::Script`.
    *   Specifically, ensure it returns `true` for errors with the message "@@@VM@@@UNEXPECTED@@@ARGV@@@TOOLONG@@@".
*   Remove or update any code paths or test assertions that previously classified the oversized argument error as not malformed.
*   Remove the constant `ARGV_TOO_LONG_TEXT` from `util/types/src/core/error.rs`:
    *   Delete the constant definition.
    *   Remove any non-test file references or imports of this constant.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.