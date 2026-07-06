## Description

When hovering over a trait in rust-analyzer, the IDE provides documentation including the trait's definition. However, it currently has no concept of object safety — the property that determines whether a trait can be used as a dynamic dispatch target (e.g., as a dynamic trait object). Developers frequently encounter compiler errors when trying to use a trait as a trait object, but rust-analyzer offers no help in understanding whether a trait supports this or why it might not.

## Expected Behavior

- When hovering over an object-safe trait, the hover documentation should indicate that the trait is safe for dynamic dispatch.
- When hovering over a trait that is **not** object-safe, the hover documentation should indicate that dynamic dispatch is not supported, and explain which specific violations are responsible — for example, that a particular method lacks a receiver.
- The underlying analysis should correctly identify all standard object safety violations, including:
  - Traits that require the implementing type to be sized
  - Traits whose associated types reference the implementing type in their bounds
  - Traits with associated constants
  - Traits with generic associated types
  - Traits that have a non-object-safe supertrait
  - Individual method violations: missing a receiver (static methods), generic type parameters, references to the implementing type in parameters or return type, or receivers that cannot be dynamically dispatched

## Why This Matters

Without object safety information in hover, developers must mentally check all the rules themselves or rely on compiler errors after the fact. Surfacing this directly in the IDE makes it immediately obvious whether a trait can be used as a trait object and points to the exact reason if it cannot.
