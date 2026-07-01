## Description

When a group is configured to embed the ratchet tree directly into outgoing Welcome messages, a new member attempting to join via such a Welcome message fails. The library should be able to extract the ratchet tree from inside the Welcome message and use it to successfully construct the new member's group state — but currently this doesn't work. As a result, joining a group via a self-contained Welcome message is broken whenever the ratchet tree is distributed inline rather than out-of-band.

## Expected Behavior

- When a group is set up to include the ratchet tree in Welcome messages, a new member should be able to join the group using only the Welcome message (without needing the ratchet tree supplied separately).
- Welcome messages that embed the ratchet tree should round-trip correctly through encoding and decoding.
- Commit messages with a combination of add, remove, and update proposals should also encode and decode correctly.

## Why This Matters

The MLS protocol supports distributing the ratchet tree inside the Welcome message as a convenience, so new joiners don't need to obtain the tree through a separate channel. If this in-band tree distribution doesn't work end-to-end — including through a Welcome message encode/decode cycle — then one of the main group-joining paths in the protocol is broken. This fix ensures that the self-contained Welcome message joining flow works correctly.
