I need to implement a logout capability for pnpm.

*   The logout function must accept a context object and an opts object. The opts object must include configDir (string), dir (string), authConfig (a map of registry auth settings), and an optional registry URL (defaulting to https://registry.npmjs.org/ when not provided).

*   When authConfig contains no token for the target registry, logout must throw an error with code 'ERR_PNPM_NOT_LOGGED_IN' and a message of the form "Not logged in to <registry>, so can't log out".

*   On successful logout, the function must call the registry's token revocation endpoint via an HTTP DELETE request to <registry>-/user/token/<encodedToken>, with an Authorization header of 'Bearer <token>'. The token must be URL-encoded (e.g. slashes, plus signs, and equals signs must be percent-encoded).

*   The registry URL must be normalized to always end with a trailing slash before constructing URLs or returning result messages.

*   The function must read auth settings from the file at path.join(configDir, 'auth.ini'), remove the token key (e.g. '//registry.npmjs.org/:_authToken'), and write the remaining settings back to that same file.

*   On success, the function must return the string 'Logged out of <registry>' where <registry> is the normalized registry URL.

*   When the registry returns a non-OK HTTP response (any status that is not ok), the function must call context.globalInfo with the message 'Registry returned HTTP <status> when revoking token', but must still attempt to clean up the token locally and return a success result.

*   When the fetch call throws a network error, the function must call context.globalInfo with the message 'Could not reach the registry to revoke the token', but must still attempt to clean up the token locally and return a success result.

*   When the token is present in authConfig but not found in auth.ini (including when auth.ini does not exist, i.e. readIniFile throws an error with code 'ENOENT'), and the registry call succeeds, the function must: NOT call writeIniFile, call context.globalWarn with a message containing 'was not found in <path to auth.ini>', call context.globalWarn with a message containing 'The token was revoked on the registry but must be removed manually', and still return the success string.

*   When the token is NOT in auth.ini AND the registry call fails (non-ok response), the function must throw an error with code 'ERR_PNPM_LOGOUT_FAILED' and a message containing both 'Failed to log out of <registry>' and 'may still need to be revoked on the registry'.

*   When readIniFile throws an error with a code other than 'ENOENT', the error must be propagated as-is (preserving its original code).

*   Registries with URL paths (e.g. https://example.com/npm/) must be supported: the token revocation URL is constructed as <registry>-/user/token/<encodedToken>, and the corresponding auth.ini key is '//example.com/npm/:_authToken'.

*   The LogoutContext and LogoutFetchResponse types must be exported from auth/commands/src/logout.ts so consumers can import and implement them.


*   Interface details: Type: Function
Name: logout
Location: auth/commands/src/logout.ts
Signature: logout({ context, opts }: { context: LogoutContext, opts: LogoutOpts }): Promise<string>
Description: Performs a logout operation: revokes the auth token on the registry and removes it from the local auth.ini file. Returns a success message string like "Logged out of https://registry.npmjs.org/". Throws structured errors for not-logged-in and logout-failed conditions.

Type: Interface
Name: LogoutFetchResponse
Location: auth/commands/src/logout.ts
Description: Represents the response object returned by the fetch dependency. Must have: ok: boolean, status: number, text: () => Promise<string>.

Type: Interface
Name: LogoutContext
Location: auth/commands/src/logout.ts
Description: Dependency injection context for the logout function. Must have:
  - fetch(url: string, init?: RequestInit): Promise<LogoutFetchResponse>
  - globalInfo(message: string): void
  - globalWarn(message: string): void
  - readIniFile(path: string): Promise<Record<string, unknown>>
  - writeIniFile(path: string, settings: Record<string, unknown>): Promise<void>

Type: Interface
Name: LogoutOpts
Location: auth/commands/src/logout.ts
Description: Options passed to the logout function. Must have:
  - configDir: string  (path to the config directory containing auth.ini)
  - dir: string  (working directory)
  - authConfig: Record<string, string>  (current auth configuration, e.g. { '//registry.npmjs.org/:_authToken': 'my-token' })
  - registry?: string  (optional registry URL; defaults to 'https://registry.npmjs.org/')


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.