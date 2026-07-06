I'm working with a linter that has a rule for detecting and merging redundant duplicate type-checking expressions — the pattern where you check whether an object matches one type or another type across two separate "or" conditions.

*   The duplicate isinstance call lint rule must detect all standard duplicate-isinstance patterns connected by 'or', including cases where the individual isinstance calls are wrapped in extra parentheses (e.g., a double-parenthesized outer expression, or a parenthesized second operand).

*   When generating an auto-fix that merges duplicate isinstance calls, the fix must preserve the target argument's original source text verbatim rather than re-rendering it through the AST. This is required for correctness when the target is an f-string containing escape sequences in its format spec (such as \x22 or \x7b) or lambda expressions in its interpolations.

*   When the type argument of an isinstance call is a tuple, the rule must flatten the tuple's elements (splice them) into the merged result rather than nesting tuples. Starred expressions inside those tuples must be kept as individual elements in the merged result.

*   When a single starred expression is the only element contributed to the merged tuple, the result must include a trailing comma to keep it a valid tuple (e.g., `isinstance(x, (*types,)) or isinstance(x, ())` fixes to `isinstance(x, (*types,))`).

*   When both calls have a starred tuple that each contribute one starred element, they are merged without a trailing comma (e.g., `isinstance(x, (*types,)) or isinstance(x, (*types,))` fixes to `isinstance(x, (*types, *types))`).

*   When one isinstance call uses an empty tuple as its type argument, the empty tuple contributes no elements to the merged result. The non-empty side's elements are used as-is (e.g., `isinstance(x, ()) or isinstance(x, int)` fixes to `isinstance(x, (int))`).

*   When both isinstance calls have an empty tuple type argument, the result keeps the empty tuple form (e.g., `isinstance(x, ()) or isinstance(x, ())` fixes to `isinstance(x, ())`).

*   When the outer boolean 'or' expression is enclosed in extra parentheses, the fix must replace only the range spanning from the start of the first operand (including any parentheses wrapping it) to the end of the last operand (including any parentheses wrapping it), so that dangling parentheses are absorbed. For example, `((isinstance(x, int)) or isinstance(x, str))` fixes to `(isinstance(x, (int, str)))` where the outer paren is preserved and the inner paren is absorbed.

*   When only the second operand of the boolean 'or' is parenthesized, the parentheses are absorbed into the replacement range, and no extra outer parens appear in the result. For example, `isinstance(x, int) or (isinstance(x, str))` fixes to `isinstance(x, (int, str))`.

*   For f-string targets, the fix must preserve the exact source bytes of the f-string. For example, `isinstance(f"{(lambda: 0)}", int) or isinstance(f"{(lambda: 0)}", str)` fixes to `isinstance(f"{(lambda: 0)}", (int, str))`, and `isinstance(f"{0:\x22}", int) or isinstance(f"{0:\x22}", str)` fixes to `isinstance(f"{0:\x22}", (int, str))`.


*   Interface details: Type: Function
Name: duplicate_isinstance_call
Location: crates/ruff_linter/src/rules/flake8_simplify/rules/ast_bool_op.rs
Signature: duplicate_isinstance_call(checker: &Checker, expr: &Expr)
Description: Detects and flags duplicate isinstance calls connected by a boolean 'or' operator on the same target, and generates an auto-fix that merges them into a single isinstance call. The fix must preserve the target's original source text verbatim (using source slicing rather than AST re-rendering) to correctly handle f-strings with escape sequences or lambda expressions. Tuple type arguments must be flattened (elements spliced into the merged tuple), including tuples containing starred expressions. Empty tuple type arguments must be handled gracefully.

Type: File (test fixture)
Name: SIM101.py
Location: crates/ruff_linter/resources/test/fixtures/flake8_simplify/SIM101.py
Description: Test fixture file containing Python source code that exercises the SIM101 (duplicate isinstance call) lint rule. The file includes regression test cases for f-strings with lambda interpolations, f-strings with escape sequences in format specs, starred tuple unpacking, empty tuple type arguments, and parenthesized isinstance expressions.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.