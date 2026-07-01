Implement changes to ensure consistent use of fully qualified .NET type names in the C# frontend for Joern's code property graph (CPG). Address issues with nullable and generic types, and simplify the API for processing AST generator results.

*   Update CPG call nodes:
    *   Use fully qualified .NET type names for methodFullName and typeFullName fields (e.g., 'System.Int32' instead of 'int').
*   Update CPG method nodes:
    *   Use fully qualified .NET type names for the fullName and signature fields, particularly for return types.
*   Ensure method return nodes:
    *   Set typeFullName to fully qualified .NET type names.
*   Handle nullable types:
    *   Set identifier nodes' typeFullName without the nullable marker (e.g., 'int?' becomes 'System.Int32').
*   Handle generic collection types:
    *   Strip generic type parameters from typeFullName (e.g., 'List<int>' becomes 'List').
*   Modify the `processAstGenRunnerResults` method:
    *   Accept only `parsedFiles: List[String]` and `config: Config` as parameters.
    *   Remove the TypeMap parameter; resolve types internally.
*   Resolve inherited method calls:
    *   Ensure call nodes without explicit receivers resolve to the correct parent class method with fully qualified names.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.