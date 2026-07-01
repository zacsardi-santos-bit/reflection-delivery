Implement a method to apply a user-defined function to each group of rows in a grouped dataframe. This function should allow custom computations on the entire group and return a new dataframe with the results.

*   Update the `GroupedDataFrame` class in `daft/dataframe/dataframe.py`:
    *   Add a `map_groups(udf: Expression) -> "DataFrame"` method.
    *   Ensure `map_groups` accepts a user-defined function (UDF) and applies it independently to each group of rows.
    *   Return a new DataFrame containing:
        *   Group key columns.
        *   A result column produced by the UDF.
    *   Name the UDF result column after the first input expression passed to the UDF.
    *   Repeat (broadcast) group key values in the output if the UDF returns multiple rows for a group.
    *   Support single-key and multi-key groupby operations.
    *   Accept compound expressions as inputs to the UDF, including column aliases and arithmetic operations.

*   Ensure `map_groups` works correctly when:
    *   The dataframe contains only a single group.
    *   Grouping is performed on multiple key columns.

*   Update the cookbook test data loader to include the 'Closed Date' column for UDF-based group operations over date range data.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.