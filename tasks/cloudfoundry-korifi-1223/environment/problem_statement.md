## Description

When a task is created via the API, the response body does not include any timestamp fields indicating when the task was created or last updated. The Cloud Foundry API specification requires these timestamps to be present in resource responses, so their absence makes our task responses non-compliant and breaks any client that relies on this information for ordering, display, or auditing.

## Expected Behavior

- When a task is created, the internal task record should capture the task's actual creation time from the underlying resource.
- The API response for a newly created task should include both a creation timestamp and a last-updated timestamp, formatted in standard ISO 8601 UTC notation without fractional seconds.
- Because tasks are immutable after creation, the creation timestamp and the last-updated timestamp should be the same value.

## Why This Matters

Clients consuming the API expect timestamp fields on all resource types. Without them, clients cannot determine task age, sort tasks by creation time, or display meaningful "created at" information in UI surfaces. This omission causes compatibility issues with standard CF API clients.
