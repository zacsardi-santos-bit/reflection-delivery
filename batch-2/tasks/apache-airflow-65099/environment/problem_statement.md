## Description

The CLI's remote version check command fails when the user is not logged in. Because the server version endpoint is a publicly accessible, read-only operation that doesn't require authentication, requiring stored credentials is unnecessarily restrictive.

## Current Behavior

Running the command to fetch the remote server version with the --remote flag raises a credentials error when the user isn't logged in. The command treats this unauthenticated endpoint exactly the same as authenticated ones, requiring a full login to proceed.

## Expected Behavior

- The remote version command should work without any stored login credentials.
- There should be a dedicated no-authentication client mode for calling API endpoints that don't require authentication.
- When this mode is used, no keyring access or credential file lookup should occur.
- The no-authentication mode should resolve to the standard API base URL (the same as the regular CLI mode).
- When credentials are loaded in no-authentication mode without any config file present, the result should have no token and no URL set, without raising an error.
- When a CLI-mode client is used with an explicit token provided at call time, it should work even without a local config file, using the provided token directly without keyring access.

## Why This Matters

Commands that call publicly accessible, unauthenticated endpoints should not require the user to be logged in. This is a usability issue — a developer who just wants to check the remote version of their server shouldn't have to authenticate first. The fix makes it possible to run such commands freely without prior login.
