## Description

Currently, the core language model interface requires every implementing type to provide a method for counting tokens in a string. This is unnecessarily burdensome because token counting does not need to be a per-model responsibility — it can be handled generically without each model implementation having to duplicate this logic.

## Expected Behavior

- The language model interface should only require methods that are truly core to language model functionality (e.g., calling the model)
- Token counting should not be part of the language model interface contract
- All existing types that implement the interface should not need to provide token counting in order to satisfy the interface
- All chain operations that depend on the language model interface should continue to work correctly after this change

## Why This Matters

Every developer who wants to create a new language model integration currently has to implement token counting as part of the interface contract, even if their use case doesn't require it. Removing this requirement makes the interface leaner and easier to implement, reducing the barrier to writing new integrations. Token counting can be provided as a standalone utility function instead, decoupling it from the interface.
