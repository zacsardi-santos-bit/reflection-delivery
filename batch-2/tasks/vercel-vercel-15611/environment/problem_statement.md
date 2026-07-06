## Description

The Vercel Workers Python SDK currently fails when developers try to include common Python types — such as unique identifiers, timestamps, and decimal numbers — directly in queue message payloads. The SDK's JSON serialization step raises an error for these types instead of converting them to natural JSON equivalents.

This means developers must manually convert all values before enqueueing a job, which is error-prone and adds unnecessary boilerplate. Similarly, the Django task backend has the same problem: passing these types as task arguments or keyword arguments causes the enqueue call to fail with a serialization error.

## Expected Behavior

- The SDK should handle these common Python types automatically when serializing a queue message payload, converting them to appropriate JSON values (strings for identifiers and timestamps, numbers for decimals).
- Developers should also be able to provide a custom encoder class to extend the default behavior and support additional types.
- The Django backend should serialize task arguments and keyword arguments containing these types correctly without requiring manual conversion.

## Why This Matters

Developers working with real-world data almost always deal with unique identifiers, datetimes, and decimal numbers. Forcing them to manually serialize these before each enqueue call creates unnecessary friction and potential bugs. The SDK should handle this transparently so developers can pass native Python objects directly.
