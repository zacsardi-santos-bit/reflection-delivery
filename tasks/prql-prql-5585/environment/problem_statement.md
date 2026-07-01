## Description

When defining a named variable that holds a table or relation (a full pipeline result), and then referencing that variable in a comparison or filter expression as though it were a single scalar value, the PRQL compiler should detect this semantic error and reject the query with a clear, actionable error. Currently, instead of reporting an error, the compiler silently generates incorrect or misleading SQL output.

## Expected Behavior

- When a table-typed variable is used in a scalar context — such as one side of a comparison within a filter — the compiler must return an error rather than compiling successfully.
- The error message must clearly state that a table variable cannot be used as a scalar value.
- The error must include a help message pointing users toward valid alternatives: using a join, or inlining the subquery directly.
- The error must identify the specific location in the source query where the problematic reference occurs.

## Why This Matters

Users who accidentally reference a table-type variable as if it were a scalar get silently wrong SQL today. A clear, well-placed error message with an actionable suggestion makes it much easier to understand and fix this type of mistake, improving the overall developer experience.
