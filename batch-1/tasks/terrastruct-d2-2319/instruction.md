Implement the `DeleteEdge` method in `d2ir/d2ir.go` for the `Map` type to correctly handle the deletion of connections between nodes with multi-level paths. Ensure that connections can be removed using both absolute and parent-scoped syntax.

*   Update the `DeleteEdge` method signature to:
    *   `(m *Map) DeleteEdge(eid *EdgeID) *Edge`
    *   Ensure it deletes the edge matching the given `EdgeID` from the `Map`.
*   Ensure the method:
    *   Resolves `EdgeID`s that contain a common ancestor path by locating the appropriate nested sub-map field.
    *   Recursively delegates deletion to the correct sub-map rather than only searching the top-level edges.
    *   Handles `EdgeID`s specified with absolute paths for both source and destination.
*   Ensure that when two identical connections between deeply nested nodes are both assigned null using indexed edge reference syntax:
    *   One using absolute path syntax.
    *   One using parent-scoped syntax.
    *   The compiled graph contains the expected nodes but zero edges.
*   Verify the existence and correctness of golden test data files:
    *   `testdata/d2compiler/TestCompile2/nulls/basic/basic-edge.exp.json` must match the expected compiled output for a simple connection that is declared once and then nulled out by index.
    *   `testdata/d2compiler/TestCompile2/nulls/basic/nested-edge.exp.json` must match the expected compiled output for two identical nested connections nulled out by index using different reference syntax variants, resulting in a graph with 5 objects and 0 edges.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.