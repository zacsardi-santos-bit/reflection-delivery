## Description

Active Job currently provides no built-in mechanism for declaring typed, persistent attributes on a job class. Developers who need to track and accumulate state across retries or across the resume cycles of a long-running iterating job are forced to manage serialization manually, which is repetitive and error-prone.

## Expected Behavior

A new opt-in module should be available that job authors can include to declare named fields with types and default values on their job classes:

- Declared fields should behave like typed accessors: assigning a value applies automatic type coercion (e.g. a numeric string assigned to an integer field should be stored as an integer, and "0" assigned to a boolean field should be stored as false).
- Field values should be automatically included in the job's serialized form and correctly restored during deserialization, so the full set of declared values survives a serialize/deserialize round-trip.
- If deserialized data contains fields that are not declared on the job class, they should be silently ignored rather than causing an error.
- If deserialized data is missing the attributes payload entirely, declared fields should fall back to their default values without error.
- Because jobs are serialized before re-enqueueing on retry, attribute mutations made during a failed attempt must carry forward into the next attempt automatically.
- Jobs that also accept keyword arguments in their perform method must continue to work correctly when this module is included.
- Iterating jobs that support mid-execution interruption and resumption should be able to declare attributes and have those values persist correctly across multiple interrupt/resume cycles.

## Why This Matters

Without this feature, any stateful job pattern requires custom serialization boilerplate. A standardized attribute declaration system makes stateful jobs much easier to write, read, and maintain, and ensures that state is handled correctly in all the lifecycle scenarios Active Job supports (retries, continuations, etc.).
