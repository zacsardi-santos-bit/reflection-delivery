## Description

When listing configured protocol servers, there is currently no way to distinguish between servers that are intentionally prevented from running and servers that simply failed to connect. A server excluded by administrator configuration and one that timed out look the same to the user, both appearing as "disconnected." This makes it difficult to understand the actual state of each server.

## Expected Behavior

- Servers that are blocked by an administrator exclusion configuration should display a "Blocked" status when listed, and no connection attempt should be made to them.
- Servers that have been individually disabled through a per-server enablement mechanism should display a "Disabled" status when listed, and no connection attempt should be made to them.
- The status display view should correctly separate blocked servers from the regular server list — a server that appears as blocked should not also appear in the regular connected-server section.

## Why This Matters

Users need to quickly understand why a server is unavailable. Without distinct status indicators for "blocked" and "disabled" states, there is no way to tell whether a server is down due to a network issue or intentionally prevented from running. This also prevents unnecessary connection attempts to servers that are known to be blocked or disabled, improving performance and reducing confusing error output.
