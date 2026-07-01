Update the blank line style checker in the Python linter to handle lines consisting only of whitespace without crashing. Ensure these lines are treated as blank lines for rule evaluation and update the snapshot files to reflect the corrected behavior and line numbers.

*   Modify the `BlankLines` enum in `blank_lines.rs`:
    *   Remove the assertion `assert_eq!(range.end(), line_range.start())` in the `Many` variant implementation.
    *   Ensure lines with only whitespace are treated as blank lines, equivalent to empty lines.

*   Implement rule-specific behavior:
    *   Ensure a function body with one whitespace-only blank line followed by a wrongly-indented comment produces no blank-line violation.
    *   Report an E303 violation when two methods in a class are separated by two lines containing only tabs.
    *   Report an E303 violation when a function body has one empty line followed by a line with a single space.

*   Update snapshot files to reflect new behavior:
    *   `ruff_linter__rules__pycodestyle__tests__E301_E30.py.snap`: Adjust line numbers for 9 new fixture lines before the E301 test section.
    *   `ruff_linter__rules__pycodestyle__tests__E302_E30.py.snap`: Adjust line numbers for ~23 new fixture lines affecting the E302 section.
    *   `ruff_linter__rules__pycodestyle__tests__E303_E30.py.snap`: 
        *   Include two new E303 violations: 
            *   At the second method definition in the tab-indented class fixture.
            *   At the pass statement in the space-only-blank-line fixture.
        *   Adjust line numbers for existing E303 violations.
    *   `ruff_linter__rules__pycodestyle__tests__E304_E30.py.snap`: Adjust line numbers for new fixture lines before the E304 test section.
    *   `ruff_linter__rules__pycodestyle__tests__E305_E30.py.snap`: Adjust line numbers for new fixture lines before the E305 test section.
    *   `ruff_linter__rules__pycodestyle__tests__E306_E30.py.snap`: Adjust line numbers for new fixture lines before the E306 test section.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.