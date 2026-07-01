## Description

When communities or channels have token-based access restrictions, it's necessary to manage encryption keys carefully in response to state changes. Currently, there is no systematic way to evaluate what cryptographic key actions are required when a community's permissions or membership changes. For example, when a member is removed from a restricted community, the remaining members should receive a fresh encryption key to maintain security. When a community first gains token-gating, an encryption key needs to be created. When a community loses its token-gating, the encryption key should be removed.

## Expected Behavior

The system should be able to compare two states of a community (before and after a modification) and produce a structured decision about what encryption key actions are needed:

- If a community or channel transitions from open to token-gated, a new encryption key should be added.
- If a community or channel transitions from token-gated to open, the encryption key should be removed.
- If a member is removed from a token-gated community or channel, the encryption key must be regenerated and redistributed to remaining members.
- If new members join a token-gated community or channel with no removals, the existing encryption key should be sent to only those new members.
- If nothing changed, or only non-access-controlling permissions changed, no key action is needed.

This evaluation should work both at the overall community level and at the individual channel level within a community.

## Why This Matters

Without this logic, the system cannot make correct decisions about when to create, rotate, remove, or redistribute encryption keys. This is a core security requirement for ensuring that members who are removed from a token-gated community or channel cannot continue to decrypt future messages, and that newly admitted members can immediately access the content they are entitled to.
