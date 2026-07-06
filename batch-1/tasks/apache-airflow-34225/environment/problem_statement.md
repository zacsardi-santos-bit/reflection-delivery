## Description

When running serverless big data jobs through Airflow, users have no easy way to navigate to the logs or monitoring dashboards for those jobs. After submitting a job, there are currently no clickable links in the task interface that take a user directly to the relevant AWS console pages — users must manually look up job IDs, navigate to the correct AWS service pages, and construct the appropriate URLs themselves.

## Expected Behavior

After a serverless job starts, the task details view should automatically show links based on the job's logging configuration:

- If S3 logging is configured, a link should appear that takes the user directly to the relevant S3 bucket location filtered to that specific job's log output.
- If CloudWatch logging is enabled, a link should appear pointing directly to the relevant CloudWatch log stream, filtered to the specific job run.
- If the operator is configured to expose live application UIs, one-time dashboard links for the Spark or Tez UI should be generated and shown. For Spark jobs, an additional link to the Spark driver stdout log should also be made available.
- These application UI links should be opt-in, defaulting to off, because they are ephemeral and accessible to any user with access to the DAG.

## Why This Matters

Without these links, debugging and monitoring a running or completed serverless job requires significant manual effort navigating AWS consoles. These automatic, context-aware links let any authorized user jump directly to the right place — whether that is S3, CloudWatch, or a live Spark UI — without needing to know the job's internal identifiers or navigate the AWS console hierarchy manually.
