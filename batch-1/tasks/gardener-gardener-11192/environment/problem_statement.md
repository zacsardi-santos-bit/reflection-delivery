## Description

The garden operator currently has no support for declaring garden-level extensions in the Garden resource. We need the ability to specify which extensions are active for a given garden, and the system should validate those declarations to prevent misconfiguration. Additionally, the extension lifecycle management component is missing operations required for full teardown: there's no way to delete all extension resources at once (rather than only stale ones), and no way to wait until all of them have been fully removed.

## Expected Behavior

- The Garden resource spec should accept a list of extension registrations, each with a type identifier and optional provider-specific configuration.
- The garden controller validation should reject a Garden that lists the same extension type more than once, returning a descriptive error that identifies the duplicate entry by its position in the list.
- A garden with a valid, duplicate-free set of extensions should pass validation without errors.
- The extension component should expose an operation to delete all of its managed extension resources (not just the ones considered stale).
- The extension component should expose an operation to wait until all managed extension resources have been fully cleaned up, returning an error identifying any resource that is still present.

## Why This Matters

Without these capabilities, the garden operator cannot properly register extensions at the garden level, cannot catch invalid configurations (like accidental duplicate extension registrations), and cannot cleanly tear down all extension resources when a garden is removed or updated. These gaps block proper extension lifecycle management in the garden operator.
