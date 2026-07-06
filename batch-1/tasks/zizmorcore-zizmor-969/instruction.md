Improve the obfuscation detection in a GitHub Actions security linting tool by refining diagnostic spans and messages, and adding detection for computed index expressions. Ensure the tool provides precise and concise feedback for users.

*   Update diagnostic spans for constant expressions:
    *   Ensure the span covers only the inner expression content, excluding surrounding delimiters.
    *   Report the column number at the start of the inner content.
    *   Use the message 'can be replaced by its static evaluation' for these findings.

*   Update diagnostic spans for constant-reducible subexpressions:
    *   Ensure the span covers only the reducible subexpression, not the entire enclosing expression.
    *   Report the column number at the start of the subexpression.
    *   Use the message 'can be reduced to a constant' for these findings.

*   Implement detection for computed index expressions in pedantic mode:
    *   Detect when a dynamic expression is used as an index key in an object or array.
    *   Report the finding at the column of the opening bracket of the index access.
    *   Ensure the diagnostic span covers the bracket and the dynamic key expression.
    *   Use the message 'index expression is computed'.
    *   Classify these findings with 'low' severity and 'High' audit confidence.
    *   Ensure this detection is only active under the '--persona=pedantic' flag.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.