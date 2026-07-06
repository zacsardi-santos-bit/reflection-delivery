## Description

When generating WebAssembly component bindings, there are two related issues that prevent certain valid binding configurations from working correctly.

First, host resource drop handlers in generated code return results directly. This works fine for the standard result type, but breaks down when developers use the flexible error handling mode — because the drop handler's return type no longer matches what the runtime expects. Developers using the flexible error handling mode should be able to implement resource drop methods that return either the standard result type or the extended result type used in that mode, and the generated glue should handle the conversion transparently.

Second, using the flexible error handling mode alongside custom error type mappings in the same component binding definition is currently not possible — the two options are mutually exclusive due to how they interact in the code generator. This forces developers to choose between flexible error propagation and custom error type remapping, even though both are useful together.

## Expected Behavior

- Resource drop handlers in generated bindings should work correctly regardless of whether the flexible error handling mode is enabled, by using a uniform result-conversion wrapper.
- The component binding macro should accept and correctly generate code when both the flexible error handling mode and custom error type mappings are specified together in the same binding definition.

## Why This Matters

Developers working with host-implemented resources need to be able to use the full range of error handling options without hitting artificial limitations in the binding generator. Without these fixes, enabling the flexible error mode breaks resource cleanup, and pairing it with custom error types causes a compilation failure.
