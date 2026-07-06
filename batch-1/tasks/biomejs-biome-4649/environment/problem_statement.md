## Description

The linter's suppression comment system has two correctness issues that need to be fixed.

**Issue 1: Redundant range suppressions are not detected**

When a file already has a file-wide suppression that disables a specific rule throughout the entire file, any range-based suppression comments (a start/end pair) targeting the same rule are silently treated as if they have effect. Developers may not realize these range suppressions are completely redundant. The linter should detect such cases and flag the range suppression as having no effect, pointing the developer to the file-wide suppression that is already handling that rule.

**Issue 2: Misplaced file-wide suppressions still suppress rules**

When a file-wide suppression comment appears somewhere other than the very beginning of the file, the linter currently warns about the misplacement but still silently applies the suppression — the lint rules remain suppressed as if the comment were valid. This is incorrect behavior. A misplaced file-wide suppression should truly have no effect: the lint violations it was trying to hide should be reported as real errors, and the command should exit with an error status rather than silently succeeding.

## Expected Behavior

- A range suppression that duplicates coverage already provided by a file-wide suppression should be flagged as a warning with a message explaining it has no effect and pointing to the suppression comment that is actually in use.
- A misplaced file-wide suppression should produce a warning about the misplacement AND cause the actual lint violations to be reported as errors, resulting in a non-zero exit code.

## Why This Matters

These issues can cause developers to believe their code is being properly checked when it is not, or leave unnecessary suppression comments in the codebase without any feedback that they are doing nothing.
