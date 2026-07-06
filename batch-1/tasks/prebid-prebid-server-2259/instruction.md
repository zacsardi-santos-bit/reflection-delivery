Implement account-level validation for the setuid endpoint to ensure that requests are only processed for active publisher accounts. If an account is disabled, respond with an error message and a 400 Bad Request status.

*   Update the `NewSetUIDEndpoint` function signature:
    *   Accept a pointer to the full server `Configuration` struct as the first parameter.
    *   Add an `accountsFetcher` parameter of type `stored_requests.AccountFetcher` between the `pbsanalytics` and `metricsEngine` parameters.
*   Modify the `/setuid` handler:
    *   Read the account ID from the 'account' query parameter.
    *   If the 'account' query parameter is absent or empty, use the `metrics.PublisherUnknown` sentinel value for fetching.
    *   Use the account service's `GetAccount` function to validate the account.
    *   If `GetAccount` returns errors (e.g., account is disabled), respond with HTTP 400 Bad Request and include the error message in the response body.
    *   Ensure the error message for a disabled account is: 'account is disabled, please reach out to the prebid server host'.
    *   If a valid, non-disabled account ID is provided, continue processing normally and return HTTP 200 OK with the cookie set.
*   Update internal references within the handler:
    *   Use `cfg.HostCookie` for cookie operations, as `cfg` is now of type `*config.Configuration`.
*   Update the call site in `router/router.go`:
    *   Pass the full `*config.Configuration` pointer and the `accountsFetcher` instance instead of `cfg.HostCookie`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.