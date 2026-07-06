Implement efficient timestamp handling in the badger storage backend for Jaeger by centralizing timestamp computations and aligning data types. Ensure that timestamps are computed once per span write and reused across all relevant operations, and update data structures to use consistent unsigned integer types.

*   Update the `CacheStore` struct:
    *   Modify the `Update` method in `plugin/storage/badger/spanstore/cache.go` to accept parameters: `service` (string), `operation` (string), and `expireTime` (uint64). Ensure `expireTime` is passed in as a Unix timestamp and not computed internally.
    *   Change the `services` field to `map[string]uint64` and the `operations` field to `map[string]map[string]uint64` in `plugin/storage/badger/spanstore/cache.go`. Use `uint64` for all timestamp values.

*   Modify function signatures:
    *   Update `createTraceKV` in `plugin/storage/badger/spanstore/writer.go` to accept `startTime` as a `uint64`, representing epoch microseconds. Use this parameter directly without internal computation.
    *   Change `createIndexKey` in `plugin/storage/badger/spanstore/writer.go` to accept `startTime` as a `uint64` (epoch microseconds). Embed this timestamp directly into the index key using big-endian encoding.

*   Ensure trace query results:
    *   When `FindTraces` returns 6 results from an index-based query, order them from newest to oldest. The first result should have the highest `TraceID.Low` value (56) and the sixth result should have `TraceID.Low` of 51, reflecting correct big-endian byte ordering.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.