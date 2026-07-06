# Arcam FMJ: Improve connection error handling in config flow

## Description

The Arcam FMJ integration's device setup flow has two usability problems related to how connection failures are handled.

First, when a device is automatically discovered on the network, the integration currently shows a confirmation screen before attempting to connect. This means that if the device is unreachable, the user has to go through a confirmation step only to be told setup failed. The connection check should happen during discovery so that unreachable devices are immediately rejected without showing the user a dead-end confirmation screen.

Second, when a user sets up the device manually (entering host and port), if the connection attempt fails for any reason, the entire setup flow is currently aborted. The user gets no useful feedback and has to start over. Instead, the form should stay open and display a descriptive error message explaining what went wrong (e.g., the host could not be resolved, the connection was refused, or the attempt timed out), so the user can fix the issue and try again.

## Expected Behavior

- For automatically discovered devices: if the device is unreachable, abort the flow immediately without showing a confirmation form
- For manually configured devices: if the connection fails, show the configuration form again with a specific error message indicating the type of failure
- Different types of network failures should produce distinct error messages (invalid hostname, connection refused, timed out, etc.)
- After correcting the input, the user should be able to successfully complete the setup

## Why This Matters

These improvements make setup failures actionable rather than confusing. Users get clear feedback about what went wrong and can fix it without restarting the entire process.
