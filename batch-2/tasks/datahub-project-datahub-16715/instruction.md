I work at a company that uses single sign-on for DataHub, so I can't use a username and password or generate a personal access token on my own.

*   The init command must accept a new --sso flag that triggers browser-based SSO login mode.

*   The --sso flag must be mutually exclusive with --username/--password; if both are provided, the command must exit with a non-zero code and output must contain '--sso cannot be used with --username/--password'.

*   The --sso flag must be mutually exclusive with --token; if both are provided, the command must exit with a non-zero code and output must contain '--sso cannot be used with --token'.

*   When --sso is used with a localhost GMS host (e.g. port 8080), the init command must derive the frontend URL by substituting port 9002, default token duration to 'ONE_MONTH', and call browser_sso_login with the derived frontend URL, duration, and support=False.

*   When --sso is used with an acryl.io GMS URL ending in '/gms', the init command must derive the frontend URL by stripping the '/gms' suffix, default token duration to 'ONE_HOUR', and call browser_sso_login with the derived frontend URL, duration, and support=False.

*   The --sso flag must respect the --token-duration option; if provided, that value is passed as the duration argument to browser_sso_login.

*   On successful SSO login, the init command must print output containing 'Generated token' and write the token and host to the config file.

*   The init command must accept a new --support flag. If --support is used without --sso, the command must exit with a non-zero code and output must contain '--support requires --sso'.

*   When --support is combined with --sso, browser_sso_login must be called with support=True.

*   The browser_sso_login function must accept a frontend_url string, a duration string, an optional timeout_ms integer, and an optional support boolean (default False), and return a tuple of (token_name, access_token) where token_name contains 'cli token' and access_token is the value from the createAccessToken GraphQL response.

*   browser_sso_login must launch a Playwright chromium browser, extract 'actor' and 'PLAY_SESSION' cookies from the browser context after login, URL-decode the actor cookie value to obtain the actorUrn, set those cookies on a requests.Session, then POST to '{frontend_url}/api/v2/graphql' with a createAccessToken mutation passing input.actorUrn and input.duration.

*   If browser_sso_login times out waiting for login to complete, it must raise an exception with a message matching 'SSO login timed out' and close the browser.

*   If no actor cookie is found after login, browser_sso_login must raise an exception with a message matching 'no actor cookie found'.

*   If the createAccessToken GraphQL response contains errors, browser_sso_login must raise an exception with a message matching 'Failed to create access token'.

*   _warn_about_existing_cli_tokens must accept a session, a frontend_url string, and an actor_urn string; query listAccessTokens via GraphQL; count tokens whose names start with 'cli token'; print a warning containing '{count} existing CLI token(s)' and '{frontend_url}/settings/tokens'; and silently suppress any exceptions without re-raising them.


*   Interface details: Type: Module
Name: sso_cli
Location: metadata-ingestion/src/datahub/cli/sso_cli.py
Description: New module providing browser-based SSO login functionality for the DataHub CLI.

---

Type: Function
Name: _check_playwright_ready
Location: metadata-ingestion/src/datahub/cli/sso_cli.py
Signature: _check_playwright_ready() -> None
Description: Internal helper that verifies Playwright is importable. Must exist in the module so it can be patched in tests. Called before the browser is launched inside browser_sso_login. Raises an appropriate error if Playwright is not installed.

---

Type: Function
Name: browser_sso_login
Location: metadata-ingestion/src/datahub/cli/sso_cli.py
Signature: browser_sso_login(frontend_url: str, duration: str, timeout_ms: int = <default>, support: bool = False) -> tuple[str, str]
Description: Launches a Playwright chromium browser, navigates to the DataHub frontend login page, waits for SSO login to complete, extracts the 'actor' and 'PLAY_SESSION' cookies from the browser context, URL-decodes the actor cookie value to obtain the actorUrn, then sets all cookies on a requests.Session by calling session.cookies.set() for each cookie individually. Makes a listAccessTokens GraphQL call first (for the existing-token warning), then a createAccessToken GraphQL mutation at '{frontend_url}/api/v2/graphql' with input.actorUrn and input.duration. Returns a tuple of (token_name, access_token) where token_name contains 'cli token'. Raises an exception matching 'SSO login timed out' on timeout (and closes the browser before raising). Raises an exception matching 'no actor cookie found' when the actor cookie is absent. Raises an exception matching 'Failed to create access token' when the GraphQL response contains errors.

---

Type: Function
Name: _warn_about_existing_cli_tokens
Location: metadata-ingestion/src/datahub/cli/sso_cli.py
Signature: _warn_about_existing_cli_tokens(session, frontend_url: str, actor_urn: str) -> None
Description: Makes a listAccessTokens GraphQL call using the provided session. Counts tokens whose names start with the prefix 'cli token' (i.e., the string 'cli token' followed by a space and timestamp). Prints a warning containing '{count} existing CLI token(s)' and '{frontend_url}/settings/tokens'. All exceptions are silently suppressed — this function must never raise.

---

Type: CLI Flag
Name: --sso
Location: metadata-ingestion/src/datahub/cli/init_cli.py
Description: New flag on the `init` command that enables browser-based SSO login mode. Mutually exclusive with --username/--password (error message must contain '--sso cannot be used with --username/--password') and with --token (error message must contain '--sso cannot be used with --token'). When active, derives the frontend URL from the GMS host (localhost port 8080 → port 9002; acryl.io URLs ending in '/gms' → strip '/gms' suffix), then calls browser_sso_login with (frontend_url, effective_duration, support=False) or support=True if --support is also set. On success, writes the generated token and host to the config file and prints output containing 'Generated token'. Default duration: 'ONE_MONTH' for localhost, 'ONE_HOUR' for acryl.io/cloud URLs.

---

Type: CLI Flag
Name: --support
Location: metadata-ingestion/src/datahub/cli/init_cli.py
Description: New flag on the `init` command for support-access scenarios. Requires --sso to also be provided; without --sso, the command exits with a non-zero code and output contains '--support requires --sso'. When combined with --sso, passes support=True to browser_sso_login.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.