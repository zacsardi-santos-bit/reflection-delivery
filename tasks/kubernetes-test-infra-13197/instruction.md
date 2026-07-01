Implement access control for the prow deck's job rerun endpoint to ensure only authorized users can trigger new CI jobs. Distinguish between read and write requests, and manage job creation based on user authorization and configuration settings.

*   Implement the `GetLogin` method in `prow/githuboauth/githuboauth.go`:
    *   Accept an HTTP request and a `GitHubClientGetter`.
    *   Extract the OAuth access token from the session named 'access-token-session' using the key 'access-token'.
    *   Call `GetGitHubClient` with the extracted token to obtain a `GitHubClientWrapper`.
    *   Use `GetUser` with an empty string on the wrapper to retrieve the authenticated GitHub username and return it along with any error.

*   Implement the `handleRerun` function in `prow/cmd/deck/main.go`:
    *   Accept parameters: a `ProwJob` client, a boolean for job creation enablement, a function returning `RerunAuthConfig`, a `githuboauth.Agent`, and a `GitHubClientGetter`.
    *   On GET requests, return HTTP 200 OK with the prowjob spec in the response body.
    *   On POST requests:
        *   If `createProwJob` is false, return HTTP 405 Method Not Allowed.
        *   If `createProwJob` is true:
            *   Retrieve the authenticated user's GitHub login using `goa.GetLogin`.
            *   Check authorization against the `RerunAuthConfig`.
            *   If the user is authorized or `AllowAnyone` is true, create a new ProwJob and return HTTP 302 Found.
            *   If the user is not authorized and `AllowAnyone` is false, do not create a ProwJob but return HTTP 302 Found.

*   Update the `RerunAuthConfig` struct in the `prow/config/` package:
    *   Include an `AllowAnyone` boolean field.
    *   Include an `AuthorizedUsers` string slice field.

*   Ensure the `GitHubClientGetter` interface in `prow/githuboauth/githuboauth.go`:
    *   Includes a `GetGitHubClient` method that returns a `GitHubClientWrapper`.

*   Ensure the `GitHubClientWrapper` interface in `prow/githuboauth/githuboauth.go`:
    *   Includes a `GetUser` method that returns a pointer to `github.User` and an error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.