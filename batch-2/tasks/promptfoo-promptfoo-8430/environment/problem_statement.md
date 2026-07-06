## Description

The cloud authentication login command doesn't properly support users who belong to teams spread across multiple organizations. When a user specifies a team name during login, the command relies on a remote API call to determine which team to use as the default, and there's no way to scope the login to a specific organization. Additionally, credential validation and credential saving are coupled into a single operation, making it impossible to resolve the correct team and organization context before the credentials are committed.

## Expected Behavior

- When specifying a team by name or identifier during login, the command should search across all accessible teams in all organizations to find a match, then automatically switch to the organization that team belongs to.
- When specifying an organization during login, team selection should be scoped to only teams within that organization.
- When both an organization and a team are specified, exact display-name matches should take priority over slug matches.
- When the user's default organization has no teams but they belong to teams in another organization, the login should automatically detect and switch to that organization and log which organization was selected.
- When a specified organization cannot be found in the user's accessible teams, the login should fail with a clear error message listing which organizations are accessible, and should not persist any credentials.
- When auto-selecting a default team (in non-interactive mode or when the user cancels interactive selection), the oldest team (by creation date) should be used instead of making an extra API call.
- Credential validation and credential saving should be separate steps, so team and organization resolution can happen between them.

## Why This Matters

Users who belong to multiple organizations or who automate logins via CI need reliable, predictable control over which organization and team context is established during authentication. Ambiguous team resolution and silent fallbacks to incorrect organizations can cause downstream failures when the wrong context is used for subsequent API calls.
