Implement a new threshold-based filter condition in Qdrant's filtering system that allows specifying "at least N out of M conditions must match." Ensure this new filter type integrates seamlessly with existing filter types and supports nested and negated contexts.

*   Update the `Filter` struct:
    *   Add an optional field `min_should` of type `Option<MinShould>`, positioned between `should` and `must`.
    *   Ensure all existing `Filter` struct literals include this field, typically set to `None`.

*   Define a new struct `MinShould` in `lib/segment/src/types.rs`:
    *   Include two public fields: `conditions` (a `Vec<Condition>`) and `min_count` (a `usize`).
    *   Ensure `MinShould` is JSON-serializable/deserializable using snake_case field names.
    *   Derive `Debug`, `Deserialize`, `Serialize`, `JsonSchema`, `Validate`, `Clone`, `PartialEq`, `Default`.

*   Implement constructors for `Filter`:
    *   `Filter::new_must(condition: Condition) -> Self` and `Filter::new_must_not(condition: Condition) -> Self` must set `min_should` to `None`.
    *   `Filter::new_min_should(min_should: MinShould) -> Self` creates a `Filter` with `min_should` set and other fields as `None`.

*   Ensure JSON deserialization:
    *   Support deserialization of `Filter` from `{"min_should": {"conditions": [...], "min_count": N}}`.
    *   Ensure `filter.min_should` is `Some(...)` with `conditions.len()` matching the JSON array length.

*   Implement cardinality estimation:
    *   Define `combine_min_should_estimations` in `lib/segment/src/index/query_estimator.rs`.
    *   Compute combined cardinality by forming combinations of `min_count` estimations, intersecting each, and unioning results.
    *   Ensure when `min_count` equals the number of conditions, the estimation matches a `must` filter.

*   Update the search API:
    *   Accept filters with a `min_should` field containing `conditions` and `min_count`.
    *   Return points satisfying at least `min_count` of the listed conditions.
    *   Combine `min_should` with `must` conditions; points must satisfy both to be included.
    *   Support `min_should` inside `must_not` to exclude matching points.
    *   Return HTTP 400 Bad Request if `min_should` lacks a `min_count`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.