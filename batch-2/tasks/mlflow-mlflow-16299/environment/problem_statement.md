## Description

The TypeScript package for MLflow is missing the core entity classes and utility functions needed to work with tracing spans. Without these, developers cannot create, manipulate, or serialize traced operations in TypeScript — meaning there is no way to represent the inputs, outputs, timing, status, and events of a traced function call.

## Expected Behavior

- There should be a factory function to create span objects from active telemetry contexts. The function should handle active spans (which can be mutated), completed spans (which are read-only), as well as a no-operation fallback when no span context is available.
- Live spans should allow setting inputs, outputs, arbitrary attributes, status, and events. Completed spans should expose those same values for reading, but without any mutation methods.
- Span objects should be serializable to and from a standard JSON format compatible with the MLflow backend. Serialization should preserve timing information using high-precision nanosecond timestamps as exact integers.
- There should be a span event entity for attaching named events (including exceptions) to a span, including attribute support for string, number, boolean, and array values.
- There should be a span status entity supporting three states, with conversion to the underlying telemetry library's status format.
- There should be utility functions for converting between the high-resolution time format used by the telemetry library and nanosecond integers, as well as for encoding and decoding span and trace identifiers between hexadecimal and base64 formats.

## Why This Matters

MLflow's tracing features need a TypeScript implementation to match what's available in Python. These core entities and utilities are the foundation for any higher-level tracing functionality in TypeScript — without them, tracing cannot be implemented or tested. They also need to interoperate correctly with both the OpenTelemetry library and the MLflow backend API's wire format.
