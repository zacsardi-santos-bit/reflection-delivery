## Description

Gatus supports a wide range of alerting integrations (Slack, Discord, PagerDuty, Telegram, and others), but it does not yet support Zulip as an alerting destination. Teams that use Zulip as their primary communication platform have no way to receive Gatus monitoring alerts there.

## Expected Behavior

- A new Zulip alerting provider should be available in Gatus, configurable with a bot email, bot API key, organization domain, and channel ID.
- The provider should send alerts to the specified Zulip channel when an endpoint fails or recovers.
- Validation should ensure all required fields are present; an incomplete configuration should be rejected.
- The provider should support per-group overrides so different endpoint groups can be routed to different Zulip channels.
- Alert messages should clearly state whether the alert was triggered (due to consecutive failures) or resolved (after consecutive successes), include the alert description if set, and list each condition result with a visual indicator of pass or failure.
- Sending should authenticate using the bot's credentials.
- The provider should return an error if the Zulip API responds with a non-success status code.

## Why This Matters

Users who rely on Zulip for team collaboration cannot currently integrate Gatus into their workflow without workarounds. Adding first-class Zulip support lets these teams receive timely, structured monitoring notifications directly in their Zulip channels.
