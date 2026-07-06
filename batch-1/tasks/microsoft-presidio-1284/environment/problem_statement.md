## Description

The anonymizer and deanonymizer engines currently only support a fixed set of built-in operators. There is no way to register custom operators at runtime without modifying the library. Additionally, the operator registry uses class-level shared state, meaning all engine instances share the same operator set and cannot be independently configured.

We need to be able to plug in custom anonymization and de-anonymization logic — for example, a pseudonymization strategy that replaces each detected entity with a unique, numbered identifier (such as a label combining the entity type with a sequential count) and can later reverse that replacement.

## Expected Behavior

- Developers should be able to add their own operator classes to an engine instance at runtime, and those operators should be immediately available for use by name.
- Developers should be able to remove built-in or previously registered operators from an engine instance.
- Attempting to remove an operator that is not registered should raise a clear error indicating the operator was not found.
- Each engine instance should maintain its own independent registry so that customizing one engine does not affect others.
- Custom operators should receive the current entity type automatically as part of their parameters, both during validation and during execution.

## Why This Matters

Without this capability, developers who need custom anonymization strategies — such as consistent pseudonymization, entity counting, or domain-specific redaction — are forced to fork or patch the library. Runtime extensibility makes Presidio usable in a much wider range of production scenarios.
