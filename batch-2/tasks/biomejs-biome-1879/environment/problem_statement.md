## Description

We need a new lint rule that detects when a file is both a test file and exports values or functions. Exporting from a test file is dangerous because any other file that imports from it will also cause all the tests in that file to run again, leading to confusing duplicate test executions that are hard to trace.

## Expected Behavior

- When a JavaScript or CommonJS file contains test framework function calls (indicating it is a test file), any export statements in that file should produce a lint warning.
- The rule should cover both the modern module export syntax and the CommonJS export object pattern, including direct assignments, bracket-notation assignments, and dot-notation property assignments on the exports object.
- If a file does not contain test framework calls, exports should be allowed without any warning, even if the same export patterns appear.
- Only genuine module export patterns should be flagged — unrelated property assignments on other objects should not be affected.

## Why This Matters

Exporting from test files can cause tests to run multiple times unexpectedly when those files are imported in other test files or application code. This is a well-known pitfall, and a lint rule would catch this mistake automatically before it causes confusing behavior in CI or test runners.
