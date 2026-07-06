I'm working on improving the cloud login command to better handle multi-organization setups.

*   The CloudConfig class in src/globalConfig/cloud.ts must expose a validateApiToken(token, apiHost) method that calls the cloud API to validate the token and returns an object with user, organization, app, and an optional hasActiveLicense boolean field — without saving anything to the stored configuration.

*   The CloudConfig class must expose a saveValidatedApiToken(token, apiHost, user, app, hasActiveLicense?) method that synchronously persists the API key, API host, app URL, and optional license/sharing status to the stored configuration. It must accept the exact user and app objects returned by validateApiToken and the same hasActiveLicense value (e.g. false if the validation response returned false).

*   The getUserTeams function in src/util/cloud.ts must be updated to accept two optional parameters: apiHost and apiKey. When both are provided, it must make a direct HTTP GET request to `${apiHost}/api/v1/users/me/teams` with an Authorization: Bearer ${apiKey} header. When called without arguments it must fall back to the existing authenticated makeRequest helper.

*   The auth login command must call validateApiToken first to validate the API key, then perform team and organization resolution, and only then call saveValidatedApiToken to persist credentials. If organization/team resolution fails, saveValidatedApiToken and setUserEmail must NOT be called and process.exitCode must be set to 1.

*   When --team is specified without --org, the login command must call getUserTeams(apiHost, apiKey) with the provided host and API key to retrieve all accessible teams, then find a match by id first, then case-insensitive display name, then slug (in that priority order) across all organizations. Once a team is found, setCurrentOrganization must be called with that team's organizationId, cacheTeams must be called with all teams in that organization and the org ID, and setCurrentTeamId must be called with the team's id and organizationId. No warning should be logged when a match is found.

*   When --host is provided together with --team or --org, getUserTeams must be called with (customHost, apiKey) so that team resolution uses the specified host rather than the stored default.

*   When --org is specified, the login command must filter accessible teams to only those whose organizationId matches the given value. setCurrentOrganization must be called with the given org ID, cacheTeams must be called with the filtered team list and that org ID, and team selection must operate only within that filtered set.

*   When both --org and --team are specified and the team identifier matches a team's slug AND another team's display name (case-insensitive) within the same organization, the display name match must be selected over the slug match.

*   When no --org or --team flags are provided and the user's default organization (from the token validation response) has no accessible teams, but teams exist under a different organization, the login command must automatically resolve to the organization containing the oldest-created team. setCurrentOrganization must be called with the resolved org ID, cacheTeams must be called with the teams in that org, and logger.info must be called with a message containing both the literal string 'Organization:' and the resolved organization ID.

*   When --org is provided but no accessible team belongs to that organization, the login command must log an error message containing the exact substring: "Authentication failed: Organization '<orgId>' not found in your accessible teams. Available organizations: <ids>" where <ids> is a comma-separated deduplicated list that always includes the fallback organization ID from the token validation response (organization.id) followed by any unique organization IDs from accessible teams. process.exitCode must be set to 1 and saveValidatedApiToken and setUserEmail must not be called.

*   When multiple teams exist and the environment is non-interactive, or when the user cancels interactive team selection, the login command must automatically select the team with the earliest createdAt date (the oldest team) rather than making an API call to determine a default team. The getDefaultTeam API function must not be called in the login flow.


*   Interface details: Type: Method
Name: validateApiToken
Location: src/globalConfig/cloud.ts
Signature: validateApiToken(token: string, apiHost: string): Promise<{ user: CloudUser; organization: CloudOrganization; app: CloudApp; hasActiveLicense?: boolean }>
Description: Validates an API token against the cloud API without persisting any configuration. Returns the user, organization, app, and optional license status from the API response.

Type: Method
Name: saveValidatedApiToken
Location: src/globalConfig/cloud.ts
Signature: saveValidatedApiToken(token: string, apiHost: string, user: CloudUser, app: CloudApp, hasActiveLicense?: boolean): void
Description: Persists a previously validated API token and related configuration (API host, app URL, license/sharing status) to the stored configuration. This is a synchronous operation and does not call the cloud API.

Type: Function
Name: getUserTeams
Location: src/util/cloud.ts
Signature: getUserTeams(apiHost?: string, apiKey?: string): Promise<Array<{ id: string; name: string; slug: string; organizationId: string; createdAt: string; updatedAt: string }>>
Description: Fetches the list of teams accessible to the authenticated user. When both apiHost and apiKey are provided, makes a direct authenticated Bearer-token request to that host's API endpoint. When called without arguments, falls back to the stored configuration and makeRequest helper.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.