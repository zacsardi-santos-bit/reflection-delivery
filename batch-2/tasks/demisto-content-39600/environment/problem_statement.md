## Description

We need a new integration for the Anomali Security Analytics Alerts platform. Currently there is no way for analysts to interact with Anomali's search and alert management capabilities from within the SOAR platform. This integration should allow analysts to submit search queries, track their progress, retrieve structured results, and manage alert records.

## Expected Behavior

- Analysts can launch a new search job by providing a query, a data source, and a time range. The system should return the job identifier so the job can be tracked.
- Analysts can check on a running search job by its identifier. If the job is still in progress, the response should clearly indicate it is still running. If the job has completed with structured data (field names and records), the results should be returned as a table with the field names as headers and each record as a row of key-value pairs. If the job completed but with no structured fields, the raw result data should be returned. If the job identifier is invalid or the server returns an error, the response should clearly communicate the error, include the job identifier that was looked up, and prompt the analyst to verify it.
- Analysts can update an existing alert by its unique identifier, providing a new status, a comment, or both. If neither a status nor a comment is provided, the system must reject the request with a clear error message indicating that at least one of these fields is required.

## Why This Matters

Without this integration, analysts have no automated way to run queries against Anomali's analytics engine or update alert states from within the SOAR workflow. This closes the gap and enables end-to-end alert management and investigation directly within the platform.
