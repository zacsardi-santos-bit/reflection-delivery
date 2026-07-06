I'm working on the ruff linter and I've found a bug in the automatic fix for the rule that removes empty or trivial "finally" clauses.

*   The lint rule that detects empty or trivial 'finally' clauses must correctly detect and flag bare try-finally blocks where the 'finally' body contains only 'pass' (or '...'), even when the 'try' body contains lines starting with a formfeed character (\x0C).

*   The automatic fix for the empty finally clause rule must produce syntactically valid Python when applied to a bare try-finally block whose try body contains lines starting with formfeed characters. Specifically, removing the try-finally wrapper from a block like 'try:\n    1\n    2\nfinally:\n    pass' must yield '1\n2' — formfeed characters at the start of a line must be preserved, but the indentation introduced by the try block must be removed.

*   The dedent_to function in crates/ruff_python_trivia/src/textwrap.rs must handle lines starting with formfeed characters (\x0C) correctly: when computing the baseline indentation level, formfeed characters at the start of a line must be stripped before measuring whitespace indentation; when applying dedentation, any leading formfeed characters must be preserved and re-prepended after the indentation is removed.

*   The snapshot file at crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__preview__RUF072_RUF072.py.snap must be updated to include the new expected diagnostic output for the formfeed case: a fixable diagnostic (marked [*]) at line 178 of RUF072.py, with a diff showing removal of 'try:', '    1', '    2', 'finally:', '    pass' and replacement with '1', '2'.


*   Interface details: Type: Function
Name: dedent_to
Location: crates/ruff_python_trivia/src/textwrap.rs
Signature: dedent_to(text: &str, indent: &str) -> Option<String>
Description: Reduces the indentation of text to match the given indent string. Must be updated to correctly handle lines starting with formfeed characters (\\x0C): when determining the baseline indentation, leading formfeed characters must be stripped before measuring whitespace; when applying dedentation, leading formfeed characters must be preserved and re-added after the indentation adjustment. Returns None if the existing indentation is less than the target indent.

Type: Snapshot File
Name: ruff_linter__rules__ruff__tests__preview__RUF072_RUF072.py.snap
Location: crates/ruff_linter/src/rules/ruff/snapshots/ruff_linter__rules__ruff__tests__preview__RUF072_RUF072.py.snap
Description: Snapshot file that records the expected linter output for the RUF072 fixture. Must be updated to include the new diagnostic entry for the formfeed test case: a fixable ([*]) "Empty `finally` clause" diagnostic at line 178 of RUF072.py, with a fix diff that removes the try-finally wrapper (lines "try:", "    1", "    2", "finally:", "    pass") and replaces them with the unwrapped body ("1", "2").


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.