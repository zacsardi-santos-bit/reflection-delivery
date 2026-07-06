Implement a method to list jobs from the IonQ API in both the low-level client and high-level service interfaces. Ensure the method supports filtering by job status, limits the total number of results, and manages pagination automatically.

*   Update `_IonQClient.list_jobs` in `cirq/ionq/ionq_client.py`:
    *   Make a GET request to `/v0.1/jobs` with headers for authorization and content-type.
    *   Use `json={'limit': batch_size}` and `params={}` by default.
    *   Include `params={'status': <status>}` if a status is provided.
    *   Handle pagination by checking for a 'next' key in the response and making additional requests with `params={'next': <token>}`.
    *   Return a list of job dictionaries, truncated to the specified `limit`.
    *   Raise `IonQException` with 'Not authorized' for HTTP 401 errors.
    *   Raise `IonQException` with 'Status: <code>' for non-retriable HTTP errors.
    *   Retry requests at least once for HTTP 503 errors.

*   Update `Service.list_jobs` in `cirq/ionq/service.py`:
    *   Accept parameters `status`, `limit`, and `batch_size` and forward them to `_IonQClient.list_jobs`.
    *   Return a sequence of `Job` objects, where each `Job` object’s `job_id()` method returns the 'id' field from the job dictionary.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.