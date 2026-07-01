Implement a fix for the PT023 rule in the Ruff linter to ensure it uses semantic import resolution for identifying pytest marker decorators. Update the related snapshot files to reflect the new behavior and line numbers after adding a new test case.

*   Update the PT023 rule:
    *   Use the semantic model's import-resolution mechanism (`resolve_qualified_name`) to identify pytest mark decorators.
    *   Ensure decorators that appear before the `import pytest` statement are not flagged by the PT023 rule.

*   Modify the `get_mark_decorators` function:
    *   Accept a `SemanticModel` as a second parameter.
    *   Use the semantic model to resolve qualified names and return only decorators that resolve to the `pytest.mark.*` namespace.
    *   Update all callers of `get_mark_decorators` to pass the semantic model.

*   Update snapshot files:
    *   In `PT023_default.snap`:
        *   Ensure the first violation appears at `PT023.py:51:1`, with subsequent violations at lines `56:1`, `63:5`, `69:5`, and `77:9`.
        *   Ensure the pre-import decorator at line 1 does not appear as a violation.
        *   Remove any `snapshot_kind` field from the snapshot header.
    *   In `PT023_parentheses.snap`:
        *   Ensure the first violation appears at `PT023.py:17:1`, with subsequent violations at lines `22:1`, `29:5`, `35:5`, and `43:9`.
        *   Ensure the pre-import decorator at line 1 does not appear as a violation.
        *   Remove any `snapshot_kind` field from the snapshot header.

*   Ensure both snapshot files have correct surrounding context lines matching the new line numbering in the modified `PT023.py` fixture.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.