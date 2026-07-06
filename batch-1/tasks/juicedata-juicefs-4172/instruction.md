Update the project's module dependency files to include the missing property-based testing library. Ensure that the metadata package can compile and all existing tests can run successfully.

*   Modify the `go.mod` file:
    *   Add 'pgregory.net/rapid v0.5.3' as a direct dependency in the require block.
*   Update the `go.sum` file:
    *   Include the hash entries for 'pgregory.net/rapid v0.5.3':
        *   'pgregory.net/rapid v0.5.3 h1:163N50IHFqr1phZens4FQOdPgfJscR7a562mjQqeo4M='
        *   'pgregory.net/rapid v0.5.3/go.mod h1:PY5XlDGj0+V1FCq0o192FdRhpKHGTRIWBgqjDBTrq04='
*   Ensure the metadata package located in `pkg/meta` compiles without errors.
*   Verify that all pre-existing tests in the metadata package compile and run successfully after the dependency is added.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.