Implement support for population variance and sample variance as SQL aggregate functions in the Beam SQL extension. Ensure these functions work correctly for both integer and floating-point columns, supporting distributed computation with partial accumulators.

*   Implement the `VarianceAccumulator` class:
    *   Create a static factory method `newVarianceAccumulator(BigDecimal variance, BigDecimal count, BigDecimal sum)` to store and expose variance, count, and sum values.
    *   Provide a static `EMPTY` constant and a static `ofZeroElements()` method representing an accumulator with variance=0, count=0, and sum=0.
    *   Implement `ofSingleElement(BigDecimal value)` to return an accumulator with variance=0, count=1, and sum=value.
    *   Implement `combineWith(VarianceAccumulator other)` to combine two accumulators using the parallel variance formula. Ensure commutativity.
    *   Ensure combining with `EMPTY` returns the non-empty operand; combining `EMPTY` with `EMPTY` returns `EMPTY`.
    *   Use the formula: `combined_variance = var(x) + var(y) + m/(n*(m+n)) * (sum(x)*n/m - sum(y))^2`, where m and n are the respective counts.

*   Implement the `BigDecimalConverter` class:
    *   Implement `forSqlType(SqlTypeName typeName)` to return a non-null `SerializableFunction<BigDecimal, ? extends Number>` for SQL types: TINYINT, SMALLINT, INTEGER, BIGINT, FLOAT, DOUBLE, and DECIMAL.
    *   Ensure `forSqlType` throws `UnsupportedOperationException` for unsupported types like ARRAY.

*   Implement the `VarianceFn` class:
    *   Implement `newPopulation(SerializableFunction<BigDecimal, ? extends Number> converter)` and `newSample(SerializableFunction<BigDecimal, ? extends Number> converter)` to create variance combine functions.
    *   Ensure `createAccumulator()` returns `VarianceAccumulator.EMPTY`.
    *   Implement `addInput(VarianceAccumulator accumulator, BigDecimal input)` to return the accumulator unchanged if input is null, or incorporate the value into the accumulator.
    *   Implement `getAccumulatorCoder(CoderRegistry, Coder<?>)` to return a non-null coder for `VarianceAccumulator`.
    *   Implement `extractOutput(VarianceAccumulator accumulator)`:
        *   For population variance, divide `accumulator.variance()` by `accumulator.count()` and apply the converter.
        *   For sample variance, divide `accumulator.variance()` by `(accumulator.count() - 1)` and apply the converter.

*   Ensure SQL integration:
    *   Support `VAR_POP(column)` and `VAR_SAMP(column)` SQL aggregate functions in `BeamSql.query()`.
    *   For the dataset {1.0, 4.0, 7.0, 13.0, 5.0, 10.0, 17.0}, ensure:
        *   `VAR_POP` on double column returns approximately 26.40816326 (within 1e-7 precision).
        *   `VAR_POP` on integer column returns 26.
        *   `VAR_SAMP` on double column returns approximately 30.80952381 (within 1e-7 precision).
        *   `VAR_SAMP` on integer column returns 30.

*   Implement `BeamRecordAsserts`:
    *   `matchesScalar(int expected)` must verify an iterable contains exactly one `BeamRecord` whose first field equals the expected integer value.
    *   `matchesScalar(double expected, double delta)` must verify a single `BeamRecord`'s first double field equals the expected value within the given delta.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.