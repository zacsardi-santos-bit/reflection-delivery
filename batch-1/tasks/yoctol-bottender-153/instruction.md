Implement the `getUniqueSessionKey` and `updateSession` methods in the `TelegramConnector` class located in `src/bot/TelegramConnector.js`. These methods should handle various Telegram update types to correctly determine session keys and populate session data.

*   Implement `getUniqueSessionKey(body: TelegramRequestBody): string` to return a session key based on the update type:
    *   For `body.message`, `body.edited_message`, `body.channel_post`, and `body.edited_channel_post`, return the chat id as a string.
    *   For `body.inline_query`, `body.chosen_inline_result`, `body.callback_query` (without a message), `body.shipping_query`, and `body.pre_checkout_query`, return the sender's user id as a string.
    *   For `body.callback_query` with an associated message, return the message's chat id.
    *   Return an empty string for unknown or empty bodies.

*   Implement `updateSession(session: Session, body: TelegramRequestBody): Promise<void>` to populate session data:
    *   For `body.message` in a private chat, set `session.user` with the sender's info and `_updatedAt` timestamp; set `session.group` and `session.channel` to undefined.
    *   For `body.message` in a group chat, set both `session.user` and `session.group` with respective info and `_updatedAt`; set `session.channel` to undefined.
    *   For `body.edited_message` in a private chat, set `session.user` with the sender's info and `_updatedAt`; set `session.group` and `session.channel` to undefined.
    *   For `body.edited_message` in a group chat, set both `session.user` and `session.group` with respective info and `_updatedAt`; set `session.channel` to undefined.
    *   For `body.channel_post` and `body.edited_channel_post`, set `session.channel` with the chat info and `_updatedAt`; set `session.user` and `session.group` to undefined.
    *   For `body.inline_query` and `body.chosen_inline_result`, set `session.user` with the sender's info and `_updatedAt`; set `session.group` and `session.channel` to undefined.
    *   For `body.callback_query` in a private chat, set `session.user` with the sender's info and `_updatedAt`; set `session.group` and `session.channel` to undefined.
    *   For `body.callback_query` in a group chat, set both `session.user` and `session.group` with respective info and `_updatedAt`; set `session.channel` to undefined.
    *   For `body.shipping_query` and `body.pre_checkout_query`, set `session.user` with the sender's info and `_updatedAt`; set `session.group` and `session.channel` to undefined.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.