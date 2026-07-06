I'm working on improving error handling in the field index filtering layer.

*   The `filter` method defined in the `PayloadFieldIndex` trait (in `lib/segment/src/index/field_index/field_index_base.rs`) must change its return type from `Option<Box<dyn Iterator<Item = PointOffsetType> + 'a>>` to `OperationResult<Option<Box<dyn Iterator<Item = PointOffsetType> + 'a>>>`.

*   The `filter` implementation on `NumericIndexInner<T>` (in `lib/segment/src/index/field_index/numeric_index/mod.rs`) must return `OperationResult<Option<...>>`: return `Ok(None)` when the condition does not apply to this index type, and `Ok(Some(iterator))` when matching point offsets are available.

*   All other types implementing the `PayloadFieldIndex` trait must also update their `filter` method signatures to return `OperationResult<Option<Box<dyn Iterator<Item = PointOffsetType> + 'a>>>` to satisfy the updated trait definition — otherwise the codebase will not compile.

*   Callers of `filter` on numeric index types must handle the two-level result: first unwrapping the `OperationResult` (error layer), then unwrapping the `Option` (applicability layer), before consuming the iterator of matching point offsets.


*   Interface details: Type: Trait Method
Name: filter
Location: lib/segment/src/index/field_index/field_index_base.rs
Signature: fn filter<'a>(&'a self, condition: &'a FieldCondition, hw_counter: &'a HardwareCounterCell) -> OperationResult<Option<Box<dyn Iterator<Item = PointOffsetType> + 'a>>>
Description: Returns matching point offsets for a given field condition, wrapped in an OperationResult. Returns Ok(None) if the condition type does not apply to this index. Returns Ok(Some(iterator)) if the condition applies and matching point offsets are available.

Type: Method Implementation
Name: filter (on NumericIndexInner<T>)
Location: lib/segment/src/index/field_index/numeric_index/mod.rs
Signature: fn filter<'a>(&'a self, condition: &FieldCondition, hw_counter: &'a HardwareCounterCell) -> OperationResult<Option<Box<dyn Iterator<Item = PointOffsetType> + 'a>>>
Description: Implementation of PayloadFieldIndex::filter for the numeric index. Must return Ok(None) when no range condition is present (and the string UUID match doesn't apply), Ok(Some(Box::new(std::iter::empty()))) when boundary checks fail, and Ok(Some(iterator)) for valid range queries.

Type: Method Implementation
Name: filter (on FieldIndex)
Location: lib/segment/src/index/field_index/field_index_base.rs
Signature: fn filter<'a>(&'a self, condition: &'a FieldCondition, hw_counter: &'a HardwareCounterCell) -> OperationResult<Option<Box<dyn Iterator<Item = PointOffsetType> + 'a>>>
Description: Delegates to the inner PayloadFieldIndex implementation's filter method. Must return OperationResult<Option<...>> matching the updated trait signature.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.