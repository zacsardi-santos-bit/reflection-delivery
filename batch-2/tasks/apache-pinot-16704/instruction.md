Implement a specialized sum aggregation function for integer columns in Pinot that avoids converting integers to floating-point numbers. Ensure it handles null values correctly based on the null handling mode.

*   Update the aggregation function type registry:
    *   Add 'SUMINT' as a valid function type name in a case-insensitive manner.
    *   Ensure 'sumint', 'SUMINT', 'SuMInT', and similar variants resolve to the SUMINT type constant.

*   Implement the SumIntAggregationFunction class:
    *   Location: `pinot-core/src/main/java/org/apache/pinot/core/query/aggregation/function/SumIntAggregationFunction.java`
    *   Extend NullableSingleInputAggregationFunction<Long, Long>.
    *   Constructor: `SumIntAggregationFunction(List<ExpressionContext> arguments, boolean nullHandlingEnabled)`.
    *   Method `getType()` should return AggregationFunctionType.SUMINT.
    *   Method `getIntermediateResultColumnType()` should return DataSchema.ColumnDataType.LONG.
    *   Method `getFinalResultColumnType()` should return DataSchema.ColumnDataType.LONG.
    *   Handle null values:
        *   When null handling is disabled, include all stored values in the sum, treating nulls as the column's stored default null value. Return 0 if all positions are null and no prior partial result exists.
        *   When null handling is enabled, exclude null values from the sum. Return null if all values in a group are null.
    *   Support plain aggregation queries, group-by single-value (SV) queries, and group-by multi-value (MV) queries with consistent null handling.

*   Update the AggregationFunctionFactory:
    *   Location: `pinot-core/src/main/java/org/apache/pinot/core/query/aggregation/function/AggregationFunctionFactory.java`
    *   Extend the existing switch statement in the `getAggregationFunction` method to include a case for AggregationFunctionType.SUMINT.
    *   Return a new instance of SumIntAggregationFunction with the provided arguments and nullHandlingEnabled flag.

*   Add a new enum constant to AggregationFunctionType:
    *   Location: `pinot-segment-spi/src/main/java/org/apache/pinot/segment/spi/AggregationFunctionType.java`
    *   Name: SUMINT
    *   String value: "sumInt"
    *   SQL type: BIGINT for both intermediate and final results.
    *   Ensure the static method `getAggregationFunctionType(String)` resolves "SUMINT" case-insensitively to this constant.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.