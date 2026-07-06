## Description

The flash model routing logic has two correctness problems when a newer generally-available flash model is enabled, and the statistics display shows an incorrect model name.

**Problem 1 — Preview model selection not respected**

When the GA flash model is enabled, the routing code currently promotes _all_ flash model selections to the GA version — including cases where the user has explicitly chosen the preview flash variant. A user who has access to the preview flash model and manually selects it should continue to use that preview model. Their selection must not be silently overridden by the GA promotion logic.

**Problem 2 — Auth-type-aware model assignment missing**

The GA flash model is only appropriate for users who authenticate directly via the Gemini API. Users on other authentication methods (such as those who log in through Google) should receive a different flash model suited to their access level. Currently, the same model is assigned regardless of which authentication path is used, which is incorrect.

**Problem 3 — Wrong model name shown in statistics**

The statistics display components show an internal model identifier for one of the flash model variants rather than the proper display name. This makes the session stats confusing for users.

## Expected Behavior

- When a user explicitly selects the preview flash model and has access to it, that model should be used — not replaced by the GA flash model.
- When the GA flash feature is active, direct-API users should receive the GA flash model; users on other auth paths should receive the appropriate model for their tier.
- The auto-model description text should reflect the correct flash model name when the GA flash feature is active.
- Statistics views should display the proper display name for all model variants, including the internal flash alias.

## Why This Matters

Incorrect model routing means users may be silently sent to a model they did not select or one they do not have appropriate access to. The display name issue makes it harder to interpret usage statistics after a session.
