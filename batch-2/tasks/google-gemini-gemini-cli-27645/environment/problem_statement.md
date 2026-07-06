## Description

When a newer GA flash model is made available via an experiment flag, the routing logic does not properly account for differences in authentication backend. Users authenticated through the standard API path should have flash model selections route to the published GA model, but users authenticated through other paths require a different model identifier that their backend understands (even though it resolves to the same underlying model on the server side). Currently, the logic uses a single hardcoded GA model string for all users, which is incorrect for non-API users.

Additionally, when a user with API-key authentication has access to both the GA model and the preview variant, explicitly selecting the preview model should keep the user on the preview model — it should not be silently promoted to the GA version. This override-respecting behavior is currently missing.

Finally, the usage stats display shows raw internal model identifiers to users. When a session runs on the GA flash model variant, the stats table shows an internal model string rather than the published name users recognize from the model selection menu.

## Expected Behavior

- When the GA flash experiment is active and the user is authenticated via the standard API key, routing should use the GA model string, and the preview flash model should remain selectable and route to the preview variant (not the GA version)
- When the GA flash experiment is active and the user is authenticated through other means, routing should use the alternative model identifier that corresponds to the same capability on that backend
- If a user explicitly selects the preview flash model and has preview access, that selection should be honored — the model should not be automatically promoted to the GA flash model
- The model stats display and session stats table should show user-facing model names rather than raw internal identifiers, so the published human-readable name appears instead of backend-specific strings
- The model description shown when "auto" mode is selected should reflect the GA flash model name when the GA experiment is active and the user has preview access

## Why This Matters

Users may be confused when the stats display shows an unfamiliar model identifier for a session. Additionally, incorrect model routing for non-API-key users could result in requests being sent to a model endpoint that doesn't match what was intended, potentially causing errors or unexpected behavior.
