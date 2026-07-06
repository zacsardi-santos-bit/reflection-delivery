# Test Isolation, Name Validation, and Developer Ergonomics for IR Operators

## Description

The vLLM IR operator test suite has a reliability problem: all tests share a single globally registered set of custom operators. Because PyTorch holds operator registrations for the entire process lifetime, tests can interfere with each other, and reusing the same operator name in different test runs can even crash the process. We need each test to have its own isolated operator registry so that tests can register and deregister operators independently.

Beyond test isolation, there are two missing features that would help developers working with IR operators:

1. **Name validation**: Operator names and provider names are currently accepted without validation, so a name using uppercase letters, hyphens, or starting with a digit passes silently. Names should follow a strict lowercase convention, and violations should produce a clear error.

2. **Registration provenance**: When debugging an issue with a registered operator, there is no record of where in the source code the operator (or its implementation) was registered. Each operator and implementation should capture a stack trace at registration time so developers can trace the origin.

3. **Human-readable representation**: IR operators currently have no meaningful string representation, making them hard to identify in logs and debug output. Operators should have a compact machine-readable representation that includes their name, and a human-readable form that incorporates the first line of their docstring when one is present.

## Expected Behavior

- Each test that registers IR operators should work against its own clean, isolated registry; registrations from one test must not affect another.
- Attempting to register an operator or implementation with a name that contains uppercase letters, hyphens, or that starts with a digit should raise an error clearly indicating the name is invalid.
- After registration, each operator and each implementation should expose a traceback attribute containing a stack trace pointing to the user code that performed the registration, with internal framework frames removed from the end.
- The compact machine-readable form of an operator should return a short identifier string.
- The human-readable form of an operator should include the first line of its docstring when available, or fall back to the same compact format.

## Why This Matters

Without test isolation, the test suite is fragile and order-dependent. Without name validation, bad operator names can silently create confusingly named ops. Without stack traces, debugging registration issues requires manual code search. Without string representations, operators are opaque objects in logs and debug sessions.
