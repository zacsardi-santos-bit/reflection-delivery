## Description

When teams are migrating Azure DevOps pipelines to GitHub, they currently have no way to test whether a pipeline will work after the migration without actually making the change permanent. If the migration fails, the team is left with a broken pipeline and has to manually undo the change. We need a way to safely test pipeline migrations before committing to them.

## Expected Behavior

- The pipeline rewiring command should support a test mode option that temporarily points a pipeline at a GitHub repository, triggers a real build, and then automatically restores the pipeline back to its original Azure DevOps configuration — all in one pass.
- A separate timeout option should control how long the command waits for the test build to complete.
- A new result model should track the full outcome of each test run, including: whether the rewiring succeeded, whether the restore succeeded, the build identity and URL, start and end times, the computed build duration, and computed status flags indicating whether the build succeeded, failed, is still running, or has completed.
- The build outcome status flags should reflect the outcome values returned by Azure DevOps, mapping specific API result and status values to computed boolean indicators for success, failure, completion, and running state.
- If the restore step fails, the error should be captured in the result's error message as a description of the restoration failure that includes the underlying error details, and the test result should indicate the restore was not successful — without aborting the overall test result return.
- If no pipeline ID is supplied, the test service should look it up by name automatically.

## Why This Matters

Teams migrating from Azure DevOps to GitHub need confidence that their pipelines will work before committing to permanent changes. A reversible test mode reduces the risk of breaking active pipelines during migration and provides clear, structured feedback on what succeeded and what needs attention.
