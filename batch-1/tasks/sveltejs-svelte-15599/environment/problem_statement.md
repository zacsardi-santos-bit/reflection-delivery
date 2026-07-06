## Description

The Svelte compiler's client-side "functional" rendering mode generates very verbose, imperative JavaScript code for component templates. For each component, it produces a factory function that manually creates every DOM element, text node, and comment node, sets each attribute individually, and assembles everything into a fragment step by step. This results in compiled output that can be hundreds of lines long for even moderately complex templates.

## Expected Behavior

The compiler should instead generate a compact, declarative descriptor that describes the element tree structure directly:

- Elements should be described as nested objects identifying their tag name, optional static attributes, and optional child nodes
- Text nodes should appear as plain string values
- Reactive placeholder markers (comment nodes used as anchors) should be represented as empty slots in the structure
- The runtime function that processes these templates should interpret the declarative format and build the equivalent DOM structure

## Current Behavior

Currently, the compiler emits a factory function body with dozens of individual statements for every template — one for each element creation, text node creation, comment node, attribute set, and DOM insertion. This makes compiled output unnecessarily large.

## Why This Matters

Switching to a declarative descriptor format dramatically reduces the size of compiled Svelte component code. Fewer bytes to download means faster page loads, and a more compact representation is also easier to read when inspecting compiled output. Complex templates that currently generate hundreds of lines of setup code would be reduced to a concise nested structure representing the same element hierarchy.
