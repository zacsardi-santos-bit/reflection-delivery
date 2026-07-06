I'm running into a navigation bug where relative paths aren't being resolved correctly when I specify a parent or ancestor route as the starting point for navigation.

Here's the scenario: my app has nested routes, and a component rendered at a deep child route includes a navigation link that specifies a parent route as its starting point. When I use a relative destination path (like navigating to a sibling of the starting route), the relative path gets resolved from the currently active deep route instead of from the specified starting route. So instead of landing on the correct sibling, I end up at a nonsensical nested path.

The same issue happens with parent-relative path traversal — the traversal goes up from the wrong base route.

This bug affects both navigation links (rendered as anchor elements with an href) and programmatic navigation triggered by user actions. Both should respect the specified starting route when computing the final destination.

On top of that, when the specified starting route doesn't exist in the current active route hierarchy at all, there's no error thrown or displayed — the app just behaves incorrectly without any feedback. There should be a clear error in that situation so it's easy to diagnose.

Can you fix the relative path resolution logic so that the specified starting route is properly used as the base for all relative path calculations, and add an error when the starting route can't be matched?
