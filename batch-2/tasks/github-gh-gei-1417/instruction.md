Add a test mode to the existing pipeline rewiring command to safely test Azure DevOps pipeline migrations to GitHub. Implement a result model to track test outcomes and ensure the command supports timeout and dry-run options.

* Implement `PipelineTestResult` class in `src/Octoshift/Models/PipelineTestResult.cs`:
    * Include settable properties: `string AdoOrg`, `string AdoTeamProject`, `string AdoRepoName`, `string PipelineName`, `int PipelineId`, `string PipelineUrl`, `int? BuildId`, `string BuildUrl`, `string Status`, `string Result`, `DateTime StartTime`, `DateTime? EndTime`, `string ErrorMessage`, `bool RewiredSuccessfully`, `bool RestoredSuccessfully`.
    * Include computed properties:
        * `TimeSpan? BuildDuration` returns `EndTime - StartTime` or `null` if `EndTime` is `null`.
        * `bool IsSuccessful` returns `true` if `Result` is "succeeded" or "partiallySucceeded" (case-insensitive), otherwise `false`.
        * `bool IsFailed` returns `true` if `Result` is "failed" or "canceled" (case-insensitive), otherwise `false`.
        * `bool IsCompleted` returns `true` if `Result` is non-null and non-empty.
        * `bool IsRunning` returns `true` if `Status` is "inProgress" or "notStarted" (case-insensitive).

* Implement `PipelineTestService` in `src/Octoshift/Services/PipelineTestService.cs`:
    * Method `Task<PipelineTestResult> TestPipeline(PipelineTestArgs args)`:
        * Throw `ArgumentNullException` with 'args' message if `args` is `null`.
        * If `PipelineId` is not provided in `PipelineTestArgs`, look up by name and set it in the result.
        * Perform test workflow: retrieve original pipeline info, rewire to GitHub, queue a build, restore to ADO, monitor and return build status.
        * Populate result properties: `AdoOrg`, `AdoTeamProject`, `PipelineName`, `PipelineId`, `AdoRepoName`, `BuildId`, `BuildUrl`, `Status`, `Result`, `RewiredSuccessfully`, `RestoredSuccessfully`, `StartTime`, `EndTime`.
        * If `RestorePipelineToAdoRepo` raises an exception, set `RestoredSuccessfully` to `false` and `ErrorMessage` to 'Failed to restore: ' plus exception message, and return result without re-throwing.

* Update `RewirePipelineCommand` in `src/ado2gh/Commands/RewirePipeline/RewirePipelineCommand.cs`:
    * Add optional options: `Option<bool> DryRun { get; } = new("--dry-run")` and `Option<int> MonitorTimeoutMinutes { get; } = new("--monitor-timeout-minutes")`.
    * Ensure total options count is 11.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.