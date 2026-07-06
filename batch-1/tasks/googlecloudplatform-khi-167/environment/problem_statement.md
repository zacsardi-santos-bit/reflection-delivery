## Description

The KHI project needs a new package that provides a unified, traversable tree representation for structured data. Currently, there is no consistent way to convert arbitrary Go runtime values or parsed YAML/JSON documents into a shared node model that can be inspected and traversed with a single, predictable API.

## Expected Behavior

- A new package should implement a tree of typed nodes.
- Each node carries a type tag distinguishing whether it is a scalar value, a sequence (ordered list), or a mapping (key-value pairs).
- Scalar nodes expose their underlying Go value, supporting null, boolean, string, integer, floating-point, and timestamp types.
- Sequence and map nodes expose their children through an iterator that provides both a positional index and, for maps, a string key on each child entry.
- A function to convert an arbitrary Go value (scalar, slice, or map) into the node tree must be provided, along with a helper for ordering map keys alphabetically.
- A function to parse YAML and JSON text directly into the node tree must be provided. Since YAML is a superset of JSON, the same function should handle both formats.
- A utility for building dotted path strings must escape dots that appear within individual key segments.

## Why This Matters

Downstream code that processes configuration files or structured data from multiple sources (YAML configs, JSON payloads, in-memory Go values) can use a single traversal API rather than format-specific logic. Alphabetical key ordering ensures deterministic behavior when processing maps.
