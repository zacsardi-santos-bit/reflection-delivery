## Description

Home Assistant supports marking certain user accounts as "local only," meaning they should only be able to log in when connecting from within the local network. However, this restriction is not currently enforced for WebSocket connections. A local-only user can connect from any remote IP address and successfully authenticate via the WebSocket API, which defeats the purpose of the local-only designation.

## Expected Behavior

- A user marked as local only should be **rejected** when attempting to authenticate via the WebSocket endpoint from a remote (non-local) IP address. The response should indicate that the user cannot authenticate remotely.
- The failed authentication attempt from a remote IP should be recorded the same way other authentication failures are tracked.
- A user marked as local only should still be **allowed** to authenticate via WebSocket when connecting from a local network IP address.

## Why This Matters

Users marked as local-only represent accounts that should never be accessible from outside the home network. Without enforcement in the WebSocket authentication layer, remote attackers who obtain an access token for a local-only account can still gain access through the WebSocket endpoint. Enforcing this restriction consistently closes the security gap.
