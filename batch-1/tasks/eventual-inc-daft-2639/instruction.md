Update the documentation example in the `approx_percentiles` method to reflect the correct computed output. Modify the tests for logarithm and exponential operations to use approximate floating-point comparisons instead of exact equality.

*   In `daft/expressions/expressions.py`:
    *   Update the docstring example for the `approx_percentiles` method:
        *   Change the displayed computed approximate median value to `2.9742334234767163` for the input `[1, 2, 3, 4, 5, None]` at the 0.5 percentile.
        *   Ensure all other table formatting, column headers, and column types remain unchanged.

*   For tests involving logarithm and exponential operations:
    *   Modify tests to use approximate floating-point comparisons:
        *   For logarithm operations (log with arbitrary base) on numeric columns:
            *   Use approximate comparisons for non-None values.
            *   Ensure None inputs still produce None outputs.
        *   For natural logarithm operations on numeric columns:
            *   Use approximate comparisons for non-None values.
            *   Ensure None inputs still produce None outputs.
        *   For exponential operations on numeric columns:
            *   Use approximate comparisons for values close to:
                *   `1.1051709180756477` and `1.010050167084168` for inputs `0.1` and `0.01`.
                *   `2.718281828459045` and `22026.465794806718` for inputs `1` and `10`.
            *   Ensure None inputs still produce None outputs.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.