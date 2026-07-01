## Description

The distributed task queue system is missing a standard, reusable mechanism for converting arbitrary Go data structures into the wire format required by the queue, and for recovering typed results after tasks finish executing.

Currently, developers working with the task queue have no shared utilities for this conversion, which means each caller must roll their own serialization logic — leading to duplication, inconsistency, and bugs when structured data is passed through the queue or retrieved from it.

## Expected Behavior

- A function should exist that accepts any Go value and converts it into a task argument in the format expected by the underlying queue library. The result should be a single-element collection containing the encoded form of the input.
- A complementary function should exist that takes the raw task result values (as reflected Go values) and deserializes the first element back into a typed Go destination. It should return an error if more than one result element is present, since only a single encoded string is expected.
- Together, these two functions should guarantee round-trip fidelity: a value passed through marshal then unmarshal should be identical to the original.

## Why This Matters

Without these utilities, consuming code must implement its own serialization and deserialization at every call site, making the task queue integration fragile and hard to maintain. Centralizing this logic ensures correctness and makes it straightforward to add new task types.
