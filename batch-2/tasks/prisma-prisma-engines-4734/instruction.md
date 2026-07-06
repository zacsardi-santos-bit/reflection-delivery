Implement a fix for the query engine to correctly order records when using nested relation paths that span two or more levels deep, especially when any part of the relation chain involves a many-to-many relationship. Ensure that ordering by aggregate counts or scalar field values on deeply nested models works correctly.

*   Update the query engine to handle ordering of related records using nested relation paths that span two or more hops.
    *   Ensure records with fewer nested items appear first in ascending order and those with more appear first in descending order.
*   Ensure multi-hop ordering works correctly when any intermediate relation is a many-to-many relation.
    *   Correctly traverse both one-to-many and many-to-many relations mixed in the path.
*   Implement ordering by a scalar field value on models that are 2+ relation hops away.
    *   Ensure correct ascending or descending order based on the distant field.
*   Implement ordering by the count of a one-to-many relation at the end of a 2+ hop path.
    *   Ensure correct sorting in both ascending and descending directions.
*   Implement ordering by the count of a many-to-many relation at the end of a 2+ hop path.
    *   Ensure correct sorting in both ascending and descending directions.
*   Modify the `OrderByToManyAggregation` type:
    *   Add `intermediary_hops` method in `query-engine/query-structure/src/order_by.rs`:
        *   Signature: `intermediary_hops(&self) -> &[OrderByHop]`
        *   Returns all hops except the final aggregation hop.
    *   Add `aggregation_hop` method in `query-engine/query-structure/src/order_by.rs`:
        *   Signature: `aggregation_hop(&self) -> &OrderByHop`
        *   Returns the last hop in the order-by path.
*   Update the SQL select builder:
    *   Implement `first_hop_linking_fields` function in `query-engine/connectors/sql-query-connector/src/query_builder/select/mod.rs`:
        *   Signature: `first_hop_linking_fields(hops: &[OrderByHop]) -> Vec<ScalarFieldRef>`
        *   Returns scalar linking fields of the first hop for JOIN operations.
*   Ensure the order-by join computation:
    *   Uses `intermediary_hops()` for the intermediate JOIN chain.
    *   Uses `aggregation_hop()` for the final aggregation JOIN.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.