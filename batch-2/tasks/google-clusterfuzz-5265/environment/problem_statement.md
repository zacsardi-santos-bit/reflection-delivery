## Description

The ClusterFuzz project lacks a cron job that automatically aggregates daily fuzzer statistics and stores them in a cloud data warehouse for long-term analysis and reporting. There is currently no automated process to collect per-fuzzer metrics (such as number of testcases executed, generation counts, and fuzzing durations) from the previous day and write them into a time-partitioned table that supports efficient date-range queries.

## Expected Behavior

- A scheduled job should query the previous day's fuzzer stats and load them into a dedicated dataset and table.
- The dataset and table should be created automatically on first run; if they already exist, the job should continue without error.
- Unexpected infrastructure errors during setup should be surfaced (not silently ignored).
- The job should support an optional date override so operators can backfill stats for a specific date.
- Passing an invalid date format should cause the job to exit with an error immediately.
- The destination table should use day-based partitioning so historical data is organized and queryable by date.
- Uploaded records should include all relevant fuzzer stat fields: fuzzer name, date, testcases executed and generated (with durations), and total fuzzing duration.

## Why This Matters

Without this job, fuzzer performance data is ephemeral and cannot be analyzed over time. Persisting daily stats to a queryable, partitioned table enables teams to track trends, identify regressions, and report on fuzzer effectiveness across the fleet.
