## Description

The OSV vulnerability database writes records to cloud storage as part of its normal update pipeline. However, this write step can fail for a variety of reasons — transient storage errors, concurrent modification conflicts, or records simply going missing. Currently there is no automated recovery pathway: when a write fails, the failure is logged (or silently swallowed) and the cloud storage entry remains stale or missing indefinitely.

We need a dedicated recovery worker that can consume a queue of failed-task messages and handle each failure scenario appropriately:

- **Failed write retry**: A previous write attempt failed entirely (e.g. network error). Re-upload the vulnerability from the queued data, but skip the write if the storage copy is already newer than what was queued.
- **Missing record**: A vulnerability is in the database but missing from cloud storage. Re-generate the storage entry from the live database record.
- **Field sync mismatch**: A field update (such as aliases or upstream relationships) conflicted with a concurrent write. Re-query the current database state for that field and apply it to the storage copy.
- **Unknown task**: Any unrecognized task type should be logged as an error and acknowledged so it doesn't block the queue.

Additionally, other parts of the system (alias computation, upstream computation, the API server) currently have TODO comments where they intend to publish failure messages when these situations are detected. A shared utility is needed so callers can publish a failure message to the recovery queue without duplicating the Pub/Sub boilerplate in each location.

## Expected Behavior

- A recovery worker module with distinct handlers for each failure type (retry, missing, gen-mismatch, generic)
- Each handler accepts a Pub/Sub message and returns a value indicating whether the message was successfully handled
- A shared utility function that publishes a byte payload plus optional metadata attributes to the recovery queue topic
- The shared utility raises an error with a clear log message if the cloud project is not configured

## Why This Matters

Without this, cloud storage can permanently diverge from the database when writes fail, causing the API to serve stale or missing vulnerability data. The recovery worker closes this gap by automatically correcting inconsistencies when they are detected.
