## Description

The GitHub API library is missing support for several important webhook event types and lacks rich change detail for existing ones. Specifically:

- There is no way to parse a **membership event** (when a user is added to or removed from an organization team) — no model class exists for it.
- There is no way to parse a **team event** (when a team is created, deleted, or edited) — no model class exists for it.
- The existing **member event** parser has no way to expose *what changed* — for example, it cannot tell you what the previous and new permission levels were when a repository collaborator's access is edited.
- The existing **team_add event** parser lacks several important fields, including the team's node ID, description, privacy setting, and linked organization.

Additionally, the current representation of team privacy is fragile: if GitHub introduces a new privacy value or sends an unrecognized one, the library throws an error rather than gracefully falling back to a safe default. The same issue affects organization permission levels.

## Expected Behavior

- Parsing a membership event should expose the action, the affected member, the team (including its privacy and linked organization), and the organization.
- Parsing a team event should expose the action, the team details (name, description, privacy, organization), and — for edit actions — a structured changes object showing what was modified (description, name, privacy, or repository permissions).
- Parsing a member event should expose the action, the member, and a changes object showing what the permission level was before and after the update. When a member is newly added, the "before" permission should be absent (null).
- Parsing a team_add event should expose the full team details including node ID, description, privacy, and linked organization.
- Unknown team privacy values and unknown organization permission levels should be handled gracefully with a fallback sentinel value rather than causing an error.

## Why This Matters

Applications that react to GitHub webhook events for team and member management need to be able to inspect all of these event types. Without proper model support, developers cannot distinguish between different kinds of changes or access the change details they need.
