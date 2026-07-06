## Description

The observability package needs to be aware of the current navigation route when tracking screen interactivity. Right now, when a screen is marked as interactive, there is no route information attached — so performance data cannot be broken down per screen. We need the tracking system to automatically include the current route's path when recording an interactivity event.

Additionally, the module's configuration layer needs a clean way to opt out of the router integration. Currently there is no mechanism to disable the router-aware behavior without removing the integration entirely. An explicit configuration flag should allow the router integration to be skipped when desired.

## Expected Behavior

- When the app's router is available and the integration is not explicitly disabled, calling the "mark interactive" function should automatically include the current route's path name as part of the event data.
- When the current screen is not in focus, the "mark interactive" function should be a no-op and should not record anything.
- When a configuration option to disable the router integration is provided, the router integration should not be initialized, and the option should not be forwarded to the underlying native configuration layer.
- When the router is not installed, the system should skip router integration setup automatically.
- The top-level hook should fall back to the default interactivity tracking when the router integration is unavailable or disabled.
- The hook should return an object exposing only the interactivity-marking function as its sole property.

## Why This Matters

Without route-aware interactivity tracking, all screen performance data is aggregated globally, making it impossible to identify which specific screens are slow. Connecting navigation state to the tracking layer unlocks per-route performance analysis.
