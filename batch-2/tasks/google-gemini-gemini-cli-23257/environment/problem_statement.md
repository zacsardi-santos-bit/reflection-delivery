## Description

When a user grants permanent ("always allow") permission for a tool while in a particular operating mode, the resulting policy rule is stored without any mode context. This means the approval implicitly applies to **all** modes, including the plan mode — a restricted, read-only research environment where unintended tool execution could violate safety guarantees.

The system should be smarter about scoping these persistent approvals: a permanent approval granted in the standard interactive mode should apply to that mode and any more permissive mode, but should **not** silently extend to more restricted modes like plan mode. Conversely, an approval explicitly granted while already in plan mode should be treated as a deliberate global trust decision and cover all modes.

## Expected Behavior

- When a permanent approval is granted, the saved policy rule should explicitly record which modes it applies to, based on the mode hierarchy
- The mode hierarchy (from most restricted to most permissive) is: plan < default < auto-edit < yolo
- An approval granted in a given mode covers that mode and all more permissive modes, but not more restricted ones
- An approval granted while in plan mode covers all modes (since it is an explicit, intentional global trust decision)
- If a tool already has a saved policy rule and its approval is later updated, the existing rule should be updated in-place rather than creating a duplicate entry

## Why This Matters

Without mode-aware approvals, granting permanent access to a tool in any mode — even a relatively permissive one — unintentionally expands that approval into the plan mode, undermining its safety guarantees. Users relying on plan mode as a safe research environment need confidence that only explicitly granted approvals apply there.
