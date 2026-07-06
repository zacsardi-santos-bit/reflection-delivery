# Improve dygraph-to-static API Consistency and Usability

## Description

The dynamic-to-static graph compilation API has several inconsistencies and missing capabilities that make it harder to use:

1. **API naming**: The method for enabling or disabling the dynamic-to-static translation has a long, hard-to-remember name. It should be simplified to a shorter, cleaner name.

2. **Namespace accessibility**: Users should be able to access the decorator and program translator class through multiple namespace paths interchangeably. Currently, the program translator class is not exported from the top-level dygraph namespace, meaning users have to know the exact internal module path.

3. **Singleton consistency**: The program translator is a singleton, but when accessed through different namespace paths, it should always return the same object. This equivalence should hold regardless of which namespace path is used.

4. **Inference model saving with multiple outputs**: When a model's forward function returns multiple outputs and the user wants to save an inference model, there is no way to select a subset of outputs. A parameter should be added so users can specify which output indices to save.

5. **Multiple decorator error handling**: When a function is decorated with more than one decorator and the combination is not supported, the system should raise a clear, descriptive error rather than failing silently or producing incorrect behavior.

## Expected Behavior

- The enable/disable method on the program translator should have a shorter, cleaner name
- The decorator and program translator should be accessible via multiple namespace paths (top-level namespace, an alternative submodule path, and the full module path)
- All access paths for the program translator should return the same singleton instance
- Saving an inference model with a multi-output model should support specifying a list of indices to select specific outputs to save
- Applying unsupported combinations of decorators should raise a clear error

## Why This Matters

These improvements make the API more predictable and consistent for users building production workflows. Users can write cleaner code when the API is accessible through natural namespace paths, and the ability to select specific inference outputs is essential for deploying models with complex output structures.
