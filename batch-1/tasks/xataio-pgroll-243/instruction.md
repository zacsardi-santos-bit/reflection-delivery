Create a mechanism to verify the exact category of database constraint violations in your testing framework. Implement named constants for each type of violation to ensure tests can specify and check the expected failure reason explicitly.

*   Create a new file at `pkg/testutils/error_codes.go` within the testutils package.
*   Define four exported string constants in this file to represent PostgreSQL constraint violation error code names:
    *   `FKViolationErrorCode` should be set to "foreign_key_violation".
    *   `CheckViolationErrorCode` should be set to "check_violation".
    *   `NotNullViolationErrorCode` should be set to "not_null_violation".
    *   `UniqueViolationErrorCode` should be set to "unique_violation".
*   Ensure these string values match exactly with the error code names returned by the PostgreSQL driver's error `Code.Name()` method for the respective constraint violation types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.