Improve the sketch-based distinct count aggregators in Pinot's star-tree index builder to optimize performance and ensure consistent memory allocation. Implement changes to handle intermediate results as union objects and provide a static upper bound for maximum byte size.

*   Update `DistinctCountCPCSketchValueAggregator`:
    *   Implement `ValueAggregator<Object, Object>`.
    *   Methods `getInitialAggregatedValue`, `applyRawValue`, `applyAggregatedValue`, and `cloneAggregatedValue` must return `Object` (either `CpcUnion` or `CpcSketch`).
    *   Ensure `getMaxAggregatedValueByteSize()` returns the static value 2580 for `lgK=12`, not a dynamic value.

*   Update `DistinctCountThetaSketchValueAggregator`:
    *   Implement `ValueAggregator<Object, Object>`.
    *   Methods `getInitialAggregatedValue`, `applyRawValue`, `applyAggregatedValue`, and `cloneAggregatedValue` must return `Object` (either `org.apache.datasketches.theta.Union` or `org.apache.datasketches.theta.Sketch`).
    *   Ensure `getMaxAggregatedValueByteSize()` returns the `getCurrentBytes()` of a `Union` with `DEFAULT_THETA_SKETCH_NOMINAL_ENTRIES`.

*   Update `IntegerTupleSketchValueAggregator`:
    *   Implement `ValueAggregator<byte[], Object>`.
    *   Methods `getInitialAggregatedValue`, `applyRawValue`, `applyAggregatedValue`, and `cloneAggregatedValue` must return `Object` (either `org.apache.datasketches.tuple.Union` or `org.apache.datasketches.tuple.Sketch<IntegerSummary>`).
    *   Ensure `getMaxAggregatedValueByteSize()` returns 196632 for `nominalEntries = 1 << 14`.

*   Modify `CommonConstants.Helix.DEFAULT_TUPLE_SKETCH_LGK`:
    *   Set to 14, resulting in `nominalEntries = 1 << 14`.

*   Ensure star-tree aggregation tests:
    *   Accept aggregated values as `Object` and convert them to the correct sketch type by checking if they are `Union` or `Sketch`.

*   Implement logic in `applyAggregatedValue` and `applyRawValue`:
    *   For `CpcUnion`, reuse the union if the value is already a `CpcUnion`.
    *   Wrap a `CpcSketch` in a new `CpcUnion` before merging.
    *   For `theta Union`, reuse the union if the value is already a `Union`.
    *   Wrap a `Sketch` in a new `Union` before merging.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.