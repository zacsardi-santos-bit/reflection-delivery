I'm working with the prometheus-operator and I'd like to add support for sending Telegram alert notifications to a specific group topic (thread) rather than always sending to the main group chat. Telegram groups can have topics enabled, and I need to be able to specify a topic ID in the receiver configuration.

This feature should only be available for newer versions of Alertmanager. When the operator is configured to target an older Alertmanager version and a topic ID is present in the config, the field should be quietly dropped with a warning rather than causing a hard error.

There's also a compile error in the end-to-end test for scrape configs — the labels map for static targets uses the wrong key type and needs to be corrected to use plain strings.
