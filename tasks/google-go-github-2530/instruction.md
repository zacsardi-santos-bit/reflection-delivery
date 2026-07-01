Implement support for managing security manager teams within an organization using the go-github library. Add methods to list, add, and remove teams with the security manager role, ensuring consistency with existing library patterns.

*   Implement `ListSecurityManagerTeams` in `github/orgs_security_managers.go`.
    *   Accept parameters: `ctx context.Context`, `org string`.
    *   Make a GET request to `/orgs/{org}/security-managers`.
    *   Return a slice of `*Team`, an `*Response`, and an `error`.
    *   Parse the JSON array response body into a slice of `*Team` values.
    *   Return a URL parse error for invalid characters in `org`.

*   Implement `AddSecurityManagerTeam` in `github/orgs_security_managers.go`.
    *   Accept parameters: `ctx context.Context`, `org, team string`.
    *   Make a PUT request to `/orgs/{org}/security-managers/teams/{team}`.
    *   Return an `*Response` and an `error`.
    *   Return a URL parse error for invalid characters in `org` or `team`.

*   Implement `RemoveSecurityManagerTeam` in `github/orgs_security_managers.go`.
    *   Accept parameters: `ctx context.Context`, `org, team string`.
    *   Make a DELETE request to `/orgs/{org}/security-managers/teams/{team}`.
    *   Return an `*Response` and an `error`.
    *   Return a URL parse error for invalid characters in `org` or `team`.

*   Ensure all methods are part of the `OrganizationsService` type.
    *   Accessible via `client.Organizations.ListSecurityManagerTeams(...)`, `client.Organizations.AddSecurityManagerTeam(...)`, and `client.Organizations.RemoveSecurityManagerTeam(...)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.