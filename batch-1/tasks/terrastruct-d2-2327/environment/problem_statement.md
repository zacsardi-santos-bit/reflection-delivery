## Description

When a diagram has a title positioned at the top center, the title should appear clearly above all diagram content — including both the shapes and the connection lines between them. However, there is a bug where the title placement only accounts for the bounding box of the shapes, ignoring where connection lines are actually routed.

This means that when connections are routed outside of the container boundaries (which is common in complex diagrams with back-edges or circular references), the title ends up too close to or overlapping with those connection lines.

## Expected Behavior

- A diagram title with top-center positioning should be placed above **all** diagram content, including connection routes that extend beyond shape boundaries.
- The bounding box used to position the title should include the full extent of all edge routes, not just the shapes.
- This behavior should work correctly with all layout engines, including the ELK layout engine, which was specifically affected by this bug.

## Steps to Reproduce

Create a diagram with:
- A title with top-center positioning
- Nested containers with child nodes
- A back-edge or circular connection that gets routed outside the container boundaries

When rendered with the ELK layout engine, the title will overlap with the connection route instead of appearing cleanly above the diagram.

## Why This Matters

Diagram titles serve as labels for the whole diagram and should always be visually distinct and separated from the diagram content. Overlapping connection lines make the title difficult to read and the diagram look broken.
