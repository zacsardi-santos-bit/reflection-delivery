Ensure that the RustPython interpreter evaluates the truthiness of operands in compound boolean expressions at most once per use-site in conditional statements. This applies to 'if' statements, 'while' loops, and 'assert' statements, preserving Python's short-circuit evaluation semantics.

*   Implement single-evaluation semantics for compound boolean expressions:
    *   In 'if' statements, ensure each operand in 'and' or 'or' conditions is evaluated for truthiness at most once.
    *   In 'while' loops, ensure each operand in 'and' or 'or' conditions is evaluated for truthiness at most once per iteration.
    *   In 'assert' statements, ensure each operand in 'and' or 'or' conditions is evaluated for truthiness at most once.
*   Preserve short-circuit evaluation:
    *   In an 'or' condition, stop evaluating further operands once a truthy operand is found.
    *   In an 'and' condition, stop evaluating further operands once a falsy operand is found.
*   Validate the implementation by ensuring the test file 'tests/snippets/extra_bool_eval.py' runs without exceptions in the RustPython interpreter.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.