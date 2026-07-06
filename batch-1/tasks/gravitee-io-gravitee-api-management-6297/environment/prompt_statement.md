I'm working on our CI/CD pipeline for publishing Helm charts and I've identified several bugs that need to be fixed.

First, the Helm chart version is being bumped as part of the release tagging commit, but it should actually be bumped in the subsequent "prepare next version" commit instead. Along with the version bump, the chart's changelog annotation should also be cleaned up at that point. Right now both of those happen at the wrong time.

Second, the Helm publishing job has a checkout ordering issue — it checks out the repository before credentials and tooling have been set up, instead of after. For real releases (not dry-run), the job also needs to explicitly check out the specific release tag before packaging the charts, which it currently doesn't do. Additionally, the job currently updates both the chart version and the application version in the chart metadata, but only the application version should be updated at publish time.

Third, the chart push commands use hardcoded filenames that embed the release version number, which will break when the version changes — these should use wildcard patterns instead.

Finally, I'd like pull request builds on our main branches to also publish the Helm chart to the internal repository in dry-run mode. This way, chart changes get validated as part of normal PR review. Currently the PR pipeline doesn't include this step at all.

Could you help fix these issues in the pipeline code?
