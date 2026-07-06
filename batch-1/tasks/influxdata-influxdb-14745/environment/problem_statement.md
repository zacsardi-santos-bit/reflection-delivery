## Description

When users configure monitoring checks in InfluxDB, they define a source query and a schedule that together determine how data will be evaluated. However, there is currently no way to retrieve the actual, complete query that the system will execute when the check runs. The check's configuration is stored separately from the effective scheduled query, and nothing exposes what the final executable script looks like — including how the time window and scheduling parameters are incorporated.

## Expected Behavior

- Users should be able to retrieve the effective, ready-to-execute query for any check by its ID.
- The returned query should reflect the check's scheduling configuration — specifically, the time range should be adjusted to match the check's run interval, and task scheduling options should be included.
- The response should be a JSON document containing the generated query as a string.

## Why This Matters

Debugging monitoring checks is difficult when the user cannot see exactly what query the system is running. Having a way to fetch the effective query for a given check helps users verify that their check configuration will behave as expected, and makes it easier to diagnose issues when checks fire unexpectedly or fail to alert.
