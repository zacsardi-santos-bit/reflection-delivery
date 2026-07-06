## Description

Renderable objects in Rails views — components that implement a method to render themselves within a view context — have no way to receive local variables or block content from the caller at render time. This makes renderable components difficult to parameterize without resorting to instance variables or other workarounds.

## Expected Behavior

- When rendering a renderable object, callers should be able to pass local variables as named options, which the renderable's rendering method will receive as keyword arguments.
- When rendering a renderable object using the hash-style render options, a named locals option should be supported and its value forwarded to the renderable as keyword arguments.
- When a block is given at the render call site, it should be forwarded to the renderable so the component can yield to it.
- These features should work both inside view templates and from the standalone renderer exposed by controllers.
- Existing renderable objects that haven't updated their rendering method signature to accept keyword arguments should trigger a deprecation warning (not a hard error), giving developers time to update their code.
- Errors raised inside the renderable's rendering method — including naming errors that arise from calling a method that does not exist on an object — must propagate to the caller without being hidden or swallowed by the framework.

## Why This Matters

Without this, renderable components are essentially static — they cannot be passed data at render time in a clean way. Supporting locals and blocks makes renderables composable and useful as lightweight view components.
