Implement an automated tool to check and enforce the correct format of deprecation notices in Dart files within the Flutter repository. Ensure that all deprecation annotations adhere to the specified multi-line format and report any violations with clear error messages.

*   Implement the `verifyDeprecations` function in `dev/bots/analyze.dart` with the signature `Future<void> verifyDeprecations(String workingDirectory)`.
    *   Recursively scan all `.dart` files in the specified directory for deprecation annotations.
*   Validate deprecation annotations:
    *   Ensure they use the multi-line form with '@Deprecated(' on its own line.
    *   Reason strings must start with a capital letter and end with '.', '!', or '?'.
    *   Include a version string line: 'This feature was deprecated after vX.Y.Z.'.
    *   Ensure the closing ')' is on its own line at the correct indent level.
    *   Indent reason and version lines exactly two spaces more than the '@Deprecated(' line.
*   Report specific errors for formatting issues:
    *   'Unexpected deprecation notice indent.' for incorrect indentation.
    *   'Deprecation notice does not match required pattern.' for incorrect annotation patterns.
    *   'Deprecation notice should be a grammatically correct sentence and start with a capital letter; see style guide.' for incorrect capitalization.
    *   'Deprecation notice should be a grammatically correct sentence and end with a period.' for missing punctuation.
    *   'End of deprecation notice does not match required pattern.' for incorrect closing line format.
*   Allow suppression of checks:
    *   Skip validation for lines with '// ignore: flutter_deprecation_syntax (see analyze.dart)'.
    *   Grandfather existing violations with '// ignore: flutter_deprecation_syntax, https://github.com/flutter/flutter/issues/' followed by an issue ID.
*   Output format for violations:
    *   Print a line of 56 '━' characters.
    *   List each error as '{filepath}:{1-based-line-number}: {error message}'.
    *   Include a footer line: 'See: https://github.com/flutter/flutter/wiki/Tree-hygiene#handling-breaking-changes'.
    *   End with another line of 56 '━' characters and a trailing newline.
    *   Exit with code 1 if violations are found.
*   Organize test fixture files under `dev/bots/test/analyze-test-input/root/`.
    *   Ensure both `verifyDeprecations` and `verifyNoMissingLicense` are called with this root subdirectory path during testing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.