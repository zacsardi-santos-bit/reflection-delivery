Implement a feature in rust-analyzer to display object safety information when hovering over a trait. Ensure the hover documentation indicates whether a trait is object-safe and, if not, provides specific reasons for its lack of object safety. Enhance the analysis layer to identify and enumerate all object safety violations for a given trait.

*   Define the `object_safety_with_callback` function in `crates/hir-ty/src/object_safety.rs`:
    *   Accepts a `HirDatabase` reference, a `TraitId`, and a mutable callback of type `FnMut(ObjectSafetyViolation) -> ControlFlow<()>`.
    *   Invokes the callback for each object safety violation found and returns `ControlFlow<()>`.

*   Define the `ObjectSafetyViolation` enum in `crates/hir-ty/src/object_safety.rs`:
    *   Variants: `SizedSelf`, `SelfReferential`, `Method(FunctionId, MethodViolationCode)`, `AssocConst(ConstId)`, `GAT(TypeAliasId)`, `HasNonSafeSuperTrait(TraitId)`.
    *   Implement `Debug`, `Clone`, `PartialEq`, `Eq`, and `Hash`.

*   Define the `MethodViolationCode` enum in `crates/hir-ty/src/object_safety.rs`:
    *   Variants: `StaticMethod`, `ReferencesSelfInput`, `ReferencesSelfOutput`, `Generic`, `UndispatchableReceiver`.
    *   Implement `Debug`, `Clone`, `PartialEq`, `Eq`, and `Hash`.

*   Ensure `object_safety_with_callback` reports:
    *   `ObjectSafetyViolation::SizedSelf` for traits requiring `Self: Sized`.
    *   `ObjectSafetyViolation::SelfReferential` for associated types or supertraits referencing `Self`.
    *   `ObjectSafetyViolation::AssocConst` for traits with associated constants, unless `where Self: Sized`.
    *   `ObjectSafetyViolation::GAT` for traits with generic associated types.
    *   `ObjectSafetyViolation::HasNonSafeSuperTrait` for non-object-safe supertraits.
    *   Method-level violations unless `where Self: Sized` is present.

*   Exemptions and special cases:
    *   Methods with `where Self: Sized` are exempt from method-level violations.
    *   By-value `self` parameters and methods with only lifetime generics are object-safe.

*   Update hover documentation:
    *   Include `// Object Safety: Yes` for object-safe traits.
    *   Include `// Object Safety: No` with detailed reasons for non-object-safe traits.

*   Ensure tests using `dyn Trait` with coercion or function traits include `dispatch_from_dyn` in minicore directives.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.