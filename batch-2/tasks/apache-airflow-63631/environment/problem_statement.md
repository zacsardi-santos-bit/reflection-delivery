## Description

The DAG calendar view currently does not work correctly for partitioned DAGs (as introduced in AIP-76). These DAGs assign a "partition date" to each run rather than a "logical date," leaving the logical date field empty. As a result, the calendar endpoint either skips these runs entirely or shows incorrect data when displaying the run history for a partitioned DAG.

## Expected Behavior

- When viewing the calendar for a partitioned DAG with no date filter applied, all runs should be displayed. Each run's "partition date" should be used as the display date in the calendar entries. Any runs that only have a logical date (and no partition date) should still appear using their logical date.
- The calendar endpoint should support filtering by partition date range (start and end bounds), so users can narrow the calendar view to a specific time window based on partition date.
- When a partition date filter is active, only runs matching that partition date range should be returned. Runs that only have a logical date should be excluded.
- When a logical date filter is active (and no partition date filter), only runs with a matching logical date should be returned. Runs that only have a partition date should be excluded.
- Both the daily and hourly calendar granularities should work correctly for partitioned DAGs.

## Why This Matters

Users who have migrated their DAGs to the new partitioned scheduling model have no way to view their run history in the calendar interface. The calendar view is an important tool for identifying scheduling gaps and run failures at a glance. Without this fix, the calendar is essentially unusable for a growing category of DAGs.
