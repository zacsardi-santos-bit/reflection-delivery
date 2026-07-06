## Description

The Flutter repository has no automated check to enforce that deprecation notices are written in the required format. Developers marking APIs as deprecated often omit the version at which the deprecation occurred, use incorrect grammar (missing capital letter or trailing period), use wrong indentation, or write the annotation as a single-line form rather than the required multi-line form. This makes it difficult for library consumers to know when an API was deprecated and what migration path to follow.

## Expected Behavior

- An automated analysis tool should scan all Dart source files and validate every deprecation annotation against the standard format.
- A valid deprecation annotation must be written in multi-line form with a grammatically correct reason sentence (starting with a capital letter, ending with punctuation) and a separate line indicating the version after which the feature was deprecated.
- Violations should be reported as clear, per-line error messages that identify the file, line number, and specific problem.
- The tool should support a special inline comment to suppress the check for deprecation notices that intentionally deviate from the standard (such as special-purpose annotations that will never be removed), as well as a separate grandfathering mechanism for existing violations tracked in an issue.
- Existing test fixtures for other analysis checks were reorganized into a subdirectory to keep test inputs cleanly separated.

## Why This Matters

Consistent deprecation notices help the entire Flutter ecosystem: developers know exactly when a feature was deprecated, what to migrate to, and how long they have. Without automated enforcement, the quality of these notices degrades over time, leading to confusing or incomplete migration guidance.
