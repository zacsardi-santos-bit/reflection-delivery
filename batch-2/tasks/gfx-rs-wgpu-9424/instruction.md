I'm working with the WGSL shader compiler and I've found that passing it a shader with extremely deeply nested constructs — like thousands of levels of nested function calls or thousands of levels of nested type parameter expressions — causes the parser to crash rather than report a useful error.

*   When the WGSL front-end parser encounters a shader with deeply nested function call expressions (e.g., thousands of levels of nested calls), it must detect that the parser recursion limit has been exceeded and report a graceful error instead of crashing or stack-overflowing.

*   When the WGSL front-end parser encounters a shader with deeply nested template/type-parameter expressions (e.g., thousands of levels of nested angle-bracket arguments), it must also detect the recursion limit and report the same graceful error.

*   The error produced when the recursion depth limit is exceeded must be formatted exactly as: 'error: internal WGSL front end error\n = note: Parser recursion limit exceeded\n\n' (the error header is 'error: internal WGSL front end error', followed by a note line ' = note: Parser recursion limit exceeded', followed by a blank line).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.