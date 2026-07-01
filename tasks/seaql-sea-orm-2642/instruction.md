Implement a blanket trait `PartialModelTrait` for any type that implements both `ModelTrait` and `FromQueryResult`. This will allow full entity model types to be used directly with `into_partial_model()` and as nested fields in partial model structs. Update the system to support these functionalities without requiring redundant wrapper structs.

*   Implement `PartialModelTrait` in `src/entity/partial_model.rs` for types `T` that satisfy `ModelTrait + FromQueryResult`.
    *   Ensure it allows the use of full entity model types directly as the target type for partial-model queries.
*   Define the method `select_cols<S: SelectColumns>(select: S) -> S`.
    *   Delegate to `Self::select_cols_nested(select, None)`.
    *   Ensure it selects all columns of the entity without aliasing.
*   Define the method `select_cols_nested<S: SelectColumns>(mut select: S, prefix: Option<&str>) -> S`.
    *   If `prefix` is `Some(p)`, iterate all columns and select each with an alias formed by `format!("{prefix}{}", col.as_str())` using `select_column_as`.
    *   If `prefix` is `None`, iterate all columns and select each via `select_column(col)`.
*   Allow a partial model struct with the `from_query_result` attribute to declare a field of type `Option<M>` (where `M` is a full entity model) annotated with the `nested` attribute.
    *   Ensure querying with a left join populates that field with the full related model when present, or `None` when the join produces no match.
*   Ensure the blanket `PartialModelTrait` implementation brings the following into scope from the crate root:
    *   `EntityTrait`
    *   `IdenStatic`
    *   `Iterable`
    *   `ModelTrait`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.