## Description

The Python parser does not detect or report an error when a non-triple-quoted f-string contains a line break inside its replacement field (the interpolated expression part) when targeting Python versions older than 3.12. Python 3.12 introduced support for this syntax, but on Python 3.11 and earlier it was always invalid. The parser currently passes silently over this case rather than flagging it as unsupported syntax for the target version.

## Expected Behavior

- When targeting Python < 3.12, a non-triple-quoted f-string with a line break in a replacement field should produce an unsupported syntax error, pointing to the opening brace of the affected field.
- The error message should follow the same pattern used for other version-gated f-string features and should explain that the syntax was added in Python 3.12.
- Triple-quoted f-strings with multiline replacement fields should **not** trigger this error on any Python version, since they have always supported multiline content.
- When targeting Python 3.12 or later, this syntax should be accepted as valid with no errors.
- If a replacement field already has a backslash or comment error, the line-break error should not additionally be reported for the same field.

## Why This Matters

Users who run the linter or formatter targeting Python < 3.12 should get accurate feedback when their code uses syntax that is only valid on newer Python versions. The current silent acceptance of multiline replacement fields in non-triple-quoted f-strings means that version-specific syntax errors go undetected, potentially shipping code that is incompatible with the declared target Python version.
