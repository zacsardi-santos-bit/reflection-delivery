## Description

Charts in the Supabase Studio dashboard do not currently have a way to synchronize hover interactions across multiple charts simultaneously. When a user hovers over a data point on one chart, the other charts on the page remain unresponsive — there is no shared highlighting or tooltip display. Additionally, there is no way to persist the user's preference for synchronized interactions between page loads.

## Expected Behavior

- A new chart hook should be introduced to manage hover state that can optionally be shared across all chart instances on the page.
- When synchronized hover is enabled, hovering a data point on any chart should cause all charts to reflect the same hovered position.
- Each chart should be able to distinguish whether it is the one directly being hovered or is simply reflecting a synced hover from another chart.
- Tooltip display should also be optionally synchronizable, as a separate preference on top of hover syncing.
- Enabling tooltip sync should automatically enable hover sync; disabling hover sync should automatically disable tooltip sync.
- Both preferences (hover sync and tooltip sync) should be stored in the browser's local storage so they persist across page reloads.
- If stored preferences are corrupted or unreadable, the system should fall back to defaults gracefully (syncing off) with a warning, rather than breaking.
- If the browser storage is unavailable or throws when saving, the in-memory state should still be updated and a warning should be logged.

## Why This Matters

Users who work with multiple time-series or comparative charts benefit greatly from being able to see the same time point highlighted across all charts simultaneously. Without this feature, cross-chart data comparison is tedious. Persisting the user's sync preference avoids having to re-enable it after every page load.
