## Description

When process events and messages are exchanged between distributed actors in the process runtime, they carry data payloads whose concrete types are only known at runtime. The current serialization mechanism, based on the .NET data contract system, requires all payload types to be declared statically in advance. This makes it impossible to correctly round-trip events and messages whose data fields hold arbitrary types — including primitive values, arrays, user-defined objects, and structured error information.

When an event is serialized and then deserialized at the receiving actor, the original type of the data payload is lost. This causes errors or incorrect behavior when downstream steps try to process the data.

## Expected Behavior

- Serializing an event or message to a transferable representation and then deserializing it should fully restore the original data, including its concrete type.
- The solution should handle at minimum: integers, strings, GUIDs, arrays, user-defined complex objects, and error objects created from exceptions.
- A list containing events or messages with different payload types should round-trip correctly in a single pass.
- The approach should not require pre-registration of payload types.

## Why This Matters

The process runtime relies on distributed actors passing events and messages to each other. Losing type fidelity during transfer corrupts the data pipeline and causes failures in downstream process steps. A robust serialization layer that preserves type information is essential for the reliability of the distributed process runtime.
