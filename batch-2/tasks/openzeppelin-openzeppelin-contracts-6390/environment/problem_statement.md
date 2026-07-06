## Description

In ERC7579-compatible smart accounts, a hook module is used to intercept and validate operations — running checks before and after each action. Currently, when a user tries to remove the hook module itself, the system still invokes the hook's own check functions as part of the uninstall flow. If those check functions revert (due to a bug, a malicious hook, or because the hook's contract code was removed), the uninstallation fails and the hook becomes permanently stuck.

This means a broken or hostile hook module can make itself irremovable, potentially locking an account into a degraded or compromised state.

## Expected Behavior

- When a user uninstalls the hook module itself, any failures in the hook's own pre-check or post-check must be tolerated. The uninstallation should succeed regardless of whether the hook reverts or its code is missing.
- If the pre-check fails, neither the pre-check nor post-check callbacks should fire, and the hook module should be cleanly removed.
- If the pre-check succeeds but the post-check fails, the pre-check callback fires, the post-check is skipped, and the hook module is cleanly removed.
- For all other module types (non-hook modules), hook check failures must still cause the operation to revert, preserving normal security guarantees.

## Why This Matters

Users and account managers must always be able to remove a malfunctioning or malicious hook from their smart account, even if the hook is designed to resist removal. Without this fix, any hook that starts reverting on its checks becomes a permanent fixture, blocking account recovery.
