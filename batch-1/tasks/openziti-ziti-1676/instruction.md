Update the integration tests and SDK usage to be compatible with the new version of the networking SDK. Ensure that all client constructors, session token accesses, authentication state event callbacks, and error handling are updated according to the new SDK requirements.

*   Update client constructors:
    *   Modify all calls to `NewManagementApiClient` and `NewClientApiClient` to include a third parameter (`nil`) in addition to the URL and CA pool.
    *   Ensure these changes are applied throughout the tests package.

*   Update session token access:
    *   Replace all direct field accesses of `apiSession.Token` with method calls to `apiSession.GetToken()`.

*   Update authentication state event callbacks:
    *   Register callbacks for `AddAuthenticationStateFullListener`, `AddAuthenticationStatePartialListener`, and `AddAuthenticationStateUnauthenticatedListener` using the new session type `*edge_apis.ApiSession`.
    *   Ensure channel types holding delivered values use `*edge_apis.ApiSession`.

*   Update internal session field access:
    *   Access the current API session on the controller client using `implZtx.CtrlClt.ApiSession.Load().ID` instead of the old field name `CurrentAPISessionDetail`.

*   Enhance error handling:
    *   Wrap errors returned by certificate-based authentication calls with `rest_util.WrapErr` to surface HTTP error details.

*   Improve test reliability:
    *   In `StartServerFor` function in `tests/context.go`, add a 100-millisecond wait and retry once if removing the test database file fails with an OS error (other than 'does not exist') before propagating the error.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.