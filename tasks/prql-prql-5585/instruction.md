Implement a feature in the PRQL compiler that detects and handles the misuse of table-type variables in scalar contexts. Ensure the compiler returns a clear error message when such misuse occurs.

*   Detect when a named variable holding a table or relation is used in a scalar context, such as in a comparison or filter expression.
    *   Ensure the compiler identifies this misuse and returns an error instead of generating SQL output.
*   Return a compilation error message with the exact text: 'table variable cannot be used as a scalar value'.
*   Include a help hint in the error message with the exact text: 'use a join instead, or inline the subquery'.
*   Identify and report the specific location in the source query where the table variable is incorrectly referenced.
    *   Ensure the error span points to the exact token position of the misuse.
*   Modify the compile function to return an error result for inputs where a table variable is used as a scalar, preventing successful SQL compilation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.