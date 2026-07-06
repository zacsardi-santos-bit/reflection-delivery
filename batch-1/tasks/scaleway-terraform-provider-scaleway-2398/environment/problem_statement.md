## Description

The Scaleway serverless job definition resource in the Terraform provider does not currently support configuring a recurring cron schedule. Users who want their serverless jobs to run automatically at set intervals — for example, every day at a specific time or on the first of each month — have no way to express this in their Terraform configuration today.

## Expected Behavior

- Users should be able to add a cron schedule block to a job definition resource, specifying a cron expression and an IANA timezone.
- Creating a job definition with a cron schedule should persist the schedule and timezone values and make them readable from Terraform state.
- Updating the cron schedule (changing the expression or timezone) on an existing job definition should apply the change without destroying and recreating the resource.
- Removing the cron schedule block from a job definition's configuration should delete the schedule from the underlying API and result in no cron entries in Terraform state.

## Why This Matters

Scheduled serverless jobs are a common use case — things like nightly cleanup tasks, periodic reports, or regular data sync jobs. Without this support, users cannot manage their cron-triggered job definitions through Terraform, forcing them to configure schedules manually outside of their infrastructure-as-code workflow.
