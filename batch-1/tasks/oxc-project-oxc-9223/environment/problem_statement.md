## Description

The linter currently has no way to report when inline directive comments — the kind that suppress individual lint warnings on specific lines — are unnecessary. If a developer adds a suppression comment for a rule that would never have triggered on that line, or adds a re-enable comment that has no corresponding disable comment earlier in the file, those comments silently accumulate without any feedback. Over time, codebases end up with stale or redundant suppression annotations that add noise and confusion.

## Expected Behavior

- Users should be able to pass a command-line flag to enable reporting of unused directive comments.
- When enabled, the linter should warn about disable directives where no violation would have been reported on the targeted line.
- When enabled, the linter should warn about enable directives that have no matching disable directive.
- There should also be a variant of this option that accepts an explicit severity level (e.g., warning or error), instead of always using the default severity.
- Only one form of the option should be usable at a time.
- The option struct produced by parsing CLI arguments must expose a dedicated field that aggregates these settings.

## Why This Matters

Keeping suppression annotations accurate is important for code quality. Outdated disable comments can mislead reviewers into thinking a rule is genuinely problematic when it is not, and enable comments without matching disable comments are simply dead code. Giving developers a CLI-level tool to audit these comments makes it easier to maintain a clean and trustworthy linting configuration.
