## Description

When building data pipelines, tasks often need context about their own previous runs — specifically, the last time they ran successfully. Right now there's no convenient way to retrieve that information from within a task. Developers have to manually query the database or write custom helper logic, which is cumbersome and error-prone.

We should add properties to task run objects that expose:

- The immediately preceding task run (regardless of whether it succeeded or failed)
- The most recently successful preceding task run
- The execution date of the most recently successful preceding task run
- The start date of the most recently successful preceding task run

## Expected Behavior

- Accessing the "previous task instance" property on any task run should return the immediately preceding run for that task, or nothing if it is the first run.
- Accessing the "previous successful task instance" property should skip over any failed runs and return the most recent run that succeeded, or nothing if there are no successful predecessors.
- Dedicated properties should be available for retrieving just the execution date or start date of the most recent successful run, without needing to navigate through the full object.
- All of these properties should work correctly regardless of whether the DAG uses a cron schedule, a fixed time interval, or no schedule at all — and whether or not catchup is enabled.

## Why This Matters

This is essential for writing incremental processing pipelines where each task run should pick up from where the last successful run left off. Without this, developers cannot easily determine the time window to process without querying the database themselves.
