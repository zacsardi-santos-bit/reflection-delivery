## Description

The CI/CD pipeline for releasing Helm charts has a versioning sequencing problem: when we tag a new release, the pipeline updates the Helm chart's version as part of the release commit itself. This is incorrect — the chart version should advance when we prepare the _next_ development snapshot, not when we tag the current release. Additionally, when the chart version is bumped for the next cycle, the changelog annotation in the chart metadata should also be cleaned at the same time.

There are also a few bugs in the Helm publishing job:

1. The repository checkout step runs too early in the job — before credentials and tools have been set up. It needs to be moved to after the setup steps.
2. For real (non-dry-run) releases, the job needs to explicitly check out the tagged version before working with chart files, but currently there is no such step.
3. The chart publishing job updates both the chart version and the app version in the chart metadata, but only the app version should be updated at publish time — the chart version bump was already handled elsewhere.
4. The commands that push packaged chart files to the registry use hardcoded version-specific filenames, which will break if the filename changes. They should use wildcard patterns instead.

Finally, pull request builds on main development branches should also publish the Helm chart to the internal repository in dry-run mode, so that chart changes can be validated as part of the normal PR review process. Currently this step is missing from the PR pipeline.

## Expected Behavior

- The Helm chart version is only bumped in the "prepare next version" commit, not during the release tagging commit
- The chart changelog annotation is cleaned up at the same time as the version bump
- The Helm publishing job checks out the repository after, not before, setting up tools and credentials
- For production releases, the job explicitly checks out the release tag before packaging charts
- Only the app version (not the chart version) is updated during the publish step
- Chart archive push commands use wildcard file patterns
- PR pipelines for main branches include a dry-run Helm chart publishing step

## Why This Matters

Getting the sequencing wrong means the Helm chart version in the repository is incorrect after a release — it reflects the released version rather than the upcoming next version. The missing checkout ordering and tag-checkout steps could cause the wrong chart files to be packaged and published.
