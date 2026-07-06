Implement cloud browser support in the browser-use library to enable users to connect to cloud-hosted browser sessions. Ensure authentication with a cloud browser service using environment variables or a configuration file, manage session lifecycle, and handle errors appropriately.

*   Implement the `CloudBrowserClient` class in `browser_use/browser/cloud.py`:
    *   Define `create_browser()` to make a POST request to the cloud API, returning an object with fields: `id`, `status`, `cdpUrl`, `liveUrl`, `timeoutAt`, `startedAt`, and `finishedAt`.
    *   Include the API key in the `X-Browser-Use-API-Key` HTTP header, prioritizing the `BROWSER_USE_API_KEY` environment variable, then the `CloudAuthConfig` file.
    *   Raise `CloudBrowserAuthError` with 'BROWSER_USE_API_KEY environment variable' if no API key is available.
    *   Raise `CloudBrowserAuthError` with 'Authentication failed' on HTTP 401 responses.
    *   Define `stop_browser(session_id: str = None)` to make a PATCH request with `{'action': 'stop'}` and return an object with `id`, `status`, and `finishedAt`.
    *   Raise `CloudBrowserError` with 'not found' on HTTP 404 responses.
    *   Maintain a `current_session_id` attribute for session management.

*   Update `BrowserProfile` in `browser_use/browser/profile.py`:
    *   Add a `use_cloud` parameter (boolean) that, when `True`, makes `use_cloud` property return `True` and `is_local` property return `False`.

*   Update `BrowserSession` in `browser_use/browser/session.py`:
    *   Add a `cloud_browser` property that returns the value of `browser_profile.use_cloud`.

*   Implement `get_cloud_browser_cdp_url` async function in `browser_use/browser/cloud.py`:
    *   Instantiate `CloudBrowserClient`, call `create_browser()`, and return the `cdpUrl` string.

*   Implement `stop_cloud_browser_session` async function in `browser_use/browser/cloud.py`:
    *   Accept a session ID string, call `stop_browser(session_id)` on a `CloudBrowserClient`, and return the result object.

*   Implement `CloudAuthConfig` in `browser_use/sync/auth.py`:
    *   Store `api_token`, `user_id`, and `authorized_at`.
    *   Provide `save_to_file()` method and `load_from_file()` class method.
    *   Determine config file location using `BROWSER_USE_CONFIG_DIR` environment variable.

*   Ensure `CloudBrowserAuthError` does not trigger fallback to local mode; `use_cloud` remains `True` and `is_local` remains `False` when authentication fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.