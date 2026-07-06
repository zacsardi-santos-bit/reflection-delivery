# Add CI Job Duration Regression Detection Script

## Description

We have no automated way to detect when our CI workflows start running significantly longer than their historical baseline. Currently, engineers have to manually review workflow run histories to notice when a CI job has regressed in performance. This is time-consuming and means regressions often go unnoticed until they cause serious disruption.

We need a script that:
- Fetches recent workflow run data from GitHub Actions
- Computes durations for individual jobs within runs
- Compares recent durations against a rolling historical baseline using a robust statistical method (not a simple average, which is easily skewed by outliers)
- Sends an alert to a Slack channel when a meaningful regression is detected

## Expected Behavior

- Short or cancelled runs (non-representative wall-clock durations) must be excluded from both the baseline and the latest sample so they don't distort the comparison.
- Regression detection should require BOTH a meaningful relative increase AND a minimum absolute increase, so short noisy jobs don't generate false alarms.
- A single unusually slow baseline run must not hide a genuine regression (the comparison must be outlier-resistant).
- Jobs that are new and have no baseline history must be silently skipped rather than flagged.
- When the GitHub API returns no data or fails, the script must handle the error gracefully and return empty results.
- Slack alert messages must include context: the affected branch, the regressed job names, and a header block for visual structure.

## Why This Matters

CI regressions can silently multiply developer wait times and block releases. Proactive, automated alerting lets the team catch and address these issues quickly instead of discovering them through complaints or manual auditing.
