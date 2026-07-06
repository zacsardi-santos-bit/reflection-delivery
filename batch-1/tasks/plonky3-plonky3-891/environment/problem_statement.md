## Description

The FRI protocol in this library uses a type called "config" to hold the mathematical and security properties of the protocol (blowup factor, number of queries, proof-of-work bits, etc.). However, these values are not really software configuration — they are cryptographic **parameters** that define the protocol itself. Using the word "config" is misleading and inconsistent with how the broader cryptography community refers to these values.

Similarly, there is a generic strategy type used internally by PCS implementations to abstract over FRI folding behavior. Its current name implies it is a generic configuration type, which is also inaccurate since it describes a folding strategy, not a configuration.

## Expected Behavior

- The main FRI protocol type should be renamed to reflect that it holds **parameters** rather than configuration.
- The associated trait that PCS implementations use to plug in their folding logic should be renamed to reflect that it defines a **folding strategy**.
- The concrete two-adic implementation of the folding strategy should be renamed to match the new naming convention.
- All public helper functions that create instances of this type for testing and benchmarking should be updated to use the new name.
- Struct fields throughout the codebase that hold this type should be renamed from the old naming convention to the new one.

## Why This Matters

Using "parameters" instead of "config" makes the API more accurate and consistent with cryptographic literature. Developers integrating this library will encounter clearer, more self-documenting type names that better communicate the role of each type. The renaming affects the public API and all callers must update their code accordingly.
