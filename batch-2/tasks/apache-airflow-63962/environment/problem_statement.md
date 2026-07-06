## Description

The Airflow scheduler supports DAGs that use partitioned timetables, where scheduled runs are organized around partition keys rather than standard datetime-based intervals. When a partitioned timetable DAG has no partition key set — meaning no run is currently due — the scheduler currently lacks a guard for this situation. Instead of skipping the DAG gracefully, it may proceed with processing, potentially leading to incorrect behavior or incomplete DAG run creation.

## Expected Behavior

- When the scheduler encounters a partitioned timetable DAG that has no partition key set, it should log a descriptive error and skip that DAG for the current scheduling cycle.
- When a partitioned timetable DAG does have a partition key set, the scheduler should proceed with DAG run creation as normal.

## Why This Matters

Without this guard, the scheduler may attempt to create DAG runs for partitioned timetable DAGs even when no partition is ready. Adding an explicit check with an informative error log makes it easier to diagnose scheduling issues and avoids potentially invalid runs being created.
