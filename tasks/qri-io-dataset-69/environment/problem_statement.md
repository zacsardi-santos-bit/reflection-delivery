## Description

Several of the dataset's core data types can be serialized to JSON in two different ways: either as a full JSON object (with all their fields), or as a plain string (containing just a path reference) when the object has a stored path but no other populated fields. This dual behavior is causing issues for code that needs to always receive a structured object and cannot handle both formats.

We need a way to force serialization to always produce a JSON object for these types — regardless of whether an internal path reference is set. Without this, callers must add special-case handling whenever they consume serialized data, since they can't rely on always getting back an object they can traverse.

## Expected Behavior

- Each relevant data type should expose a dedicated method that always serializes it as a JSON object, even when a path reference is set and all other fields are empty.
- The standard JSON serialization for these types should be updated to delegate to this object-serialization method in the non-path cases, so that the output is always a parseable JSON object.
- The resulting JSON from either method should be deserializable into a generic key-value map structure without errors.

## Why This Matters

Code that consumes these serialized types needs to reliably work with the result as a structured object. The current behavior, where a type might serialize to a plain string, makes it harder to write consistent downstream logic and leads to fragile special-case handling.
