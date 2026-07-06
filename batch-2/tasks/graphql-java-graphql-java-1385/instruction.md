Implement a transformation mode in the query traversal API that allows programmatic modification of GraphQL query structures. Enable visitors to rename, delete, or add fields and fragments, and return a modified document or fragment.

*   Update `QueryTraversal` class:
    *   Implement `public Node transform(QueryVisitor queryVisitor)` method.
        *   Traverse the query in pre-order.
        *   Use `TreeTransformerUtil` methods (`changeNode`, `deleteNode`, `changeParentNode`) inside visitor callbacks.
        *   Ensure the method requires exactly one root node; throw `IllegalArgumentException` if multiple roots are present.
        *   When the root is a document, do not traverse named fragment bodies; only visit fragment spreads.
        *   When the root is a `FragmentDefinition`, traverse and return the modified fragment.

*   Update `QueryVisitorInlineFragmentEnvironment` interface:
    *   Add `TraverserContext<Node> getTraverserContext()` method.

*   Update `QueryVisitorFragmentSpreadEnvironment` interface:
    *   Add `TraverserContext<Node> getTraverserContext()` method.

*   Modify `QueryVisitorFragmentSpreadEnvironmentImpl` class:
    *   Constructor must accept `FragmentSpread`, `FragmentDefinition`, and `TraverserContext<Node>`.
    *   Implement `getTraverserContext()` to return the provided context.

*   Modify `QueryVisitorInlineFragmentEnvironmentImpl` class:
    *   Constructor must accept `InlineFragment` and `TraverserContext<Node>`.
    *   Implement `getTraverserContext()` to return the provided context.

*   Ensure field nodes can be renamed using `changeNode` in `visitField`; reflect renames across all occurrences.
*   Allow deletion of field nodes using `deleteNode` in `visitField`; ensure resulting document omits these fields.
*   Allow deletion of inline fragments and fragment spreads using `deleteNode` in `visitInlineFragment` or `visitFragmentSpread`.
*   Enable modification of parent nodes (e.g., adding sibling fields) using `changeParentNode` in `visitField`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.