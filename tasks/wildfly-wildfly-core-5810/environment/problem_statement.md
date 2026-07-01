## Description

The installation manager operations for listing available updates, applying updates, and reverting to a previous installation state currently support two cache-related behaviors: specifying a custom local repository path, or disabling cache resolution entirely. However, there is no option for administrators to explicitly request use of the default local Maven repository. This gap makes it harder to write unambiguous, self-documenting management operations, because the intent of "use the default" must be inferred from the absence of other options rather than stated directly.

A new option should be added to these operations that explicitly enables caching and resolution against the default local Maven repository. When enabled, this option should be incompatible with also specifying a custom cache path — that combination represents a contradiction and should be rejected with a clear error. Similarly, this new option must be incompatible with the existing flag that disables local cache resolution altogether, since those two behaviors directly conflict with each other.

## Expected Behavior

- Providing the new option alongside a custom local cache path must be rejected with a distinct error.
- Providing the new option alongside the option to skip local cache resolution must be rejected with a different distinct error.
- Enabling the new option alone must result in the default Maven local repository being used.
- Disabling the new option alone must result in no local repository being set.
- The CLI update and revert commands must expose this new option as a flag.

## Why This Matters

Administrators benefit from being able to explicitly state their caching intent rather than relying on implicit defaults. Clear validation errors for incompatible option combinations prevent silently misconfigured operations.
