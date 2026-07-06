## Description

When a Mitosis component defines state using the store hook and a developer adds an inline comment next to one of the state property values, the compiler fails to handle the component correctly. The comment text bleeds into the property's value representation during parsing, which corrupts the internal component model and causes broken or invalid generated code when the component is compiled to any target framework.

## Expected Behavior

- A component that defines a state property with an inline comment after its value should be parsed correctly, with the comment stripped from the property value
- State methods in the same store object should also be unaffected by adjacent comments
- The component should compile correctly to all supported target frameworks with the comment absent from the output
- The parsed internal representation of the component should contain only the clean property value without any comment text

## Why This Matters

Developers naturally want to document their state properties inline. Being unable to add a simple comment next to a property value is an unexpected restriction, and when someone does add one, it silently corrupts the compilation rather than producing a clear error. This makes it impossible to compile the component to any of the supported output targets.
