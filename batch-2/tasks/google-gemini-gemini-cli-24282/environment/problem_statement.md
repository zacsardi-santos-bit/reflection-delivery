## Description

Plan mode — which prevents the AI from making changes until the user reviews and approves its plan — is currently gated behind an experimental feature flag. This means users must enable it in an "experimental" section of the configuration file, while related settings (like where plan files are saved) live in a separate part of the configuration. This split makes plan mode harder to discover and confusing to configure.

## Expected Behavior

- All plan mode configuration should be consolidated into the general settings section, allowing users to enable/disable the feature and set the plan directory in one place.
- The feature should no longer be marked as experimental; it should appear in the general settings category.
- The description for the plan mode toggle should be updated to better communicate its purpose (read-only safety during planning).
- When a user has plan mode set as their default session mode but the feature is disabled, the tool should gracefully fall back to standard mode rather than failing with an error.
- When a user explicitly requests plan mode via a command-line argument but the feature is disabled, the tool should still produce an appropriate error message.

## Why This Matters

Plan mode provides an important safety mechanism that is ready for general availability. Keeping it hidden in experimental settings reduces discoverability and creates a confusing split in configuration. Consolidating and promoting it improves the user experience and makes the feature easier to adopt.
