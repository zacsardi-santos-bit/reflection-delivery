## Description

The Ruff type checker currently only supports suppressing type-checking errors using a comment format that was introduced by other type checkers. We should add a native suppression comment format that is specific to this tool, giving users a way to suppress errors in a style that belongs to the Ruff ecosystem.

## Expected Behavior

- Users should be able to add an inline comment to suppress all type-checking errors on a given line.
- Users should also be able to suppress only specific diagnostic codes by listing them in the comment.
- Multiple diagnostic codes can be listed in a single suppression comment.
- The comment format should be flexible about whitespace — extra spaces around the separator or within the code list should be allowed.
- Whitespace between the comment marker and the keyword should be optional.
- A trailing comma after the last code in a list should be accepted.
- Syntax errors and diagnostic messages used for type introspection should not be suppressible via this mechanism.
- Invalid characters in code names should result in the suppression being ignored (the error remains visible).
- A missing separator between codes should result in an invalid suppression (the errors remain visible).
- An empty code list should suppress nothing.

## Related Fix

There is also a bug where a suppression comment appearing after another inline comment directive on a continuation line is not recognized. This should be fixed so that suppression comments work correctly in that context.

## Why This Matters

Having a Ruff-native suppression format gives users a clear way to distinguish errors suppressed for Ruff-specific reasons from those suppressed for interoperability with other tools. It also sets the foundation for future features like warning about unused suppressions.
