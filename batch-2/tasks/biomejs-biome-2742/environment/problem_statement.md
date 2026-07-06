## Description

The tool currently supports two output formats for diagnostics: a detailed per-issue format and a structured machine-readable format. Neither is well-suited for quickly understanding the overall health of a codebase when there are many files and many types of issues.

A new reporting mode is needed that aggregates and summarizes diagnostics by category, giving developers a compact overview of problems across all files rather than listing every individual issue.

## Expected Behavior

When the new summary reporting mode is selected:

- For formatting issues: the output should list the names of all files that need to be reformatted, grouped under a "Formatter" heading.
- For import-ordering issues: the output should list all files whose imports need sorting, grouped under an "Organize Imports" heading.
- For linting issues: the output should present a table showing each triggered rule and a breakdown of how many errors, warnings, and informational messages it produced, grouped under an "Analyzer" heading.
- Each command (check, ci, format, lint) should show only the sections relevant to what it does.
- The output should conclude with a footer showing the total number of files processed and errors found.
- The help text for all commands should document this new reporter option alongside the existing options.

## Why This Matters

When working with large codebases, developers need a bird's-eye view of code quality issues. Scrolling through hundreds of individual diagnostics is impractical. A summary mode allows teams to quickly assess scope — how many files need formatting, which lint rules are most commonly violated, and how many total issues exist — without getting lost in per-line detail.
