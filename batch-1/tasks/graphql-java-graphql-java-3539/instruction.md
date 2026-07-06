Implement enhancements to the GraphQL server to protect against malicious introspection queries. Add configurable limits on field count and nesting depth, and improve detection of bad-faith queries that use aliases to repeat introspection fields.

*   Update the `Options` class in `ExecutableNormalizedOperationFactory`:
    *   Implement `maxFieldsCount(int maxFieldsCount)` to return a new `Options` instance with the specified maximum field count.
    *   Ensure `defaultOptions()` returns an `Options` instance with `maxFieldsCount` set to `Integer.MAX_VALUE`.
    *   Implement `getMaxFieldsCount()` to return the configured maximum field count.

*   Modify `ExecutableNormalizedOperationFactory`:
    *   In `createExecutableNormalizedOperationWithRawVariables`, throw `AbortExecutionException` with the message 'Maximum field count exceeded. <actual> > <limit>' if the field count exceeds the configured limit.
    *   Ensure the standard introspection query produces exactly 189 `ExecutableNormalizedFields`.
    *   Test that setting `maxFieldsCount` to 188 throws an `AbortExecutionException` with the message 'Maximum field count exceeded. 189 > 188'. Setting it to 189 or higher should not throw an exception.

*   Enhance `ExecutableNormalizedOperation`:
    *   Implement `getOperationFieldCount()` to return the total number of `ExecutableNormalizedFields`.
    *   Implement `getOperationDepth()` to return the maximum nesting depth of the operation.

*   Update `GoodFaithIntrospection`:
    *   Declare `GOOD_FAITH_MAX_FIELDS_COUNT` as 500.
    *   Declare `GOOD_FAITH_MAX_DEPTH_COUNT` as 20.
    *   Ensure the standard introspection query has a field count below `GOOD_FAITH_MAX_FIELDS_COUNT` and a depth below `GOOD_FAITH_MAX_DEPTH_COUNT`.

*   Improve bad-faith introspection detection:
    *   Flag queries using multiple aliases for the same introspection root field as bad faith.
    *   Ensure deeply nested queries (depth 10 or more) throw an `AbortExecutionException` with null execution data. For smaller depths (e.g., 2) that violate good-faith limits, raise a `BadFaithIntrospectionError`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.