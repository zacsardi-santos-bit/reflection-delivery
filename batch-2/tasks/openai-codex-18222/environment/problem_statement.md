## Description

The plugins popup in the terminal UI currently shows a single flat list of all available plugins from every marketplace source, mixed together without any filtering. There is no way for a user to quickly see only their installed plugins, or to browse plugins from one specific marketplace in isolation. Additionally, the popup lacks any contextual tab navigation, and when the plugin list refreshes in the background the user loses their current navigation position.

## Expected Behavior

- The plugins popup should present a tabbed interface navigable with left/right arrow keys:
  - An "All Plugins" tab showing every available plugin from all marketplace sources
  - An "Installed" tab that shows only currently installed plugins, with the header indicating "Installed plugins." and a count of how many are installed ("Showing N installed plugins.")
  - A tab dedicated to the official curated marketplace, showing only plugins from that source and omitting the marketplace source label from individual plugin rows (since it is redundant within that tab)
  - One tab per additional marketplace source
- Switching to a different tab must automatically clear any active search query
- When multiple marketplace sources share the same display name, their tabs must be visually differentiated with a numbered suffix, e.g., "Marketplace Name (1/2)" and "Marketplace Name (2/2)"
- Marketplace-specific tabs must be identified by the marketplace's file system path so that tabs with duplicate names remain individually addressable
- When the plugin list data refreshes in the background, the currently active tab must be preserved so the user does not unexpectedly return to the first tab

## Why This Matters

Without per-category filtering, users with many installed plugins across multiple marketplace sources must scroll through a long mixed list to find what they need. The tabbed structure dramatically improves discoverability and allows users to stay focused on the subset of plugins that is relevant to them at any given time.
