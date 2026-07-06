## Description

Several dataset component types maintain an internal path field that identifies where the data lives in storage. However, none of these types expose a public method for setting this path from a plain string. As a result, callers that need to programmatically assign a path to a component after construction have no clean, supported way to do so.

## Expected Behavior

Each major dataset component type should expose a dedicated method for setting its internal path from a string value. This method should:

- Accept a single string argument representing the desired path
- When given an empty string, reset the internal path to its zero value
- When given a non-empty string, construct and store the appropriate key from that string

## Why This Matters

Without this capability, code that needs to update or assign the internal path of a dataset component (such as after loading it or resolving references) must rely on workarounds or internal struct manipulation. A first-class method makes the intent explicit, documents that this operation should be used with care, and provides a consistent pattern across all the relevant types in the library.
