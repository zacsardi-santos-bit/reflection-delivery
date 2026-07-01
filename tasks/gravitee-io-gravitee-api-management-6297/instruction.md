Implement the necessary changes to the CI/CD pipeline for Helm chart releases to address versioning and job execution issues. Ensure correct sequencing of version bumps and job steps, and incorporate dry-run publishing for pull requests.

*   Modify the `ReleaseCommitAndPrepareNextVersionJob` class in `.circleci/ci/src/jobs/job-release-commit-and-prepare-next-version.ts`:
    *   Remove the Helm chart version update from the release commit block.
    *   In the prepare-next-version block, add commands to:
        *   Update the Helm chart version to the next version.
        *   Clean the `artifacthub.io/changes` annotation.

*   Update the `ReleaseHelmJob` class in `.circleci/ci/src/jobs/helm/job-release-helm.ts`:
    *   Move the repository checkout step to occur after environment setup steps.
    *   Add a 'Checkout tag {version}' step with the command `git checkout {version}` for non-dry-run releases.
    *   Ensure the 'Update Chart and App versions' step only updates the `appVersion` field in `helm/Chart.yaml`.
    *   Use wildcard patterns `apim-*.tgz` and `apim3-*.tgz` in Helm chart push commands.

*   Modify the `PullRequestsWorkflow` class in `.circleci/ci/src/workflows/workflow-pull-requests.ts`:
    *   Include a `ReleaseHelmJob` configured in dry-run mode for the master and 4.1.x branch workflows.
    *   Name the job "Publish Helm chart (internal repo)".
    *   Use the `cicd-orchestrator` context and require the "Setup" job.
    *   Set the environment's `isDryRun` flag to true when creating this job.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.