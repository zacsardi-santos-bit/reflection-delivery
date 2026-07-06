## Description

It is currently not possible to add custom views to a collection in the admin panel that are accessible at dedicated URLs. When a developer registers a custom view on a collection with a specific path (for example, a grid view or map view at a sub-route of that collection), the admin router does not recognize it and falls through to the default document editing view instead of rendering the custom view. Additionally, when developers misconfigure a custom view — for example, by forgetting to specify a required path or component reference — no warning is shown at startup, making debugging difficult.

There is also a related routing bug: the path matching utility used internally does not correctly handle prefix-style route matching. A sub-path like a deeply nested route should be recognized as matching a shorter parent path, but the current logic gets the comparison backwards and fails to match.

## Expected Behavior

- Custom views added to a collection's admin configuration at a specific path should be rendered when a user navigates to that path in the admin panel.
- The routing system should check for matching custom collection views before falling back to the default edit view, so developers can add views like "grid", "map", or any other collection-level page.
- Built-in system routes (such as folder browsing) must always take precedence over conflicting custom view registrations.
- The path matching logic should correctly handle prefix-style (non-exact) matching, recognizing sub-paths at proper segment boundaries.
- At startup, a warning should be logged for any custom view that is missing a required path or component reference, including the name of the misconfigured view and the collection it belongs to.

## Why This Matters

Without this feature, custom collection-level views simply do not work — developers have no way to add extra pages to a collection in the admin UI. The lack of validation warnings also makes it hard to catch configuration mistakes early.
