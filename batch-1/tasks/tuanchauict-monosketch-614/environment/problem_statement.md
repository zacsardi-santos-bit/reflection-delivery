## Description

The shape system in the Svelte/TypeScript port of MonoSketch is missing a group shape type — a container that can hold and manage multiple child shapes as a single logical unit. Without this, it is impossible to group shapes together, control the stacking order of shapes within a group, or enforce ownership rules.

Additionally, the rectangle shape type is not publicly accessible, and its constructor requires an explicit id argument even when one is not needed, making it unnecessarily cumbersome to use.

## Expected Behavior

- A group shape can be created and starts empty.
- Shapes can be added to a group at a specific position: at the beginning, at the end (default), or after a given shape already in the group.
- A shape that already belongs to a different group cannot be added to another group.
- When a shape is added to a group, the group takes ownership (the shape's parent reference is updated automatically).
- Adding a shape that is already in the group has no effect.
- Shapes can be removed from a group.
- Shapes within a group can be reordered: moved up one step, down one step, to the top, or to the bottom of the stacking order.
- The rectangle shape type is exported and usable from other modules.
- The rectangle shape can be created with just a bounding rectangle, without requiring an explicit id.
- A convenience factory method is available for creating a rectangle from a plain object with optional id and parent fields.

## Why This Matters

Groups are a fundamental building block for a drawing tool. Without the ability to group shapes, users cannot treat a collection of shapes as a single composite element, and developers cannot implement layering, reordering, or hierarchical structures in the canvas.
