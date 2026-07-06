I'm working on the C# frontend for Joern and running into inconsistencies in how type names are stored in the generated code property graph. Primitive types like the integer and string types are being stored using their short-form C# aliases rather than their fully qualified .NET names, while parameter types in the same nodes are already using fully qualified names. This creates a mismatch that makes it hard to write reliable queries.

I'd also like to fix a couple of related issues: nullable types (those declared with a "?" annotation) are including the nullable marker in the recorded type name, making it harder to match types regardless of nullability. Generic collection types are including their type parameters in the type name, which should also be stripped.

There's also a problem with how method calls to inherited methods are resolved when there's no explicit receiver — the class hierarchy was being consulted incorrectly, leading to the wrong fully qualified method name being assigned in the call graph.

Finally, the API for processing AST generator results currently requires callers to construct and pass a separate type-mapping object as an explicit argument. I'd like to remove that dependency so callers only need to provide the list of parsed files and the configuration, with type resolution handled internally.
