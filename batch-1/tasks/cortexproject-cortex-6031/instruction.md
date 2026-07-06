Implement native histogram support in the Cortex querier layer to ensure correct querying of histogram data from block storage. Update the batch iterator, test utilities, and test infrastructure to handle both integer and float histogram data types, ensuring proper conversion, deduplication, and resource limit enforcement.

*   Update the `AtFloatHistogram` method in `pkg/querier/batch/batch.go`:
    *   Handle native histograms by converting them to float histograms and returning the correct timestamp.
*   Modify `GenerateChunk` in `pkg/util/test_util.go`:
    *   Accept additional labels as a variadic parameter.
    *   Default to 'foo' as the metric name if not provided.
*   Remove `FromQueryResponse` from `pkg/ingester/client/compat.go`.
*   Simplify `NewMockStoreQueryable` in `pkg/querier/querier_test.go`:
    *   Remove the `Config` parameter and use `getChunksIteratorFunction(Config{})`.
*   Ensure the blocks store querier correctly queries and returns native histogram series:
    *   Handle single and multiple series from single and multiple gateways.
    *   Deduplicate and merge overlapping series.
*   Apply all resource limit checks to histogram chunks:
    *   Enforce limits on chunks per query, chunk bytes, series, and data bytes.
*   Update `TestBlocksStoreQuerier_PromQLExecution` to test all chunk encodings:
    *   Validate results for XOR, native histogram, and native float histogram encodings.
*   Run the full querier test suite across all chunk encodings:
    *   Update the `query` struct in `querier_test.go` to include `encodings` and `expectedFunc`.
*   Support new histogram-specific PromQL queries in tests:
    *   Test `histogram_sum(foo)` and `histogram_count(foo)` with histogram encodings.
*   Expose functions in `pkg/util/histogram`:
    *   `GenerateTestHistogram(i int)` and `GenerateTestFloatHistogram(i int)` for predictable test values.
*   Implement `makeMockChunks` in `pkg/querier/chunk_store_queryable_test.go`:
    *   Create chunks with additional labels.
*   Update `testRangeQuery` in `pkg/querier/querier_test.go`:
    *   Accept an `enc` parameter and use `q.expectedFunc` for validation.
*   Update `mockSeriesResponse` in `pkg/querier/blocks_store_queryable_test.go`:
    *   Use explicit slice parameters for samples and histograms.
*   Add fields to `valueResult` struct in `pkg/querier/blocks_store_queryable_test.go`:
    *   Include `h` and `fh` for expected histogram values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.