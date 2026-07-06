## Description

When zizmor reports security findings in GitHub Actions workflows, users currently have no way to tell from the output which findings can be automatically remediated. All findings look the same in the output, even when some of them support an automatic fix. This means developers must manually investigate each finding to determine whether a fix is available, which slows down remediation.

## Expected Behavior

- For each finding that has an available automatic fix, the diagnostic output should include a clear note indicating that the finding is auto-fixable. This note should appear as part of the finding's annotations in the plain-text output.
- The overall findings summary line should include a count of how many findings are fixable. For example, if 2 out of 3 findings are fixable, the summary should reflect that fixable count alongside any existing suppressed count.
- Rules that support automatic remediation should be reported as fixable.
- Rules that do not have automatic fixes available should not show the auto-fix annotation and should not be counted as fixable in the summary.

## Why This Matters

Users who run zizmor on their GitHub Actions workflows benefit from being able to quickly triage findings. Knowing at a glance which problems can be resolved automatically versus which require manual effort helps teams prioritize their work and resolve security issues faster.
