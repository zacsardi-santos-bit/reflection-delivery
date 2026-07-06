## Description

The fill-viewport sliver widget is currently implemented by composing several internal wrappers — a layout builder, a padding layer, and a fixed-extent list — to achieve its behavior. This creates an unnecessarily deep rendering hierarchy with confusing debug output: when developers inspect the resulting render tree, they see an internal layout builder class at the root of the subtree instead of the actual fill-viewport render object.

## Expected Behavior

- The fill-viewport sliver widget should directly create and manage its render object, without delegating through a layout builder or intermediate padding/fixed-extent-list wrappers.
- The debug output of the render tree should show the fill-viewport render object at the top level, with its children attached directly underneath.
- The fill-viewport render object should be a proper, first-class render sliver that directly manages its children, consistent with how other sliver adaptor render objects work.
- The render object should not be marked as deprecated — it should be a fully supported, public-facing API that can be used directly.

## Why This Matters

The current indirect implementation makes the render tree harder to understand and debug. Developers who inspect the widget hierarchy see internal implementation details rather than the meaningful component name. Making the fill-viewport sliver a first-class render object simplifies the widget structure and makes the implementation consistent with how other sliver adaptor widgets work.
