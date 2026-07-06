Add support for configuring a recurring cron schedule in the Scaleway Terraform provider's serverless job definitions. Implement a 'cron' block in the job definition resource to specify a schedule expression and timezone. Ensure that the cron schedule is accurately reflected in the Terraform state after creation, updates, or removal.

*   Update the `scaleway_job_definition` resource to include an optional 'cron' block attribute.
    *   The 'cron' block must be a TypeList with a maximum of 1 item.
    *   Include two required string sub-attributes: 'schedule' (cron expression) and 'timezone' (IANA timezone identifier).
    *   Ensure accessibility in Terraform state as 'cron.#', 'cron.0.schedule', and 'cron.0.timezone'.

*   Implement the following functions in `scaleway/helpers_jobs.go`:
    *   `expandJobDefinitionCron(i any) *JobDefinitionCron`
        *   Convert the raw Terraform state value for 'cron' into a `*JobDefinitionCron` struct.
        *   Return nil if the list is empty.
    *   `flattenJobDefinitionCron(cron *jobs.CronSchedule) []any`
        *   Convert the API's `CronSchedule` response into a format suitable for Terraform state.
        *   Return an empty list if cron is nil.

*   Define the `JobDefinitionCron` struct in `scaleway/helpers_jobs.go`:
    *   Include two string fields: `Schedule` and `Timezone`.
    *   Provide `ToCreateRequest()` and `ToUpdateRequest()` methods for API request conversion.
    *   Ensure `ToUpdateRequest()` returns an update request with nil fields if the struct is nil.

*   Ensure the following behaviors:
    *   On creation with a 'cron' block, reflect 'cron.#' as 1 in the state with correct values.
    *   On updating the 'cron' block, apply changes in-place and update the state accurately.
    *   On removal of the 'cron' block, delete the schedule from the API and set 'cron.#' to 0 in the state.

*   Ensure the cassette file `scaleway/testdata/job-definition-cron.cassette.yaml`:
    *   Records interactions for creating, reading, updating, and deleting cron schedules.
    *   Includes POST, multiple GETs, PATCH with cron fields, PATCH with null fields, and DELETE operations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.