## Description

The glossary term detail page currently builds its entire tab configuration inline inside the main component. This tight coupling makes it impossible for different deployments to customize or override the tab structure without modifying the core component. We need to extract this logic into a dedicated base class and a standalone utility function so the tab generation can be extended cleanly via subclassing.

Similarly, the tag detail page has a hardcoded approach for injecting an optional tab (for a recognizer feature). This should be replaced with a general-purpose extension method on the base class that returns an empty list by default, allowing subclasses to add tabs without touching the core tag page logic.

There is also a behavioral issue with the glossary term component: when a user is browsing a historical version of a glossary term, the activity feed count is fetched on page load even though that data is irrelevant in version history context. This unnecessary fetch should be skipped when the component is in version-view mode.

## Expected Behavior

- A base class for glossary term detail pages should provide a method that generates the tab array by delegating to a utility function, and a separate method that returns the list of standard tab identifiers (5 tabs: overview, child terms, assets, activity feed, and custom properties — all non-editable with empty layouts).
- A standalone utility function should generate the complete tab array. In normal view, it returns all 5 tabs in the expected order. In version-view mode, it returns only the overview tab.
- The activity feed tab's label should reflect whether it is the active tab, and should display the current feed count.
- The custom properties tab should correctly compute edit access based on the user's permissions and whether version-view mode is active.
- The tag detail page base class should expose an extension method for adding extra tabs, returning an empty list by default.
- The tag detail page should invoke this extension method with the fetched tag when building the tab list.
- The glossary term component must skip the activity feed count fetch when displaying a historical version.

## Why This Matters

Extracting the tab-building logic into overridable utility structures allows specialized deployments to customize the detail page experience without forking the core components. The version-view fix avoids an unnecessary network request that could cause confusing behavior when browsing historical records.
