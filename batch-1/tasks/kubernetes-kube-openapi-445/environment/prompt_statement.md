I'm working on a Go-based OpenAPI schema generator for Kubernetes API types. Right now, developers can annotate their Go struct fields and types with comment markers to specify basic validation constraints — things like numeric bounds, string length limits, or patterns — and the generator picks these up and embeds them in the generated schema.

What I need is support for a more expressive, rule-based validation system. Specifically, I want to be able to write indexed validation rule entries directly in Go comments on a type or field, where each rule entry has a rule expression and optionally a human-readable message, a message expression, and a flag for how the old value is handled. Multiple rules on a single type or field should be expressed as consecutive indexed entries (starting at 0), and the parser should enforce that these indices appear in order without gaps.

When the schema is generated, these rule annotations should appear as a standard Kubernetes validation extension on the corresponding schema entries — both at the type level and at the individual field level.

In addition, the error messages from the comment parser need to be updated to be more precise and consistent. Errors for duplicate keys and invalid values should include better context. Type-constraint validation errors (like using a numeric constraint on a string type) should be returned directly from the parsing step rather than through a separate validation method.

The integration test golden files and the generated code for test types also need to be updated to reflect that CEL annotations on those types produce the expected validation extension in the output schema.
