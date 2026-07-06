Update the Rust codebase to address compilation errors related to missing lifetime parameters in core traits. Implement explicit lifetime parameters for traits dealing with global reference checking and side-effect analysis context, ensuring all implementations are updated accordingly.

*   Update the `IsGlobalReference` trait:
    *   Add a lifetime parameter `'a`, changing the trait to `IsGlobalReference<'a>`.
    *   Ensure the method `is_global_reference` accepts `&IdentifierReference<'a>`.
    *   Modify all implementations to use `impl<'a> IsGlobalReference<'a> for T`.

*   Update the `MayHaveSideEffectsContext` trait:
    *   Add a lifetime parameter `'a`, changing the trait to `MayHaveSideEffectsContext<'a>`.
    *   Ensure it has a supertrait bound `IsGlobalReference<'a>`.
    *   Modify all implementations to use `impl<'a> MayHaveSideEffectsContext<'a> for T`.

*   Update the `MayHaveSideEffects` trait:
    *   Add a lifetime parameter `'a`, changing the trait to `MayHaveSideEffects<'a>`.
    *   Ensure the method `may_have_side_effects` accepts `&impl MayHaveSideEffectsContext<'a>`.
    *   Update all existing implementations for AST node types to use `impl<'a> MayHaveSideEffects<'a> for T<'a>`.

*   Update the `DetermineValueType` trait:
    *   Add a lifetime parameter `'a`, changing the trait to `DetermineValueType<'a>`.
    *   Ensure the method `value_type` accepts `&impl IsGlobalReference<'a>`.
    *   Update all existing implementations to use `impl<'a> DetermineValueType<'a> for T<'a>`.

*   Update the `ConstantEvaluationCtx` trait:
    *   Change the supertrait bound to `MayHaveSideEffectsContext<'a>`.

*   Update the `ConstantEvaluation` trait:
    *   Change the supertrait to `MayHaveSideEffects<'a>`.

*   Update other traits in `crates/oxc_ecmascript/src/`:
    *   Ensure traits like `ToBoolean`, `ToNumber`, `ToJsString`, `ToBigInt`, `ToNumeric`, `ToPrimitive`, `ArrayJoin`, `IsInt32OrUint32` propagate the lifetime parameter consistently using `IsGlobalReference<'a>`.

*   Verify that the entire `oxc_minifier` crate compiles successfully and all 328 tests pass, including those in the `ecmascript` module.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.