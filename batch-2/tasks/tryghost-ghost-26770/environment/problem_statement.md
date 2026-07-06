## Description

The Transistor podcasts integration in the member portal currently labels its action button "Manage", but this wording is misleading. Members using this feature are not managing anything — they are simply accessing their private podcast feed. The button text should be updated to "View" to better reflect the actual action being taken.

## Expected Behavior

- When a member has podcasts available and their identifier is present, the Transistor section should display a "Podcasts" heading and a "View" button linking to their private feed.
- The "View" link should open in a new tab.
- The link destination should follow the partner URL pattern using the member's unique identifier.
- When either the podcasts flag is not set or the member identifier is missing, the section should not be shown at all.

## Related Work

Additionally, as part of ongoing welcome email improvements in the admin settings area, a new component file is needed for a welcome email design customization modal. This file should be added to the admin settings component directory at the appropriate location within the member emails section.

## Why This Matters

Using "Manage" as the button label for viewing a private podcast feed is confusing and inaccurate. "View" communicates the correct intent — members are accessing their feed, not managing it. This small but meaningful label change improves clarity for members and aligns the UI text with the actual functionality.
