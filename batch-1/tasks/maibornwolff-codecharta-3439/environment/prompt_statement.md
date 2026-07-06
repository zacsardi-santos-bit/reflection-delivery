I'm working on a code visualization tool that supports comparing two versions of a codebase side by side. In this comparison mode, some nodes only have difference values (positive or negative) instead of regular absolute metric values — for instance, a file that was newly added or deleted will only have a delta, not a standard attribute value.

The problem is that the tree view and the 3D map currently treat these delta-only nodes as if they have zero area, so they get grayed out and rendered as if they're irrelevant. But a negative delta value is actually meaningful — it signals that a file shrank or was removed — and those nodes should be displayed normally with proper sizing and coloring, not suppressed.

I need the area validity logic throughout the application to recognize negative difference values as valid. Concretely: when a node only has a negative delta for the area metric, the tree view item name should not receive the "no area metric" visual treatment, the icon color pipe should return the marking color rather than the neutral gray, and the map layout should use zero as the minimum building height in comparison mode rather than the normal positive minimum.

There's also a related refactoring needed: the edge arrow service currently handles both populating an internal edge data structure and rendering arrows in a single method call. These two responsibilities should be separated into distinct method calls so that the data-preparation step and the rendering step are independent.

Additionally, the CSS class name used to visually highlight search-matched items in the tree view needs to be renamed to something more semantically appropriate.
