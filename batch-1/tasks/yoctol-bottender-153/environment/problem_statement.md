## Description

The Telegram bot connector currently only handles a very limited set of update types when determining the session key and populating session data. Specifically, it only processes plain messages and callback queries, ignoring edited messages, channel posts, edited channel posts, inline queries, chosen inline results, shipping queries, and pre-checkout queries.

This means that bots receiving any of these other update types will fail to establish a proper session context — the session key will be empty and the session will not contain any user, group, or channel information.

## Expected Behavior

- When any of the supported Telegram update types arrives, the connector should extract a meaningful session key that identifies the conversation context (the chat id for message-based updates, or the sender's user id for query-based updates).
- The session object should be populated with three distinct context fields: user info, group info (if the update comes from a group), and channel info (if the update comes from a channel).
- For group-based updates, both the user and group session data should be present.
- For channel-based updates, only channel session data should be present.
- For private or query-based updates, only user session data should be present.
- All session data objects should include a timestamp indicating when they were last updated.

## Why This Matters

Without this change, bots built on the framework are effectively broken for a large portion of valid Telegram interactions. Group bots, channel bots, inline bots, and payment bots all rely on update types that were previously silently ignored.
