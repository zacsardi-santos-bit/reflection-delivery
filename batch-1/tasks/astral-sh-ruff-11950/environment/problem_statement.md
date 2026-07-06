# Linter Stops Reporting Violations After First Syntax Error

## Description

When a Python file contains a syntax error, the linter currently stops processing tokens as soon as the first error token is encountered. This means any rule violations that appear elsewhere in the file — including in sections that are entirely valid — are silently dropped. Users only see the first syntax error, not the full picture.

## Expected Behavior

- The linter should continue analyzing the entire file even when syntax errors are present.
- Both syntax errors and rule violations found anywhere in the file should be reported together.
- Rules that track indentation or code structure (such as blank-line rules) should correctly recognize what is "top-level" code even when unclosed delimiters or other parse failures are present.
- Rules that inspect string tokens should correctly handle files containing unterminated strings: violations inside properly terminated strings should still be reported; characters inside unterminated strings should not produce false positives.

## Why This Matters

Developers often work with partially-valid files — for example, when adding new syntax, refactoring incomplete code, or using language features that the parser recovers from gracefully. Silently dropping all non-syntax violations from such files gives an incomplete and misleading picture. By continuing past syntax errors, the linter provides actionable feedback even for files that aren't fully parseable.
