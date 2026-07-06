## Description

When saving and loading model files that include configuration objects — such as training argument classes or structured data container configs — the secure deserialization layer rejects these objects even when they are completely safe. This is overly restrictive: simple user-defined classes that don't override any serialization hooks cannot be exploited for code execution, yet they are blocked by the current whitelist-only approach.

Additionally, the utility that traverses loaded objects tries to iterate into structured configuration objects, which can corrupt or discard them instead of leaving them intact.

## Expected Behavior

- User-defined classes, including structured data container classes, that do not define any dangerous serialization methods should be loadable when they were previously saved alongside model weights.
- Classes that do define such dangerous methods should still be blocked, and the resulting error should clearly identify that the problematic method is the cause.
- When traversing a loaded object graph, structured configuration objects should be passed through as-is without the traversal logic attempting to iterate into them.
- This safe/unsafe determination should be exposed as a standalone utility that can be called on any class to check whether it is safe to deserialize.

## Why This Matters

Many real-world workflows save model configuration alongside weights in a single file. Blocking simple config classes breaks these workflows unnecessarily. The fix allows safe configuration classes to load correctly while preserving all existing security guarantees against malicious payloads.
