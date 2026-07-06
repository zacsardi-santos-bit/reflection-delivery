## Description

The current device management system uses a context-variable mechanism to determine the default device, which means the default device is not a simple assignable value but is instead derived from a context variable at runtime. This causes two related problems:

1. The default device cannot be temporarily changed by directly assigning to it — developers who want to test or temporarily override the default device must use the context manager interface, which is overly complex for this use case.
2. The deprecation check for old-style device environment variables is tied to the presence of the context variable, meaning the deprecation warning may not fire reliably when running in certain environments where the context variable is not explicitly configured.

## Expected Behavior

- The default device should be a directly settable value on the device manager, so that code can assign a new default and later restore the original without needing a context manager.
- When the device manager's default is set to a lowercase device name and then canonicalized, the result should be the correctly uppercased canonical device name.
- Accessing the default device in an environment where an old-style device env variable is set should always produce a deprecation error, regardless of whether a separate device context variable is present or absent.
- The context-variable-based approach to temporarily switching the active device is removed; this functionality is no longer required.

## Why This Matters

Relying on a context variable to power the default device makes the system harder to reason about and creates fragility in the deprecation path. Simplifying to a directly-assignable default removes unnecessary indirection and ensures the deprecation warning works consistently across all environments.
