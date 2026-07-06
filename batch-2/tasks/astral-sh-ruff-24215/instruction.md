I'm working on a Python linter and I'd like to extend the rule that checks for mismatches between the number of format placeholders in a percent-style format string and the number of substitution values provided.

*   The percent-format positional count mismatch rule must flag a format string that has zero placeholders when the right-hand side is a non-empty literal integer (e.g., a plain number), reporting the diagnostic with message: "`%`-format string has 0 placeholder(s) but 1 substitution(s)"

*   The rule must flag a format string that has zero placeholders when the right-hand side is a non-empty tuple literal (e.g., a tuple with one or more elements), reporting the same diagnostic

*   The rule must flag a format string that has zero placeholders when the right-hand side is a variable name, regardless of whether that variable is defined or unknown, reporting the same diagnostic

*   The rule must flag a format string that has zero placeholders when the right-hand side is a function call or an attribute access expression, reporting the same diagnostic

*   The rule must NOT flag a format string that has zero placeholders when the right-hand side is an empty tuple `()` — this is the only allowed exception

*   The snapshot file at `crates/ruff_linter/src/rules/pyflakes/snapshots/ruff_linter__rules__pyflakes__tests__F507_F50x.py.snap` must be updated to include the new F507 diagnostics for each of the newly flagged cases, with exact line numbers and source context matching the fixture file


*   Interface details: Type: Function
Name: percent_format_positional_count_mismatch
Location: crates/ruff_linter/src/rules/pyflakes/rules/strings.rs
Description: Checks for mismatches between the number of positional placeholders in a percent-style format string and the number of substitution values provided. Must be extended to also flag the case where the format string has zero placeholders but the right-hand side is a non-empty value (non-empty literal, variable, function call, or attribute access). Empty tuple `()` as the right-hand side must remain allowed and must not trigger a diagnostic.

Type: Snapshot File
Name: ruff_linter__rules__pyflakes__tests__F507_F50x.py.snap
Location: crates/ruff_linter/src/rules/pyflakes/snapshots/ruff_linter__rules__pyflakes__tests__F507_F50x.py.snap
Description: Insta snapshot file that stores expected linter output for the F50x.py fixture. Must be updated to include the new F507 diagnostics for each newly flagged case. The new diagnostic message is: "`%`-format string has 0 placeholder(s) but 1 substitution(s)". There are 8 new diagnostics to add, one for each flagged expression in the fixture file at lines 57–66 (excluding the allowed `'hello' % ()` at line 68).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.