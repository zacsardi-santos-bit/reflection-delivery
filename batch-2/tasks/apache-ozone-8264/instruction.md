Update the resource name validation logic to prioritize length checks over content-based checks. Ensure that the error message clearly communicates when a name's length is the issue.

*   Implement length validation in `HddsClientUtils.verifyResourceName`:
    *   Check if the resource name is below the minimum allowed length of 3 characters.
    *   If the length is invalid, throw an `IllegalArgumentException`.
    *   Ensure the exception message includes the phrase 'length is illegal'.
*   Prioritize length validation over content-based checks:
    *   If a name is both too short and consists entirely of digits, ensure the error message is about the length being illegal, not about the name being all-numeric.
*   Ensure the validation logic consistently communicates length issues before any other content-based validation errors.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.