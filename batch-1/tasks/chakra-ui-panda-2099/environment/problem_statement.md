## Description

CSS container queries are a modern layout technique that allows elements to respond to the dimensions of their containing element rather than the whole viewport. This makes components truly self-contained and reusable regardless of where they're placed in the layout. Currently, our framework has no built-in utility for setting up container query contexts — developers have to manually apply the necessary CSS properties, and the responsive syntax shorthand they're used to for viewport breakpoints doesn't work for container targets.

We need a built-in preset pattern for defining container query contexts, so developers can:
- Declare an element as a container with a given name
- Use the familiar responsive shorthand to style children relative to that named container
- Get sensible defaults (inline-size container type) out of the box

## Expected Behavior

- A dedicated built-in pattern should exist for creating container query wrapper elements
- The pattern should accept an optional container name and an optional container type, defaulting to inline-size
- Both a functional API and a JSX component API should be available
- Responsive styles targeting a named container using the at-sign/name/size shorthand syntax should generate the correct scoped container query CSS rule
- The generated CSS should apply container-type and container-name properties with appropriate utility class names

## Related Issue

Additionally, the at-rule CSS sorting logic should extend to handle container query rules the same way it handles media query rules — sorting by breakpoint width so the cascade order is correct and predictable.

## Why This Matters

Without this pattern, developers writing responsive component-based layouts must manage container queries manually, losing the DX benefits the framework provides for viewport-based responsive styles. Supporting container queries as a first-class pattern keeps the framework aligned with modern CSS capabilities and enables more portable, container-aware component design.
