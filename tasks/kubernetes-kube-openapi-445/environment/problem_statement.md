## Description

Kubernetes API authors need a way to declare expressive, rule-based validation constraints on their Go types and fields directly in source code comments. Today, the comment-driven schema generation system supports basic constraints like minimum/maximum values, length limits, and patterns — but there is no mechanism to specify more powerful validation expressions that check relationships between fields, enforce immutability, or apply conditional logic.

This means developers must either leave these validations out of the schema entirely or maintain them separately outside of the Go type definition. That makes the constraints harder to discover and keep in sync with the type they apply to.

## Expected Behavior

- Developers should be able to add indexed validation rule entries to Go type and field comments using a structured marker syntax.
- Each validation rule entry can include the rule expression itself, a human-readable error message, an expression-based error message, and an optional flag controlling how the previous value is handled.
- Multiple rules per type or field should be supported, specified as consecutive indexed entries.
- When the schema is generated, these rule annotations should appear as a recognized Kubernetes validation extension on the appropriate schema entries for both the type and any annotated fields.
- The parser should reject malformed annotations — such as non-consecutive indices, duplicate keys, or type-incompatible values — with clear, descriptive error messages.
- Validation constraint errors (type mismatches, out-of-range values, etc.) should be surfaced directly from the comment parsing step rather than requiring a separate validation call.

## Why This Matters

Being able to co-locate expressive validation rules with the type definition makes the schema self-documenting and reduces the chance of drift between the Go type and its validation logic. Kubernetes controllers and admission webhooks rely on generated schemas to enforce correctness, so enriching the schema with these rule-based constraints at generation time is a significant improvement for API authors.
