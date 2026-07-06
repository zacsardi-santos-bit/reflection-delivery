## Description

The RDL interface definition language currently requires developers to express interface properties by writing out individually annotated getter and setter functions, using a special naming convention and a marking attribute on each. This is unnecessarily verbose and does not clearly convey the concept of a "property" as a first-class construct. Similarly, WinRT interface events must be expressed as a pair of add/remove methods rather than as a dedicated event declaration.

We should introduce a simpler, more expressive shorthand for declaring properties and events directly within interface bodies.

## Expected Behavior

- A property can be declared with a concise field-like syntax inside an interface body. Without any qualifying annotation, the property is read-write.
- A property annotated as read-only generates only a getter; a property annotated as write-only generates only a setter.
- If a property is annotated with both the read-only and write-only annotations simultaneously, the parser must reject it with a clear, location-aware error message.
- If a property carries any annotation that is not the recognized read-only or write-only annotation, the parser must reject it with a clear, location-aware error message explaining which annotations are supported.
- WinRT interface events can be declared with a dedicated shorthand that binds a name to a delegate handler type.
- The writer (used for roundtripping) must emit these shorthands back when serializing interfaces, rather than expanding them to individual method representations.

## Why This Matters

The old approach required several lines and special naming conventions for every property, obscuring intent and increasing the chance of mistakes. First-class property and event syntax makes interface definitions easier to read, write, and maintain, and brings the RDL language closer to the conceptual model of COM and WinRT interfaces.
