## Description

We need a new utility script in the `utils/` directory that automatically posts and updates CI run summaries on GitHub pull requests. Currently, when a CI run completes, there is no automated way to inject a structured overview of the results into the PR description. Developers have to manually check the CI system to find out how many tests ran, how many failed, how long the run took, and what the overall conclusion was.

## Expected Behavior

- After a CI run completes, the PR description should be automatically updated with:
  - A status badge at the top of the PR body linking to a monitoring dashboard
  - A detailed recap section at the bottom summarizing the run (result, job count, test count, failure count, and duration)
- If metrics are not yet available from the monitoring backend, the recap should fall back to information from the workflow run itself
- If a code quality check job failed, the recap should include a prominent warning about it
- On subsequent CI runs, the badge and recap blocks should be updated in-place rather than duplicated
- Old-format dashboard comments left by previous versions of the script should be cleaned up automatically

## Details

The script needs utility functions to:
- Format numbers with thousands separators and durations in a human-readable way (e.g. "1h 1m", "9m 59s", "1,234")
- Query a Prometheus-compatible metrics backend to retrieve run-level statistics
- Paginate through the GitHub API to find PRs, jobs, and comments
- Safely escape strings for use in monitoring queries
- Inject or replace delimited blocks within PR body text without disturbing surrounding content

## Why This Matters

Contributors rely on PR checks to understand whether their changes broke anything. Having the CI results embedded directly in the PR description reduces context-switching and makes it immediately obvious whether a run passed, how many tests were affected, and whether any quality checks failed.
