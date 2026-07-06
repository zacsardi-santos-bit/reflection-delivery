# Pro Model Access Gate & Flash Lite Preview Model Support

## Description

Users who are on free or restricted tiers are currently able to see and select Pro-tier models in the model selection dialog, even when they don't have access to those models. Worse, if a user has automatic model selection enabled, the system quietly uses a Pro model they can't access rather than falling back to an appropriate alternative.

This is confusing and potentially broken behavior for users who lack Pro model access.

## Expected Behavior

- When a user has no access to Pro models, the model selection dialog should skip the initial "main" view and immediately display the model list showing only non-Pro options (flash and flash-lite models).
- Pro models should be filtered out entirely from the displayed list for these users.
- The "Auto" option should not appear for users without Pro model access.
- If a user without Pro access has automatic model selection configured, the system should automatically switch to an appropriate flash model.
- A new lightweight flash preview model should be available as an option, but only visible to free-tier users (not to Pro-access users). It should appear after the standard flash preview model in the list and before the non-preview flash options.
- When a user without Pro access is viewing the model list and presses Escape, the dialog should close entirely rather than navigating to a Pro model selection view.

## Why This Matters

Without this gating, free-tier users are shown a confusing list of models they cannot use, and the system may silently attempt to use a Pro model on their behalf. Properly filtering the model list and auto-downgrading the active model prevents failed requests and improves the experience for users on restricted access tiers.
