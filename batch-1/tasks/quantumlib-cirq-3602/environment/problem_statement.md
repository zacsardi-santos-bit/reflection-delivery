## Description

The IonQ service integration supports creating, retrieving, and canceling individual quantum computing jobs, but there is currently no way to fetch a list of jobs. This is a significant gap when users need to audit their job history, find jobs matching a particular status, or simply see all jobs associated with their account.

## Expected Behavior

- Users should be able to retrieve a list of jobs from the IonQ API through both the low-level client and the high-level service interface.
- The listing operation should support filtering by job status (e.g., only completed or canceled jobs).
- Users should be able to specify the maximum total number of jobs to return.
- Users should be able to control the batch size used for each individual API call, to avoid requesting too many results at once.
- Pagination should be handled transparently: if the API indicates there are more results, subsequent requests should be made automatically and the results merged together.
- The high-level service interface should return proper job objects (not raw API response dicts), consistent with how other job-fetching methods behave.
- Error handling should follow the same patterns as other API calls: unauthorized requests should raise an appropriate error, non-retriable failures should surface the HTTP status code, and transient server errors should be retried automatically.

## Why This Matters

Without the ability to list jobs, users must track job identifiers externally and query each one individually. A bulk listing capability makes the service more practical for real workloads and aligns it with what users would expect from a complete job management API.
