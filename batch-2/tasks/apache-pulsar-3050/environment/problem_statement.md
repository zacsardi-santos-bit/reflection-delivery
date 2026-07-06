## Description

Pulsar Functions currently expose a stats endpoint and admin API, but the statistics data model has a bug: when a function instance has never processed any messages, the average latency and last invocation timestamp fields report as zero instead of being absent. This makes it impossible for operators to distinguish an idle function (one that hasn't received any messages yet) from a function with near-zero latency.

Additionally, the admin command-line tool does not have a stats subcommand for functions, so there is no way to inspect function statistics from the shell without writing custom code.

## Expected Behavior

- When a function has not yet processed any messages, the average process latency and last invocation timestamp fields should be absent rather than defaulting to zero.
- When a function has processed messages, those fields should report meaningful, non-zero values reflecting actual processing activity.
- Both aggregate function statistics and per-instance statistics should be retrievable through the admin API, and the results from both access paths should be consistent with each other.
- The aggregate statistics should correctly average latency across only the instances that have actual processing data, rather than including instances with no data in the average.
- The admin command-line tool should support retrieving function statistics by tenant, namespace, and function name, returning parseable output.

## Why This Matters

Without this fix, monitoring dashboards and health checks cannot reliably determine whether a function is idle or fast — both states look the same. The absence of a CLI stats command also makes quick operational checks impractical.
