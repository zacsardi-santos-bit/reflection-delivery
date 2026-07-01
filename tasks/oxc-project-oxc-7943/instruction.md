Implement support for object-style configuration entries in the "no restricted imports" lint rule. Ensure that both string and object formats for module restrictions are recognized and enforced consistently.

*   Update the lint rule to accept object-style configuration entries directly in the top-level configuration array.
    *   Each object must include a 'name' field specifying the module to restrict.
    *   Optionally, include a 'message' field for a custom diagnostic message.
*   Ensure that importing from a module specified in an object entry is flagged as a lint violation.
*   Allow the configuration array to contain a mix of plain string entries and object entries.
*   Verify that both configuration styles (string and object) are treated consistently, applying restrictions as expected.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.