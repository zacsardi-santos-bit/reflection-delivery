Fix the bug in relative path resolution for navigation links and programmatic navigation when an ancestor route is specified as the starting point. Ensure that relative paths are resolved correctly and errors are reported when the starting route does not match any in the current hierarchy.

*   Resolve relative paths for navigation links:
    *   When a link has a 'from' prop specifying an ancestor route and a 'to' path starting with './', resolve the href relative to the 'from' route.
    *   When a link has a 'from' prop specifying an ancestor route and a 'to' path starting with '../', start path traversal from the 'from' route's level.
    *   If the 'from' prop does not match the current active route or any ancestors, display an error: 'Invariant failed: Could not find match for from: <value>'.

*   Resolve relative paths for programmatic navigation:
    *   When the navigate function is called with a 'from' option specifying an ancestor route and a 'to' path starting with './', resolve the destination relative to the 'from' route.
    *   When the navigate function is called with a 'from' option specifying an ancestor route and a 'to' path starting with '../', start path traversal from the 'from' route's level.
    *   If the 'from' option does not match the current active route or any ancestors, throw an error synchronously.

*   Preserve dynamic route parameters:
    *   Ensure dynamic route parameters (e.g., $postId) are inherited and preserved when navigating using an ancestor 'from' path combined with a relative 'to' path.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.