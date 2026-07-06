Implement changes to the Python formatter to ensure consistent handling of function return type annotations, particularly for generic types with bracket subscripts. Address the discrepancies in formatting between functions with and without parameters and align the output with the reference formatter's expectations.

*   Ensure functions with parameters:
    *   Never wrap subscript return types in additional outer parentheses, regardless of line length.
    *   Expand subscripts inline in both stable and preview modes.

*   Ensure functions without parameters:
    *   In stable mode, wrap subscript return types in outer parentheses if they do not fit on one line.
    *   In preview mode, expand subscripts inline without outer parentheses.
    *   Wrap return types in outer parentheses if the type name alone does not fit on the header line, in both modes.
    *   Do not apply 'hugging' layout to list literals used as return type annotations.
    *   For multiple-element subscripts:
        *   In stable mode, parenthesize the entire subscript.
        *   In preview mode, expand inline, fitting elements on one line or breaking them if necessary.
    *   For single overlong elements in subscripts:
        *   In stable mode, parenthesize the subscript.
        *   In preview mode, expand inline.
    *   For subscripts with a trailing comma, match the reference formatter by expanding inline without outer parentheses.
    *   Leave string or name return types unparenthesized if they fit on one line; otherwise, wrap them in parentheses.

*   For functions with parameters:
    *   Ensure string or name return types that do not fit on one line cause the parameter list to expand, but keep the return type unparenthesized.

*   For union return type annotations:
    *   Wrap in outer parentheses with each union element on its own line if they exceed the configured line width, for both functions with and without parameters.

*   Handle binary expression return type annotations:
    *   For no-parameter functions in stable mode, wrap in outer parentheses.
    *   In preview mode, format inline.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.