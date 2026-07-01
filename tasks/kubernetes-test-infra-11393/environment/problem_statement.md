## Description

The status reconciler currently triggers new required presubmit jobs for all open pull requests in every organization and repository it manages. When a new required presubmit job is added to configuration, the reconciler automatically queues that job for all open PRs in the affected repos. There is no way to exempt specific organizations or repositories from this automatic triggering behavior.

This creates a problem for teams that want to roll out new required checks incrementally across a large number of repositories. They may want the reconciler to handle status retirement and migration as usual, but defer the automatic triggering of new presubmit jobs for certain organizations or specific org/repo pairs.

## Expected Behavior

- The status reconciler should support a configurable list of organizations and org/repo pairs to exclude from automatic job triggering.
- When an org or org/repo is on the exclusion list, new presubmit jobs should **not** be triggered for pull requests in those repositories.
- Status retirement (removing old statuses) and status migration (renaming statuses) should still happen normally for excluded repos.
- When the exclusion list is empty, existing behavior is fully preserved.

## Why This Matters

Large Prow deployments may manage hundreds of repositories across multiple organizations. Being able to skip job triggering for specific orgs or repos during a rollout allows operators to gradually adopt new required presubmits without causing a sudden flood of triggered jobs across all managed repositories.
