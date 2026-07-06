## Description

When a new member is invited to join a group, they need access to the group's ratchet tree to reconstruct the group state. Currently there is no way to configure a group to automatically include the ratchet tree in the Welcome message that is sent to new joiners. This forces deployments to always distribute the ratchet tree through a separate out-of-band channel, which complicates integration.

There should be a configuration option on the managed group that controls whether the ratchet tree is embedded in Welcome messages. When enabled, new members can join directly from the Welcome message alone, without needing any separately-distributed ratchet tree. When disabled (the current behavior), attempting to join from a Welcome without an externally-provided ratchet tree should fail with a clear, specific error rather than silently misbehaving.

## Expected Behavior

- The group configuration should expose a flag to enable or disable ratchet tree extension inclusion in Welcome messages.
- When the flag is enabled and a new member is added, the Welcome message should contain the ratchet tree so the new member can join successfully without any additional data.
- When the flag is disabled and a new member attempts to join using only the Welcome message (without an external ratchet tree), the operation should fail with a specific, identifiable error indicating that the ratchet tree is missing.

## Why This Matters

This makes it easier to deploy and test MLS group operations in scenarios where out-of-band ratchet tree distribution is not available or desirable, and provides a clear error signal when joining is attempted without the required data.
