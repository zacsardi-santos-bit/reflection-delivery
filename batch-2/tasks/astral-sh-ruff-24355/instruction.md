I've been looking at how the Python parser handles f-strings that span multiple lines inside their expression parts.

*   When parsing a non-triple-quoted f-string whose replacement field contains a line break (newline or carriage return), and the target Python version is earlier than 3.12, the parser must emit an unsupported syntax error with the message 'Cannot use line breaks in non-triple-quoted f-string replacement fields on Python X.XX (syntax was added in Python 3.12)'.

*   The unsupported syntax error for a line break in a replacement field must be positioned at the opening '{' character of that replacement field (a range of exactly one character).

*   Triple-quoted f-strings whose replacement fields contain line breaks must NOT trigger the line-break syntax error on any Python version, because triple-quoted f-strings have always allowed multiline content.

*   On Python 3.12 and later, non-triple-quoted f-strings with line breaks in their replacement fields must parse as valid syntax with no unsupported syntax error emitted.

*   When a replacement field already contains a backslash or a comment that triggers its own unsupported syntax error, the line-break error must NOT also be emitted for that same replacement field.

*   The fixture file for invalid f-string syntax on Python < 3.12 (crates/ruff_python_parser/resources/inline/err/pep701_f_string_py311.py) must include a test case: a non-triple-quoted f-string with a line break inside its replacement field.

*   The fixture file for valid f-string syntax on Python < 3.12 (crates/ruff_python_parser/resources/inline/ok/pep701_f_string_py311.py) must include a test case: a triple-quoted f-string with a line break inside its replacement field.

*   The fixture file for valid f-string syntax on Python 3.12 (crates/ruff_python_parser/resources/inline/ok/pep701_f_string_py312.py) must include a test case: a non-triple-quoted f-string with a line break inside its replacement field.

*   The formatter must also detect and report this error (line breaks in non-triple-quoted f-string replacement fields) when formatting code that targets Python versions before 3.12.


*   Interface details: ## Fixture Files to Modify

The snapshot tests exercise these fixture files by name. Each must be updated with a new test case:

**File:** `crates/ruff_python_parser/resources/inline/err/pep701_f_string_py311.py`
- Add a new non-triple-quoted f-string whose replacement field contains a line break (e.g., `f"{\n    1\n}"`), positioned before the existing line-continuation test case.
- This will cause the parser (targeting Python 3.11) to emit the new error.

**File:** `crates/ruff_python_parser/resources/inline/ok/pep701_f_string_py311.py`
- Add a new triple-quoted f-string whose replacement field contains a line break (e.g., `f"""{\n    1\n}"""`), positioned before the existing `"escape outside of \t"` test case.
- This must parse as valid (no error) on Python 3.11.

**File:** `crates/ruff_python_parser/resources/inline/ok/pep701_f_string_py312.py`
- Add a new non-triple-quoted f-string whose replacement field contains a line break (e.g., `f"{\n    1\n}"`), positioned before the existing line-continuation test case.
- This must parse as valid (no error) on Python 3.12.

---

## Error Message

The exact error message string produced by the parser (and included in snapshots) must be:

```
Cannot use line breaks in non-triple-quoted f-string replacement fields
```

(The test framework appends `on Python X.XX (syntax was added in Python 3.12)` automatically.)

---

## Implementation Location

**File:** `crates/ruff_python_parser/src/error.rs`

A new variant `LineBreak` must be added to the existing `FStringKind` enum. Its `Display` implementation must produce the exact message: `"Cannot use line breaks in non-triple-quoted f-string replacement fields"`.

**File:** `crates/ruff_python_parser/src/parser/expression.rs`

The existing per-replacement-field validation code must be extended to:
- Scan for line breaks (`\n` or `\r`) within a non-triple-quoted replacement field's source range.
- Skip this check when the field already has a backslash or comment error.
- When a line break is found (and the target Python version is < 3.12), emit an `UnsupportedSyntaxError` of kind `Pep701FString(FStringKind::LineBreak)` with a range pointing to the opening `{` of the replacement field (a single-character range).
- The helper function `check_fstring_comments` should be updated to return `bool` (indicating whether any comment was found) so the caller can use it to gate the line-break check.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.