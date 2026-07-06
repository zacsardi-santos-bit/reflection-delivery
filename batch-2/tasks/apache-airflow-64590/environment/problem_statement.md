## Description

The Airflow development tooling makes repeated calls to the GitHub API when inspecting pull requests — fetching contributor profiles, CI check results, and workflow run statuses each time they are needed. This is slow and burns through API rate limits. We need a persistent, disk-based caching layer for these different types of PR data so that the tooling can serve repeated lookups quickly from local storage.

Each data category has different freshness requirements:
- Contributor/author profile information stays valid for about a week
- PR metadata and state is useful for a few hours
- Workflow run results are relevant for roughly 10 minutes
- CI check statuses for a given commit are immutable — they never need to expire

When a PR receives a new commit, all cached data for that PR (across all caches) should be invalidatable in a single call that reports how many entries were removed. The invalidation logic should also cleanly handle corrupt cache files by removing them.

We also need a utility that inspects a PR's diff and generates a list of review concerns automatically. It should flag potential issues such as:
- Very large diffs (many added lines)
- Missing test coverage (no test files changed)
- Addition of version annotations
- Lines that introduce breaking changes or mark functionality as deprecated
- Inconsistent exception handling patterns (many different exception types added)

A clean, small PR with tests present and none of the above signals should produce no concerns.

## Why This Matters

Without caching, the tooling is unnecessarily slow and API-rate-limited. Without SHA-based invalidation, reviewers could be shown stale data after a PR is updated. The diff analysis utility ensures reviewers are prompted to check important aspects of a PR without manual inspection.
