## Description

When generating a custom resource definition from a composite resource definition, the system currently does not validate that the schema validation field is present for each version. If a developer accidentally omits this field (leaving it nil), the code may panic or produce confusing undefined behavior instead of returning a clear, informative error.

We need the system to detect this condition early and return a descriptive error that tells the developer exactly what went wrong and which resource definition was affected.

## Expected Behavior

- When attempting to generate a custom resource definition from a composite resource definition whose version has a nil schema validation, the system should return a clear error message rather than crashing or producing incorrect output.
- The error should identify both the type of resource (Composite Resource) and the name of the affected definition.
- When schema validation is properly configured — including with a minimal empty schema — the system should continue to work as expected.

## Why This Matters

Platform engineers configuring composite resource definitions may accidentally leave out the required schema validation field. Without proper nil-checking, this leads to cryptic panics or silent failures. With this change, they receive an actionable error message that clearly identifies the problem and the affected resource, making misconfiguration much easier to diagnose and fix.
