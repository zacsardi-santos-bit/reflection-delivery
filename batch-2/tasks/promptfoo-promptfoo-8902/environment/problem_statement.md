## Description

Our telemetry system currently captures authentication state (email address, login status, and auth method) once at initialization and caches it for the lifetime of the process. This causes two problems:

1. Analytics events can be tagged with stale identity information if a user's auth state changes during a session.
2. Dashboard filters based on person properties (e.g. filtering out CI traffic, segmenting by auth method) are unreliable because person properties are not mirrored onto individual event payloads.

## Expected Behavior

- There should be a single consolidated function that snapshots the current user's authentication info in one config read, returning the email address (or null), whether the user is logged into cloud (via API key from config or environment), and the authentication method (one of three states: API key authentication, email-only authentication, or no authentication).
- Every telemetry event sent should include a fresh snapshot of these person properties, embedded directly in the event payload, so analytics dashboards can reliably filter on them.
- The fresh auth snapshot should be fetched at event-send time, not cached from startup.
- The email reported to the analytics reporting endpoint should also reflect the current, live auth state rather than a cached startup value.

## Why This Matters

Analytics dashboards rely on person properties being present on events to enable filtering (e.g. "exclude CI runs", "segment by auth method"). Without mirroring these properties on each event, those filters may silently fail or show incomplete data. Consolidating the auth snapshot into a single function also reduces redundant config reads.
