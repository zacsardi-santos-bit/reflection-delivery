## Description

The model fallback system allows the application to switch to an alternative AI model when the primary model is unavailable (e.g., due to quota exhaustion). Currently, when a fallback is activated, the system records the new active model but does not track *which model originally failed* or maintain a queryable mapping of failed-to-replacement models. This means there is no way to look up what model is being used in place of another, and no mechanism for keeping those mappings consistent when chains of failures occur.

## Expected Behavior

- When activating fallback mode, it should be possible to specify which model failed, so the system can store a mapping from the failed model to its replacement.
- These mappings should be queryable: given a failed model name, the system should return which model is currently substituting for it.
- If a replacement model subsequently also fails and is itself replaced, all prior mappings that pointed to the intermediate model should be updated to point to the final replacement (chain flattening), and the underlying model routing configuration should reflect this.
- Runtime model overrides registered with the model configuration service should be clearable all at once via a dedicated method.
- Fallback override mappings should be automatically cleared when authentication is refreshed or when the session changes, since these are fresh starts.
- Fallback override mappings should be preserved when only the preferred model is changed, since an active fallback is still relevant.
- Activating fallback mode with the same model that is already active should not trigger an unnecessary reset of the model availability state.

## Why This Matters

Without tracking which models have failed and what they were replaced with, the system cannot accurately re-route requests at the routing layer, and multi-step fallback chains can leave the routing table in an inconsistent state. These improvements make fallback behavior more robust and predictable across the lifecycle of a session.
