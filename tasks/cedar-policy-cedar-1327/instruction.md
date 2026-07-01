Implement support for checking the existence of a chain of nested optional attributes in a single expression within Cedar policies. Ensure that the type checker and policy formatter handle these expressions correctly, and fix the error reporting for reserved keywords used as attribute names.

*   Update the policy formatter to handle multi-level attribute paths in existence check expressions:
    *   Format the expression compactly on a single line when it fits without comments.
    *   Expand the expression across multiple lines with progressive indentation when comments are present between segments.
    *   Wrap long attribute names after the first segment, placing each subsequent segment on its own line with appropriate indentation.

*   Enhance the type checker to validate policies with multi-level existence checks:
    *   Accept policies where the existence check covers the full attribute path and subsequent access matches exactly.
    *   Accept policies using multiple separate existence checks to guard nested optional attribute access.
    *   Reject policies where access goes deeper than the guarded path, reporting an unsafe optional attribute access error with the full access path in reverse order.
    *   Reject policies where a different attribute is accessed at the same level as the guarded attribute, suggesting the guarded attribute name in the error message.

*   Improve error reporting for reserved keywords used as attribute names:
    *   Ensure the parse error reports the source location as just the reserved keyword.
    *   Use the standard reserved identifier error message instead of 'invalid attribute name'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.