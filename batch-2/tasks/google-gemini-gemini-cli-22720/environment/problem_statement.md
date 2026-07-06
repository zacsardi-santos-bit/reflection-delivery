## Description

Users who don't have premium model access are currently not handled correctly in two places. First, when the CLI is configured to use an auto-selected premium model and the user turns out to not have premium access, the model is never downgraded — the user ends up trying to use a model they can't actually access. Second, the model selection dialog shows premium model options (including the "Auto" mode) to users who don't have access to them, which is confusing and unhelpful.

## Expected Behavior

- When authentication completes and the system detects that a user lacks premium access (via an experiment flag), and the user was previously on an auto-mode premium model, the active model should be automatically switched to an appropriate flash model.
- The model selection dialog should detect upfront whether the user has premium access. If they do not, the dialog should open directly in the manual model selection view instead of the main view, showing only the models available to them (no premium or "Auto" options), in a defined order.
- Users without premium access who see the manual selection dialog should be able to dismiss it by pressing Escape.
- A new Flash Lite Preview model variant should be introduced and should appear in the model list exclusively for free-tier users — premium-tier users should not see this model in their selection list.

## Why This Matters

Non-premium users currently have a broken experience: they're shown model options they cannot use, and if they had previously selected an auto mode, they may silently be assigned an inaccessible model. This change ensures the interface and the active model selection always match what the user actually has access to, based on their tier.
