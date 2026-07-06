Implement support for additional GitHub webhook event types and enhance existing parsers to include detailed change information. Ensure graceful handling of unrecognized enum values.

*   Add new inner classes to `GHEventPayload.java`:
    *   `GHEventPayload.Membership`:
        *   Expose `getAction()`, `getOrganization()`, `getMember()`, and `getTeam()`.
        *   Ensure the team's organization is correctly linked during late binding.
    *   `GHEventPayload.Team`:
        *   Expose `getAction()`, `getOrganization()`, `getTeam()`, `getRepository()`, and `getChanges()`.
        *   Ensure `getChanges()` returns a `GHTeamChanges` object, nullable unless action is "edited".
        *   Ensure the team's organization is correctly linked during late binding.
    *   `GHEventPayload.TeamAdd`:
        *   Expose `getTeam()`, `getOrganization()`, and `getRepository()`.
        *   Ensure `getAction()` returns null for this event type.
        *   Ensure the team's organization is correctly linked during late binding.
    *   `GHEventPayload.Member`:
        *   Expose `getAction()`, `getMember()`, `getOrganization()`, `getRepository()`, and `getChanges()`.

*   Create new classes:
    *   `GHMemberChanges` in `GHMemberChanges.java`:
        *   Expose `getPermission()` returning a `GHMemberChanges.FromToPermission` object.
    *   `GHMemberChanges.FromToPermission`:
        *   Expose `getFrom()` and `getTo()`, with `getFrom()` returning null if the JSON "from" field is absent.
    *   `GHTeamChanges` in `GHTeamChanges.java`:
        *   Expose `getDescription()`, `getName()`, `getPrivacy()`, and `getRepository()`.
    *   `GHTeamChanges.FromString`:
        *   Expose `getFrom()`.
    *   `GHTeamChanges.FromPrivacy`:
        *   Expose `getFrom()`.
    *   `GHTeamChanges.FromRepository`:
        *   Expose `getPermissions()`.
    *   `GHTeamChanges.FromRepositoryPermissions`:
        *   Expose `hadPullAccess()`, `hadPushAccess()`, and `hadAdminAccess()`.

*   Update enums:
    *   `GHTeam.Privacy` in `GHTeam.java`:
        *   Include `SECRET`, `CLOSED`, and `UNKNOWN`.
        *   Ensure `GHTeam.getPrivacy()` returns `UNKNOWN` for unrecognized values.
    *   `GHOrganization.Permission` in `GHOrganization.java`:
        *   Include `ADMIN`, `TRIAGE`, `WRITE`, `MAINTAIN`, `PULL`, and `UNKNOWN`.
        *   Ensure `UNKNOWN` is used as a fallback for unrecognized permission strings.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.