## Description

There are two related issues affecting group-by aggregation queries in Pinot.

**Bug: Array index out-of-bounds crash in group-by queries**

When a group-by query is executed with a configured limit on the number of groups, and that limit is set to a value smaller than the actual number of distinct groups in the data, the query crashes with an array index out-of-bounds error instead of returning a trimmed result. This is especially problematic when using dictionary-based group key generation, where both single-key and multi-key group-by queries are affected. The expected behavior is that the query should gracefully return at most the configured number of groups without throwing an error.

**Feature: Configurable trim size for the multi-stage aggregation operator**

The multi-stage query engine's final aggregation stage currently provides no mechanism to control how many groups are returned as output. There is no query option or per-query hint to specify a trim size at the aggregation level. Users need the ability to limit intermediate aggregation results so they can tune memory usage and control result sizes. The trim size should be settable via a query option, and a per-query hint should be able to override the option value.

## Expected Behavior

- Group-by queries with a group limit smaller than the actual number of distinct groups should return a trimmed result without crashing.
- Users should be able to set a trim size for the multi-stage aggregation stage via a query option.
- A per-query hint for the aggregation trim size should take priority over the query option.
- When no trim size is configured, trimming should be effectively disabled (no limit applied).

## Why This Matters

The crash makes it impossible to use small group limits as a way to bound query resource usage, and the missing trim-size control makes it harder to tune multi-stage query performance.
