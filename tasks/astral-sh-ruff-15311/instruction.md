Implement changes to the docstring linter to recognize lowercase section headers as valid sections when they appear after a blank line. Ensure that all applicable lint rules are triggered for these headers, including capitalization and formatting checks.

*   Update the section detection logic in `crates/ruff_linter/src/docstrings/sections.rs`:
    *   Recognize lowercase section headers (e.g., 'returns:') as valid sections if they appear after a blank line.
    *   Ensure the 'Section name should be properly capitalized' rule (D405) is triggered for lowercase section headers.
    *   Ensure the 'Missing blank line after last section' rule (D413) is triggered if a lowercase section header is the last section and lacks a following blank line.
*   Maintain existing behavior for lowercase subsection headers:
    *   Do not alter the handling of lowercase subsection headers that appear directly after other section content.
*   Update snapshot files to reflect changes:
    *   Modify snapshot files in `crates/ruff_linter/src/rules/pydocstyle/snapshots/` to account for new violations and shifted line numbers.
    *   Adjust line numbers in snapshot files to reflect the addition of 11 new lines starting at approximately line 567 in the `sections.py` fixture.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.