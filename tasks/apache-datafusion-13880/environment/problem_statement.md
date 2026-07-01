## Description

DataFusion supports user-defined logical plan nodes, allowing developers to extend the query planning system with custom operations. However, when a logical plan containing one of these custom extension nodes is converted back to SQL, the process always fails — there is no mechanism to tell the converter how to handle custom nodes. This makes round-trip SQL conversion impossible for any plan that includes custom extensions.

We need a way for developers to register custom handlers that describe how their extension nodes should be serialized to SQL. There are two scenarios that should be supported:

- A custom node that represents an entire SQL statement on its own, and should be converted directly to a standalone SQL statement.
- A custom node that is used as a source/relation inside a larger query, and should be embedded as a subquery within a containing statement.

## Expected Behavior

- Users can attach custom conversion handlers to the SQL converter when working with plans that contain their own extension nodes.
- When a registered handler recognizes and handles an extension node, the conversion succeeds.
- When multiple handlers are registered, the first one that successfully handles the node is used, and the rest are skipped.
- When no registered handler can handle an extension node, the conversion fails with a clear not-implemented error, preserving the existing behavior for unhandled nodes.
- The internal AST builder types needed to construct SQL subtrees must be accessible to external implementors.

## Why This Matters

Without this feature, any codebase that uses custom logical plan nodes cannot leverage the built-in SQL generation capabilities. This blocks round-trip plan serialization, query export, and any tooling that depends on converting logical plans back to SQL strings.
