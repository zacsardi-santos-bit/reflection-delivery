## Description

Context and scope matching incorrectly handles two types of site tree nodes: nodes that are children of "data-driven" placeholder nodes, and leaf nodes that have parameter names appended to their display labels.

When a user defines a scanning context with include/exclude URL regular expressions, ZAP walks the site tree to decide which nodes are in scope. Two cases produce wrong results:

1. **Data-driven nodes**: The site tree uses special placeholder nodes to represent dynamic URL segments (for example, a segment whose value is driven by a data set). When a child node sits beneath such a placeholder, ZAP does not reconstruct the full URL correctly for scope matching. As a result, include/exclude regexes that reference the dynamic segment never match, so child nodes are silently left out of (or incorrectly included in) the context.

2. **Nodes with parameter annotations**: Leaf nodes append their URL parameter names in parentheses to the display name shown in the site tree. When these nodes are evaluated against context regexes, the parameter annotations are mistakenly included in the URL used for matching, causing regexes that match the bare path to fail.

## Expected Behavior

- A node that sits beneath a data-driven placeholder node should be considered included in a context when the context's include regex matches the full reconstructed URL (where the placeholder segment acts as a wildcard).
- A node that sits beneath a data-driven placeholder node should be considered excluded from a context when the context's exclude regex matches the reconstructed URL, and should not be in scope.
- Retrieving the list of contexts that contain a node beneath a data-driven placeholder should return the correct contexts.
- A node whose display name contains parameter annotations in parentheses should match context regexes based on the clean path name only, without those annotations.
- Nodes should also expose a clean name (the path segment name without method prefix or parameter annotations) for use in URL reconstruction and display.

## Why This Matters

Users rely on context scoping to limit scans to relevant parts of a web application. When data-driven paths or parameterized endpoint nodes are not recognized as in-scope, those paths are silently skipped during active scanning, leaving potential vulnerabilities undetected.
