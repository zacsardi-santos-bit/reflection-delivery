## Description

The CodeCharta visualization does not correctly handle nodes in comparison (delta) mode — specifically, nodes that only carry difference values rather than absolute metric values. These arise when a file was added or removed between two compared versions of a codebase. Currently, such nodes are treated as having zero area and are displayed in a grayed-out "no area metric" state even though they represent meaningful changes. The tree view and map should recognize a negative difference value as a valid area signal and render the node accordingly.

## Expected Behavior

- A node that only has a negative difference value for the area metric should be considered valid and should not be grayed out or marked as "no area metric" in the tree view.
- A node that has a zero or positive difference value for the area metric, or has no difference value at all, should still be treated as having no valid area and rendered with the appropriate gray styling.
- Icon colors in the tree view should follow the same logic: a valid negative difference value enables the node's marking color, while a missing or non-negative difference value results in the neutral gray color.
- Building heights on the map should use zero as the minimum height in comparison mode when a delta value is present, rather than enforcing the normal minimum building height. When there is no delta, the standard minimum height applies.
- The CSS class name used to highlight search results in the tree view should be updated to a more semantically meaningful name.
- The edge arrow preparation should be split into two explicit steps: one that processes nodes into an internal data structure, and one that renders the arrows from that structure.

## Why This Matters

Users comparing two versions of their codebase rely on the visual map to understand which files changed, were added, or were removed. When added or removed files are silently hidden or grayed out, the comparison view is misleading and fails to convey important information about structural changes between versions.
