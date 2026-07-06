## Description

The tree-sitter test corpus format currently has no way to annotate individual test cases with metadata that controls how they execute. Every test case is always run regardless of the current platform, expected parse result, or desired parser. This makes it difficult to manage test suites in real-world scenarios.

## Problems

- There is no way to temporarily disable a test without deleting it entirely.
- Tests cannot be restricted to specific operating systems, so platform-specific parse behavior (e.g., Windows vs Unix newlines) cannot be handled cleanly.
- There is no lightweight way to assert that a piece of input is intentionally invalid without spelling out the full error-ridden parse tree.
- Multi-language repositories (e.g., a repo that provides parsers for both TypeScript and TSX, or XML and DTD) have no way to direct individual test cases to run against a specific parser.
- There is no way to stop the test run early after a critical failure.

## Expected Behavior

Test headers should support optional annotation lines placed between the test name and the closing delimiter. These annotations would control test execution:

- A "skip" annotation causes the test to be skipped without removing it.
- A "platform" annotation (with an OS name parameter) causes the test to run only on that operating system; multiple platform annotations combine with OR logic.
- A "fail-fast" annotation stops the entire test run if that test fails.
- An "error" annotation asserts that the parsed input contains a parse error, making it unnecessary to write out the expected error tree.
- A "language" annotation (with a language name parameter) directs the test to use that specific parser; multiple language annotations run the test with each named parser.

## Why This Matters

Without this feature, developers maintaining parsers for multiple platforms or multiple languages must work around the limitations by deleting tests, writing complex conditionals outside the test format, or tolerating noisy failures. The annotation system provides a first-class solution inside the test corpus format itself.
