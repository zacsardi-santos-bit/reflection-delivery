## Description

The XDS override host load balancing policy does not correctly handle the case where the targeted override host subchannel is in a non-READY connectivity state. Currently, when a pick is made requesting a specific host override and that host's subchannel is still connecting or has gone temporarily offline, the policy has no defined behavior for those intermediate states. This can lead to traffic being misrouted or the override being silently ignored in situations where it should be honored.

## Expected Behavior

- When the targeted override host subchannel is actively establishing a connection (in a connecting state), picks targeting it should be held in a queue until the connection succeeds.
- When the targeted override host subchannel has gone idle, the policy should proactively initiate a new connection to it and queue the pick while waiting.
- When the targeted override host subchannel has entered a permanent failure state, the override should be abandoned and the pick should fall through to the remaining healthy subchannels in the pool.
- Whenever a tracked override host subchannel transitions to an idle or failed state, the policy should trigger name re-resolution so the system can obtain fresh endpoint information.

## Why This Matters

Without this fix, session stickiness and host-pinning features that rely on the override host mechanism can fail silently when a targeted host undergoes transient connectivity changes. The policy needs to handle these intermediate states gracefully to ensure correct behavior during rolling deployments, brief connectivity blips, or backend restarts.
