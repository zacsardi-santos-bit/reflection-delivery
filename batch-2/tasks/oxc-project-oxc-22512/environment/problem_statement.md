## Description

The formatter crashes (panics) when JSDoc comment formatting and import sorting are both enabled at the same time. Multi-line JSDoc comments contain internal line breaks that the import-sorting logic incorrectly interprets as real source line boundaries, causing it to enter an inconsistent state and trigger a panic. A separate but related crash also occurs when a non-import comment (such as a trailing single-line comment) appears after an import block while import sorting is active.

## Expected Behavior

- When JSDoc comment formatting and import sorting are both active, the formatter should complete successfully without panicking
- Single-line JSDoc comments before imports should be formatted normally and imports should be sorted alphabetically
- Multi-line JSDoc comments (including those with documentation tags) before imports should be formatted normally and imports should be sorted alphabetically
- A trailing comment appearing after an import block should be preserved correctly without causing a crash when import sorting is enabled

## Why This Matters

Users who want both their JSDoc comments cleaned up and their imports sorted cannot currently use both features together — enabling either feature while the other is active will crash the entire formatter. This is a basic usability issue that should be fixed so both features can be used safely in combination.
