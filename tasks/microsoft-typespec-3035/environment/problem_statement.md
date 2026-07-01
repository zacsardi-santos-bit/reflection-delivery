## Description

TypeSpec currently has no package to support XML-specific serialization annotations. When building APIs that produce or consume XML, specification authors need a way to describe how data models map to XML — which properties become XML attributes, which are unwrapped from container elements, and what XML namespace a type belongs to. Without this capability, TypeSpec users working with XML-based APIs have no standard way to express these concerns.

## Expected Behavior

A new XML library package should provide:

- A decorator to assign an XML-specific name to a model or property (independent of its TypeSpec name)
- A decorator to mark a model property as an XML attribute rather than a child element
- A decorator to mark a model property as unwrapped (not enclosed in a wrapper element)
- A decorator and supporting enum mechanism to assign XML namespace information (URL and prefix) to models and properties
- Namespace annotations should not be inherited by child properties from a parent model
- Validation that catches common mistakes: using an undeclared namespace enum, enum members without string URL values, providing redundant prefix when using an enum member, or supplying an invalid (non-URI) namespace string
- Default XML encoding mappings for common scalar types such as date/time types, durations, and binary data, with the ability to override them per-property

## Why This Matters

This enables TypeSpec to be a complete description language for XML-based APIs, giving tooling authors everything they need to generate correct XML serialization code and schemas from TypeSpec definitions.
