Implement a new error detection feature in the Noir compiler to ensure trait implementations do not introduce stricter constraints than specified in the original trait methods. Emit a specific error when such violations occur, detailing the constraint type and trait involved.

*   Implement a new enum variant `ImplIsStricterThanTrait` in `DefCollectorErrorKind` within `compiler/noirc_frontend/src/hir/def_collector/errors.rs`.
    *   Ensure it is a struct-like variant with the following fields:
        *   `constraint_typ: crate::Type` - Represents the improperly constrained type. Its `to_string()` method must yield the full type name.
        *   `constraint_name: String` - The name of the trait applied as the extra constraint.
        *   `constraint_generics: Vec<crate::Type>` - The generic arguments of the constraint trait, with the first element's `to_string()` yielding the first generic argument.
        *   `constraint_span: Span` - The source location of the constraint.
        *   `trait_method_name: String` - The name of the original trait method.
        *   `trait_method_span: Span` - The source location of the trait method definition.

*   Ensure the compiler emits one `ImplIsStricterThanTrait` error for each extraneous constraint in a trait implementation method's where clause that is not present in the corresponding trait method.
    *   Include the actual type being constrained, the trait name, and the generic arguments in the error details.
    *   Raise the error regardless of whether the trait method itself had any constraints if a method-local generic is improperly constrained.
    *   Do not emit errors for methods that correctly mirror the trait method's constraints with renamed generics.

*   Allow constraints placed on the implementation block itself and ensure they do not trigger `ImplIsStricterThanTrait` errors.
    *   If these block-level constraints are not satisfied at a call site, emit a `CompilationError::TypeError(TypeCheckError::NoMatchingImplFound)` error instead.

*   Ensure the number of `ImplIsStricterThanTrait` errors matches the number of methods introducing extraneous constraints, excluding methods with correct constraints and those inheriting from the impl block.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.