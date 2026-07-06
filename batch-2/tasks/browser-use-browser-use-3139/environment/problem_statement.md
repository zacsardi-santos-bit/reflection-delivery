## Description

The browser automation library currently only supports running browsers locally, but many users need to connect to cloud-hosted browser sessions instead. There is no built-in support for authenticating with a cloud browser service, starting a remote browser session, obtaining a connection endpoint, or stopping sessions when finished.

## Expected Behavior

- Users should be able to configure authentication either through an environment variable or a saved configuration file
- Starting a cloud browser session should return a connection endpoint (WebSocket URL) that can be used to connect to the remote browser
- Stopping a cloud session should cleanly terminate it and return the updated session status
- When authentication credentials are missing, the system should raise an informative error pointing the user to the correct environment variable
- When authentication fails (rejected by the server), a clear authentication failure error should be raised
- Failures during cloud browser setup should not silently fall back to running a local browser — the cloud setting should remain in effect
- Browser profile and session objects should expose a property indicating whether a cloud browser is being used, and the profile should correctly reflect that it is not a local session when cloud mode is enabled

## Why This Matters

Users running automated tasks in cloud environments or wanting managed browser infrastructure need a reliable, first-class way to connect to remote browsers. Without this integration, they have to handle all the API communication and session lifecycle themselves, with no standardized error handling or authentication support.
