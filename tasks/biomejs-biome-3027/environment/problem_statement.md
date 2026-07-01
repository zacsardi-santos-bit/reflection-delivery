## Description

Teams using GitLab for continuous integration have no way to integrate the linter's diagnostics into GitLab's native code quality review workflow. GitLab supports a specific JSON format for code quality reports that, when produced by a CI job, causes diagnostic annotations to appear inline on merge requests. Without a dedicated output mode, users must either skip this feature entirely or build their own transformation layer.

## Expected Behavior

- A new output format option should be available for all major commands (code checking, linting, CI mode, and formatting)
- When the new output mode is selected for linting or checking commands, the tool should emit a structured JSON array where each entry describes a single diagnostic finding with its description, rule identifier, a unique fingerprint, severity level, and source location (file path and line number)
- When used with the formatting command, the output should be an empty JSON array (since formatting issues are not code quality findings in this context)
- The help text for all commands should reflect that this new output mode is available

## Additional Fix

Certain diagnostic messages were previously rendered with a line break between two clauses of the same sentence. This caused those messages to appear incorrectly formatted in structured outputs such as the XML-based test reporter and language server protocol responses. The messages should be normalized to a single line, with the two clauses joined by a space.

## Why This Matters

Native GitLab code quality integration means teams can see linting annotations directly in merge request diffs without extra tooling. The message normalization fix ensures all downstream consumers of diagnostic messages receive consistently formatted text.
