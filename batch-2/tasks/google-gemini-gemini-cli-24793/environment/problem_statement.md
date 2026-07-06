## Description

The topic message display in the CLI is currently always in a compact, collapsed state. It shows the title and a short strategic intent, but there is no way to see the more detailed summary associated with a topic. Users have no mechanism to expand a topic entry to view its full details.

## Expected Behavior

- When the terminal is in an unconstrained view (no height limit), the topic message should automatically display both the strategic intent and the full summary.
- Users should be able to click on a topic message to toggle its expanded state, revealing the summary text beneath the intent line.
- The expansion state should be tracked per-item using a shared context, so that only the items the user has clicked are expanded.
- When only a summary is present (no strategic intent), it should be shown as the primary description in the default collapsed view.
- When only a strategic intent is present (no summary), only the intent should be shown with no expand control offered.

## Why This Matters

Right now users can only see a brief summary of what the model is working toward. Having the ability to expand individual topic entries — or automatically expand them when viewing the full conversation — makes the interface more transparent and useful, especially for longer or more complex sessions.
