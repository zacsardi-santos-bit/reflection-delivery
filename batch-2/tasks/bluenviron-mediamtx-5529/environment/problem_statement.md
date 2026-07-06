## Description

The always-available streaming feature in the server configuration has three issues that should be fixed together.

**Implicit default track**: When a path is created without explicitly specifying any always-available tracks, the system silently adds a default video track to the configuration. Users who have not configured any always-available tracks should see an empty list, not a pre-populated one they did not ask for.

**Missing mutual-exclusion check**: The configuration currently allows a user to set both an always-available file and always-available tracks for the same path at the same time. These two options are mutually exclusive — they both define the source for the offline segment, so specifying both simultaneously is contradictory. The configuration validator should reject this combination with a clear error.

**Error message formatting**: When an invalid file (not a valid MP4) is specified as the always-available file, the error message wraps the diagnostic byte values in unnecessary single quotes, which is inconsistent with the rest of the codebase. These extra quote characters should be removed so the byte values are reported in plain bracket notation.

## Expected Behavior

- A newly configured path with no explicit always-available tracks has an empty tracks list.
- Configuring both an always-available file and always-available tracks on the same path is rejected with a clear error stating the two options cannot be used together.
- When an invalid (non-MP4) always-available file is specified, the error reports the diagnostic bytes without extra quoting around the bracket notation.

## Why This Matters

Users who configure the always-available feature need predictable defaults and clear validation. The implicit default track can produce unexpected behavior, the missing mutual-exclusion check silently accepts an ambiguous configuration, and the inconsistent error format makes diagnostics harder to read.
