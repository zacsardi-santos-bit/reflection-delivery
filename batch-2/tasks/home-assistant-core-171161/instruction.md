I'm working on improving the setup flow for the Arcam FMJ integration in Home Assistant.

*   When a device is auto-discovered via SSDP and the connection attempt raises any network-related exception (general connection failure, connection refused, OS-level error, DNS resolution error, or timeout), the config flow must immediately abort with reason 'cannot_connect' — no confirmation form should be presented to the user.

*   When a user manually configures the integration (user flow) and the connection attempt fails, the flow must return the user form (step 'user') with a 'base' error key set to a specific code depending on the exception type: 'cannot_connect' for general connection failures (ConnectionFailed) or OS errors, 'connection_refused' for refused connections, 'invalid_host' for DNS or hostname resolution failures, and 'timeout_connect' for connection timeouts.

*   After displaying a connection error in the user flow, the form must allow the user to retry; if the subsequent connection attempt succeeds, the flow must create a config entry with title 'Arcam FMJ ({host})' (where {host} is the user-supplied hostname) and data containing the host and port values.

*   The integration's translation strings file (homeassistant/components/arcam_fmj/strings.json) must define error entries under the config flow 'error' section for all four error codes: 'cannot_connect', 'connection_refused', 'invalid_host', and 'timeout_connect'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.