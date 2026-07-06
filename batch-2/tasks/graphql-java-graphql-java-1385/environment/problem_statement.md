## Description

The query traversal system currently only supports read-only analysis — developers can inspect query fields, fragments, and types, but there is no way to programmatically modify the query structure and get back a transformed document. This makes it impossible to build features like query rewriting, field aliasing, or pre-processing pipelines that need to alter the query AST before execution.

## Expected Behavior

- The traversal API should support a transformation mode that allows a visitor to rename fields, delete fields, delete inline fragments, delete fragment spreads, or add new sibling fields — and receive a new, modified document as output.
- Visitor callbacks for inline fragments and fragment spreads should expose the traversal context, so that the same transformation utilities available in field visitors can also be used in those callbacks.
- When performing a transformation over a full document, the traversal should visit fragment spreads but should not descend into the bodies of named fragment definitions — named fragments should only be traversable when explicitly supplied as the traversal root.
- When a fragment definition is used as the traversal root (rather than a full document), the transformation should traverse and return the modified fragment.

## Why This Matters

Query rewriting and AST transformation are common needs for middleware, instrumentation, and schema federation layers. Without a built-in transformation API, developers must implement custom tree-walking logic, which is error-prone and bypasses the schema-aware visitor machinery already present in the library.
