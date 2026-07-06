Implement a Zulip alerting provider for Gatus to send notifications when monitored endpoints fail or recover. Configure the provider with a bot email, bot API key, organization domain, and channel ID, ensuring all fields are present and valid.

*   Implement the `IsValid` method for the `AlertProvider` struct:
    *   Return `false` if any of the four required fields (BotEmail, BotAPIKey, Domain, ChannelID) are empty.
    *   Return `false` if any override has an empty group name or any required field is empty.
    *   Return `true` only when all fields in the base config and overrides are non-empty.

*   Implement the `getChannelIdForGroup` method:
    *   Return the default ChannelID if the group string is empty or does not match any override.
    *   Return the override's ChannelID when a matching group is found.

*   Implement the `buildRequestBody` method:
    *   Create a URL-form-encoded body with fields: 'type' as 'channel', 'to' as the channel ID for the endpoint's group, 'topic' as 'Gatus', and 'content' as the formatted alert message.
    *   Format `content`:
        *   When `resolved` is `true`, use: 'An alert for **{endpoint-name}** has been resolved after passing successfully {N} time(s) in a row'.
        *   When `resolved` is `false`, use: 'An alert for **{endpoint-name}** has been triggered due to having failed {N} time(s) in a row'.
        *   Append alert description if present: '\n> {description}\n'.
        *   Append condition results: '\n:check: - `{condition}`' for successes, '\n:cross_mark: - `{condition}`' for failures.

*   Implement the `GetDefaultAlert` method:
    *   Return the `DefaultAlert` field of the provider, which may be nil.

*   Implement the `Send` method:
    *   Send an HTTP POST request to 'https://{Domain}/api/v1/messages' with the body from `buildRequestBody`.
    *   Set headers: "Content-Type: application/x-www-form-urlencoded" and "User-Agent: Gatus".
    *   Use HTTP Basic Auth with BotEmail and BotAPIKey.
    *   Return `nil` for HTTP status codes < 400.
    *   Return a non-nil error for HTTP status codes >= 400.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.