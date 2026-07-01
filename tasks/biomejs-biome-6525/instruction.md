Improve the type inference system to ensure that function and method return types are inferred from the function body when no explicit annotation is provided. Enhance the floating promise detection lint rule to catch asynchronous return values from unannotated methods.

*   Update the noFloatingPromises lint rule:
    *   Detect floating Promises when a method returns a Promise inferred from the function body without an annotation and the call site does not handle it with await, .catch, .then, or the void operator.

*   Implement return type inference for unannotated functions:
    *   Infer `void` for functions with no return statements.
    *   Infer the type of the returned expression for functions with a single return statement.
    *   Infer a union of all distinct return types for functions with multiple return statements, ensuring deduplication.

*   Modify the type system:
    *   Register a dedicated void TypeId entry for functions inferred to return void, displayed as 'void' in snapshots.
    *   Ensure Promise instance types track their type parameter, producing 'instanceof Promise<T>' where T is the resolved inner type.
    *   Represent the null literal type as TypeData::Null, changing snapshot display from 'value: null' to 'null'.
    *   Implement `Type::is_null()` to return true only if the type is TypeData::Null.
    *   Implement `Type::is_string_literal(value)` to return true only if the type is a string literal matching the given value.
    *   Make `Type::resolved_data()` a public method returning `Option<ResolvedTypeData>`.

*   Update TypeData::Union:
    *   Ensure the inner Union struct exposes a `types()` method returning deduplicated type references.

*   Adjust biome_js_type_info snapshots:
    *   Reflect the new void TypeId registration by adding a TypeId entry for void before the arrow function's TypeId, shifting all subsequent TypeIds by one.

*   Ensure the lint test fixture `09_invalid.ts` triggers the noFloatingPromises diagnostic:
    *   The test must involve a TypeScript snippet where an object method returns a Promise (inferred, no annotation) and is called without proper handling.
    *   The corresponding snapshot `09_invalid.ts.snap` must contain one diagnostic at line 6, column 1, with the specified message text.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.