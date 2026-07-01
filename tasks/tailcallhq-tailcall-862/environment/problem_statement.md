## Description

We need a way to compose multiple resolver types into a single, conditional expression on a field. Right now, every field can only have one resolver — you pick HTTP, gRPC, GraphQL, or a constant value. There's no way to say "use this resolver if condition X is true, otherwise use that one."

This is blocking use cases where the data source to use depends on some runtime condition. For example, routing to different endpoints based on query parameters or calculated values.

## Expected Behavior

- A new field-level directive should accept an expression that supports the existing resolver types (HTTP, gRPC, GraphQL, constant) and a new conditional branching variant.
- The conditional variant requires three sub-expressions: a condition, a branch to evaluate when the condition is truthy, and a branch to evaluate when it is falsy. At runtime, it evaluates the condition and returns the result of the appropriate branch.
- If the directive is misconfigured — for example, if the required expression argument is missing, if an unknown variant is used, or if any of the three required sub-fields of the conditional are absent — validation should produce a clear, structured error message indicating exactly what is wrong and where in the schema.
- Fields using this directive should be recognized as having resolvers (not flagged as unresolved).
- The server schema should serialize and deserialize this directive in a round-trip-safe way.
- The client-facing schema should not expose the internal directive — those fields should appear as plain fields to consumers.

## Why This Matters

Without conditional branching in expressions, schema authors must work around the limitation by splitting logic into multiple fields or using upstream logic. A composable expression system makes the schema itself the source of truth for routing and conditional data fetching, reducing external complexity.
